<template>
	<div class="p-4">
		<h1>Parent</h1>
		<HelloWorld
			:tableData="mockDataLikeProps"
			:tableName="'ReceivingFiatFromCustomer'"
		/>
		<HelloWorld :tableData="mockDataLikeProps" :tableName="'test'" />
	</div>
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue';
import { reactive } from 'vue';

const setDate = () => new Date().toLocaleDateString();

const calculateRateFromPlnToEur = (divided, divisor) => {
	return divided / divisor;
};

const calculateCommission = (num, percent) => {
	return num - (num * percent) / 100;
};

const calculateEuroToUsdt = (num, percent) => {
	return num * percent;
};
const calculateDifference = (num1, num2) => {
	return num1 - num2;
};
// const headers = ref([
// 	'Fiat Acceptance from Client',
// 	'Date',
// 	'Transaction Reference Number',
// 	'Bank Name / Transaction Type / Cash Voucher',
// 	'Deposited Currency Type',
// 	'Deposited Currency Amount',
// 	'Client Reference Number',
// 	'Client Type (Individual/Business)',
// 	'Client Name',
// 	'Exchange Rate to Euro / Euro Rate',
// 	'Euro Amount from Client',
// 	'Commission %',
// 	'Euro Sold After Commission',
// 	'Sold Crypto Type',
// 	'Crypto Rate from Wanda',
// 	'Sold Crypto Amount',
// 	'Wallet Number',
// 	'Profit',
// ]);

// // Дані таблиці (початкові)
// const tableData = reactive([
// 	[
// 		'Przelew bankowy',
// 		'10.02.2025',
// 		'№12345',
// 		'PKO',
// 		'PLN',
// 		10000,
// 		'-',
// 		'indywidualny',
// 		'Borys',
// 		Number(4.14),
// 		'2415,45893719807',
// 		'1%',
// 		'2391,30584882609',
// 		'USDT',
// 		'1,4',
// 		'2487,0',
// 		'xyz',
// 		'24,2',
// 	],
// ]);

const mockDataLikeProps = reactive([
	{
		id: 1,
		header: 'Fiat Acceptance from Client',
		value: 'Przelew bankowy',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 2,
		header: 'Date',
		value: '10.02.2025',
		noEditable: false,
		input_type: 'text',
		action: setDate, // Функція для @click
	},
	{
		id: 3,
		header: 'Transaction Reference Number',
		value: '№12345',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 4,
		header: 'Bank Name / Transaction Type / Cash Voucher',
		value: 'PKO',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 5,
		header: 'Deposited Currency Type',
		value: 'PLN',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 6,
		header: 'Deposited Currency Amount',
		value: 10000,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromPlnToEur,
		dependsOn: [6, 10],
		target: 11,
	},
	{
		id: 7,
		header: 'Client Reference Number',
		value: '-----',
		noEditable: false,
		input_type: 'string',
		action: null,
	},
	{
		id: 8,
		header: 'Client Type (Individual/Business)',
		value: 'indywidualny',
		noEditable: false,
		input_type: 'checkbox',
		action: null,
	},
	{
		id: 9,
		header: 'Client Name',
		value: 'Borys',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 10,
		header: 'Exchange Rate to Euro / Euro Rate',
		value: 4.14,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromPlnToEur,
		dependsOn: [6, 10],
		target: 11,
	},
	{
		id: 11,
		header: 'Euro Amount from Client',
		value: 0,
		noEditable: true,
		input_type: 'number',
		action: calculateCommission,
		dependsOn: [11, 12],
		target: 13,
	},
	{
		id: 12,
		header: 'Commission %',
		value: 1,
		noEditable: false,
		input_type: 'number',
		action: calculateCommission,
		dependsOn: [11, 12],
		target: 13,
	},
	{
		id: 13,
		header: 'Euro Sold After Commission',
		value: 0,
		noEditable: true,
		input_type: 'number',
		action: null,
	},
	{
		id: 14,
		header: 'Sold Crypto Type',
		value: 'USDT',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 15,
		header: 'Crypto Rate from Wanda',
		value: 1.04,
		noEditable: false,
		input_type: 'number',
		action: calculateEuroToUsdt,
		dependsOn: [13, 15],
		target: 16,
	},
	{
		id: 16,
		header: 'Sold Crypto Amount',
		value: 0,
		noEditable: true,
		input_type: 'number',
		action: null,
	},
	{
		id: 17,
		header: 'Wallet Number',
		value: '1e12e1l;m1j21ioud1u01nd02d1nu0',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 18,
		header: 'Profit',
		value: '0',
		noEditable: true,
		input_type: 'number',
		action: calculateDifference,
		dependsOn: [11, 13],
		target: 18,
	},
	// {
	// 	id: 4,
	// 	header: 'num 1',
	// 	value: 10,
	// 	noEditable: false,
	// 	input_type: 'number',
	// 	action: sumFunction, // Функція для @input
	// 	dependsOn: [3, 4],
	// 	target: 5,
	// },
	// {
	// 	id: 5,
	// 	header: 'num 2',
	// 	value: 20,
	// 	noEditable: false,
	// 	input_type: 'number',
	// 	action: sumFunction, // Функція для @input
	// 	dependsOn: [3, 4],
	// 	target: 5,
	// },
	// {
	// 	id: 5,
	// 	header: 'sum 1 & 2',
	// 	value: 0,
	// 	noEditable: true,
	// 	input_type: 'number',
	// },
]);
</script>
