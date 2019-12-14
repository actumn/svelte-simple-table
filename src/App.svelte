<script>
	import Table from './components/Table.svelte';
	import { onMount } from 'svelte';

	let pk = "nccCampaignId";
	let columns = {
		"nccCampaignId": true,
		"customerId": true,
		"name": true,
		"userLock": true,
		"campaignTp": true,
		"periodEndDt": true,
		"periodStartDt": true,
		"deliveryMethod": true,
		"trackingUrl": true,
		"trackingMode": true,
		"usePeriod": true,
		"dailyBudget": true,
		"useDailyBudget": true,
		"totalChargeCost": true,
		"status": true,
		"statusReason": true,
		"expectCost": true,
		"migType": true,
		"delFlag": true,
		"regTm": true,
		"editTm": true,
	};
	let entities = [  
		{  
			"nccCampaignId":"cmp-a001-01-000000000318720",
			"customerId":469757,
			"name":"소재onoff용",
			"userLock":true,
			"campaignTp":"WEB_SITE",
			"deliveryMethod":"ACCELERATED",
			"trackingMode":"TRACKING_DISABLED",
			"usePeriod":false,
			"dailyBudget":70,
			"useDailyBudget":true,
			"totalChargeCost":0,
			"status":"PAUSED",
			"statusReason":"CAMPAIGN_PAUSED",
			"expectCost":77,
			"migType":0,
			"delFlag":false,
			"regTm":"2017-01-17T07:46:09.000Z",
			"editTm":"2019-03-27T02:41:21.000Z"
		},
		{  
			"nccCampaignId":"cmp-a001-01-000000000318751",
			"customerId":469757,
			"name":"캠페인onoff용",
			"userLock":true,
			"campaignTp":"WEB_SITE",
			"deliveryMethod":"ACCELERATED",
			"trackingMode":"TRACKING_DISABLED",
			"usePeriod":true,
			"periodStartDt":"2018-10-18T15:00:00.000Z",
			"periodEndDt":"2018-10-18T15:00:00.000Z",
			"dailyBudget":0,
			"useDailyBudget":false,
			"totalChargeCost":0,
			"status":"PAUSED",
			"statusReason":"CAMPAIGN_PAUSED",
			"expectCost":0,
			"migType":0,
			"delFlag":false,
			"regTm":"2017-01-17T07:59:53.000Z",
			"editTm":"2019-03-27T02:41:21.000Z"
		}
	];

	$: tableColumns = Object.keys(columns).every((col) => columns[col] === false) ? 
		Object.keys(columns) :
		Object.keys(columns).filter(col => columns[col])

	function mark(col) {
		columns[col] = !columns[col];
	}
</script>

<div> 
	<h2>Columns</h2>
	{#each Object.keys(columns) as col}
		<label>
			<input type=checkbox checked='{columns[col]}' on:change={() => mark(col)}>
			{col}
		</label>
	{/each}
</div>
<Table
	pk={pk}
	bind:columns={tableColumns}
	bind:data={entities}
	rowPerPage=30
	>
</Table>
