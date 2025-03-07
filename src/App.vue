<template>
	<div class="p-4">
		<h1>Parent</h1>
		<HelloWorld
			:tableData="ReceivingFiatFromCustomer"
			:tableName="'ReceivingFiatFromCustomer'"
		/>
		<HelloWorld
			:tableData="ReceivingCryptoFromCustomer"
			:tableName="'ReceivingCryptoFromCustomer'"
		/>
		<HelloWorld :tableData="ReceiptOfOwnFiat" :tableName="'ReceiptOfOwnFiat'" />
		<HelloWorld
			:tableData="CurrencyConversionOfYourOwnFiat"
			:tableName="'CurrencyConversionOfYourOwnFiat'"
		/>
		<HelloWorld
			:tableData="BringingOutYourOwnForFiat"
			:tableName="'BringingOutYourOwnForFiat'"
		/>
	</div>
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue';
import bigDecimal from 'js-big-decimal';
import { reactive } from 'vue';

const setDate = () => new Date().toLocaleDateString();

const calculateRateFromPlnToEur = (divided, divisor) => {
	const dividedBD = new bigDecimal(divided);
	const divisorBD = new bigDecimal(divisor);
	return dividedBD.divide(divisorBD, 5).getValue(); // Ділення з точністю до 10 знаків після коми
};

const calculateRateFromEurToPln = (divided, divisor) => {
	const dividedBD = new bigDecimal(divided);
	const divisorBD = new bigDecimal(divisor);
	return dividedBD.multiply(divisorBD).getValue(); // множення
};

const calculateCommission = (num, percent) => {
	const numBD = new bigDecimal(num);
	const percentBD = new bigDecimal(percent);
	const commission = numBD.multiply(percentBD).divide(new bigDecimal(100), 10);
	return numBD.subtract(commission).getValue(); // Віднімання комісії
};

const calculateEuroToUsdt = (num, percent) => {
	const numBD = new bigDecimal(num);
	const percentBD = new bigDecimal(percent);
	return numBD.multiply(percentBD).getValue(); // Множення
};

const calculateUsdtToEuro = (num, percent) => {
	if (percent === 0) return 0;
	const numBD = new bigDecimal(num);
	const percentBD = new bigDecimal(percent);
	return numBD.multiply(percentBD).getValue(); // Ділення
};

const calculateDifference = (num1, num2) => {
	const num1BD = new bigDecimal(num1);
	const num2BD = new bigDecimal(num2);
	return num1BD.subtract(num2BD).getValue(); // Віднімання
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

const ReceivingFiatFromCustomer = reactive([
	{
		id: 1,
		header: 'Fiat Acceptance from Client',
		value: 'Przelew bankowy',
		noEditable: false,
		input_type: 'select',
		select_options: ['Bank transfer', 'Cash', 'Voucher'],
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
		value: 0,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromPlnToEur,
		dependsOn: [6, 10],
		target: 11,
		focus: true,
	},
	{
		id: 7,
		header: 'Client Reference Number',
		value: '-----',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 8,
		header: 'Client Type (Individual/Business)',
		value: 'Individual',
		noEditable: false,
		input_type: 'select',
		select_options: ['Individual', 'business'],
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
]);
const ReceivingCryptoFromCustomer = reactive([
	{
		id: 1,
		header: 'Acceptance of Crypto from a customer',
		value: 'Crypto transfer',
		noEditable: false,
		input_type: 'select',
		select_options: ['Crypto transfer'],
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
		header: 'Wallet No.',
		value: 'XXX',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 5,
		header: 'Type of crypto deposited',
		value: 'USDT',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 6,
		header: 'Amount of crypto deposited',
		value: 0,
		noEditable: false,
		input_type: 'number',
		action: calculateUsdtToEuro,
		dependsOn: [6, 10],
		target: 11,
		focus: true,
	},
	{
		id: 7,
		header: 'Client Reference Number',
		value: '-----',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 8,
		header: 'Client Type (Individual/Business)',
		value: 'Individual',
		noEditable: false,
		input_type: 'select',
		select_options: ['Individual', 'business'],
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
		header: "Wanda's exchange rate on the euro",
		value: 0.95,
		noEditable: false,
		input_type: 'number',
		action: calculateUsdtToEuro,
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
		action: calculateRateFromEurToPln,
		dependsOn: [13, 17],
		target: 18,
	},
	{
		id: 14,
		header: 'Type of final operation for the customer',
		value: 'Transfer',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 15,
		header: "Customer's target currency",
		value: 'PLN',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 16,
		header: 'Exchange Rate to Euro / Euro Rate',
		value: 4.14,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromEurToPln,
		dependsOn: [13, 16],
		target: 17,
	},
	{
		id: 17,
		header: 'Quantity of target currency',
		value: 0,
		noEditable: true,
		input_type: 'number',
		action: null,
	},
	{
		id: 18,
		header: 'Customer account number',
		value: 'account number',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 19,
		header: 'Euro profit',
		value: 0,
		noEditable: true,
		input_type: 'number',
		action: calculateDifference,
		dependsOn: [11, 13],
		target: 19,
	},
]);
const ReceiptOfOwnFiat = reactive([
	{
		id: 1,
		header: 'Receipt of own Fiat',
		value: 'Bank transfer',
		noEditable: false,
		input_type: 'select',
		select_options: ['Bank transfer'],
		action: null,
	},
	{
		id: 2,
		header: 'Date',
		value: '10.02.2025',
		noEditable: false,
		input_type: 'text',
		action: setDate,
	},
	{
		id: 3,
		header: 'Bank name/type of operation/cash',
		value: '№12345',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 4,
		header: 'Type of currency deposited',
		value: 'PLN',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 5,
		header: 'Quantity of currency deposited	',
		value: 'USDT',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 6,
		header: 'Customer reference number',
		value: '12345',
		noEditable: false,
		input_type: 'text',
		focus: true,
	},
	{
		id: 7,
		header: 'Origin/name of exchange office	',
		value: 'Origin/name',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 8,
		header: "Depositor's data",
		value: 'Data',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 9,
		header: 'Deposit destination',
		value: 'Santander',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
]);
const CurrencyConversionOfYourOwnFiat = reactive([
	{
		id: 1,
		header: 'Currency conversion of your own Fiat',
		value: 'Bank transfer',
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
		action: setDate,
	},
	{
		id: 3,
		header: 'Operation No.',
		value: '№12345',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 4,
		header: 'Initial currency type',
		value: 'PLN',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 5,
		header: 'Amount of initial currency',
		value: 0,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromPlnToEur,
		dependsOn: [5, 7],
		target: 8,
		focus: true,
	},
	{
		id: 6,
		header: 'Target currency type',
		value: 'EUR',
		noEditable: false,
		input_type: 'text',
		focus: true,
	},
	{
		id: 7,
		header: 'Target currency exchange rate',
		value: 4.14,
		noEditable: false,
		input_type: 'number',
		action: calculateRateFromPlnToEur,
		dependsOn: [5, 7],
		target: 8,
		focus: true,
	},
	{
		id: 8,
		header: 'Amount of target currency',
		value: 0,
		noEditable: false,
		input_type: 'number',
		action: null,
	},
	{
		id: 9,
		header: 'Supply location',
		value: 'Wanda',
		noEditable: true,
		input_type: 'text',
		action: null,
	},
]);
const BringingOutYourOwnForFiat = reactive([
	{
		id: 1,
		header: 'Bringing out your own for Fiat',
		value: 'Bank transfer',
		noEditable: false,
		input_type: 'select',
		select_options: ['Bank transfer'],
		action: null,
	},
	{
		id: 2,
		header: 'Date',
		value: '10.02.2025',
		noEditable: false,
		input_type: 'text',
		action: setDate,
	},
	{
		id: 3,
		header: 'Transaction record number',
		value: '№12345',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 4,
		header: 'Name of bank/type of operation',
		value: 'Przelew',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 5,
		header: 'Type of currency paid out',
		value: 0,
		noEditable: false,
		input_type: 'text',
	},
	{
		id: 6,
		header: 'Amount of currency paid out',
		value: 'EUR',
		noEditable: false,
		input_type: 'text',
		focus: true,
	},
	{
		id: 7,
		header: 'Rate of currency paid out',
		value: 4.14,
		noEditable: false,
		input_type: 'number',
		focus: true,
	},
	{
		id: 8,
		header: 'Target payout location',
		value: 'Gotówka',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
	{
		id: 9,
		header: 'Origin/name of the exchange office',
		value: 'Wanda',
		noEditable: true,
		input_type: 'text',
		action: null,
	},
	{
		id: 9,
		header: 'Data of the paying agent',
		value: 'M Głuchowska',
		noEditable: false,
		input_type: 'text',
		action: null,
	},
]);
</script>
