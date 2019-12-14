<script>
  import { createEventDispatcher } from 'svelte';

  export let pk = '';
  export let columns = [];
  export let data = [];
  export let rowPerPage = 10;

  $: sortBy = pk;
  let sortOrder = 1;
  let filterOptions = [];
  let filterAddState = false;
  let newFilter = {
    key: '',
    op: '',
    value: '',
  };
  let currentPage = 0;


  $: filteredData = data
    .filter((row) => {
      if (filterOptions.length === 0) 
        return true;

      for (let filter of filterOptions) {
        if (filter.op == '=') {
          const rowData = row[filter.key]; 
          if (typeof(rowData) === 'string' && rowData === filter.value) return true
          if (typeof(rowData) === 'number' && rowData === Number(filter.value)) return true;
          if (typeof(rowData) === 'boolean' && rowData === Boolean(filter.value)) return true;
        }

        if (filter.op == '>' && row[filter.key] > filter.value) {
          return true;
        }

        if (filter.op == '<' && row[filter.key] < filter.value) {
          return true;
        }
      }

      return false;
    });
  $: totalPage = Math.floor(filteredData.length / rowPerPage);
  $: rows = filteredData
    .sort((a, b) => {
      if (a[sortBy] > b[sortBy]) return sortOrder;
      else if (a[sortBy] < b[sortBy]) return -sortOrder;
      return 0;
    })
    .slice(currentPage * rowPerPage, (currentPage + 1) * rowPerPage);


  const handleClickCol = (col) => {
    if (col === sortBy) {
      sortOrder = -sortOrder;
    }
    else {
      sortOrder = 1;
    }

    sortBy = col;
  };


  function addFilter() {
    if (newFilter.key === '' || newFilter.op === '', newFilter.value === '') return;

    filterOptions = [
      ...filterOptions,
      {
        key: newFilter.key,
        op: newFilter.op,
        value: newFilter.value.trim(),
      }
    ];
  }

  function removeFilter(rowFilter) {
    filterOptions = filterOptions.filter(f => f !== rowFilter);
  }

  function removeRowMark(row) {
    row.$remove = row.$remove ? !row.$remove : true;
  }

  function removeRows() {
    data = data.filter(d => !d.$remove);
  }

  function exportCSV() {
    console.log('csv');
    let link = document.createElement('a'); 
    let csvData = columns.join(',') + '\n';
    for (let row of filteredData) {
      for (let col of columns) {
        csvData += (row[col] || '') + ',';
      }

      csvData += '\n';
    }

    link.download = "myFirstExample.csv"; 
    link.href = 'data:application/vhd.ms-excel,'+csvData;
    link.click();
  }
</script>

<h3>Filter</h3>
{#each filterOptions as filter, n}
  <div class='filter'>
    {filter.key} {filter.op} {filter.value}
    <button on:click="{() => removeFilter(filter)}">remove</button>
  </div>
{/each}
{#if filterAddState}
  <select
    bind:value={newFilter.key}>
    <option value={undefined}></option>
    {#each columns as col} 
      <option value={col}>{col}</option>
    {/each}
  </select>
  <select
    bind:value={newFilter.op}>
    <option value={undefined}></option>
    <option value='='> &equals; </option>
    <option value='>'> &lt; </option>
    <option value='<'> &gt; </option>
  </select>
  <input type=text
    bind:value={newFilter.value}>
  <button on:click={() => addFilter()}>Add</button>
{/if}
<button on:click={() => filterAddState = !filterAddState}>
  {filterAddState ? 'Close' : 'Add'}
</button>

<h3>Table</h3>
<button on:click={exportCSV}>Export to Csv</button>
<div class='table-wrap'>
  <table class='table'>
    <tr>
      {#each columns as col}
        <th
          class:fixed={col === pk}
          on:click={() => handleClickCol(col)}
        >
          {col}
          {#if sortBy === col}
            { sortOrder === 1 ? '▲' : '▼'}
          {/if}
        </th>
      {/each}
      <th>remove</th>
    </tr>
    {#each rows as row, n (row[pk])}
      <tr>
        {#each columns as col}
        <td
          class:fixed={col === pk}
        >
          {row[col] || ''}
        </td>
        {/each}
        <td>
          <label><input type=checkbox class='checkbox' checked={row.$remove} on:change={() => removeRowMark(row)}></label>
        </td>
      </tr>
    {/each}
  </table>
</div>
<button on:click={() => removeRows()}>remove rows</button>
<div class="page">
  <button on:click={() => currentPage = Math.max(0, currentPage - 1)}>◀</button>
  {currentPage + 1} / {totalPage + 1}
  <button on:click={() => currentPage = Math.min(totalPage, currentPage + 1)}>▶</button>
</div>

<style>
  .checkbox {
    align-self: center
  }

	.filter {
		position: relative;
		line-height: 1.2;
		padding: 0.5em 2.5em 0.5em 2em;
		border-radius: 2px;
		border: 1px solid hsl(240, 8%, 70%);
		background-color:hsl(240, 8%, 93%);
		color: #333;
	}
  .page {
    width: 100%;
  }


  .table-wrap {
    display: block;
    position: relative;
    max-width: 100vw;
    
    /* 100view height - paginator height */
    max-height: calc(100vh - 95px);
    overflow-x: auto;
  }

  .table-wrap .table {
    width: 100%;
    border-collapse: collapse;
  }
  
  .table-wrap .table tr:active td, .table-wrap .table tr:active td {
    background-color: wheat;
  }

  
  .table-wrap .table tr:hover {
    cursor: pointer;
  }

  .table-wrap .table tr:hover td {
    background-color: antiquewhite;
  }

  .table-wrap .table tr:first-child th, .table-wrap .table tr:first-child td {

    position: sticky;
    box-shadow: inset 0 1px 0 gray,
                inset 0 -1px 0 gray;
    top: 0px;
  }

  .table-wrap .table th {
    background-color: #eeeeee;
  }

  .table-wrap .table th, .table-wrap .table td {
    height: 50px;
    min-width: 170px;
    border: 1px solid gray;
  }
</style>
