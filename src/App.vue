<template>
	<div class="p-4">
		<h1>Parent</h1>

		<NewTable :headers="headers1" :rows="rows1" @addRow="onAddRow(rows1)" />
		<NewTable :headers="headers2" :rows="rows2" @addRow="onAddRow(rows2)" />
		<NewTable :headers="headers3" :rows="rows3" @addRow="onAddRow(rows3)" />
		<NewTable :headers="headers4" :rows="rows4" @addRow="onAddRow(rows4)" />
		<NewTable :headers="headers5" :rows="rows5" @addRow="onAddRow(rows5)" />
		<NewTable :headers="headers6" :rows="rows5" @addRow="onAddRow(rows6)" />

		<!-- <HelloWorld
			:tableData="ReceivingFiatFromCustomer"
			:tableName="'ReceivingFiatFromCustomer'"
		/> -->
		<!-- @updateDataTable="updateDataTable" -->
		<!-- <MyExchangeTable /> -->
		<!-- <HelloWorld
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
		/> -->

		<button class="primary" @click="exportToExcel">export to excel</button>
	</div>
</template>

<script setup>
import HelloWorld from './components/EtitTable.vue';
import bigDecimal from 'js-big-decimal';
import NewTable from './components/NewTable.vue';
import MyExchangeTable from './components/MyExchangeTable.vue';
import * as XLSX from 'xlsx-js-style';
import { saveAs } from 'file-saver';
import { reactive, ref } from 'vue';
// Допоміжна функція для обчислення суми: приймає рядок і масив індексів, сума яких потрібна
// function computeSum(rows1, row, indices, rowIndex, colIndex) {
// 	if (!indices || !Array.isArray(indices)) return 0;
// 	const res = indices.reduce((acc, idx) => acc + Number(row[idx] || 0), 0);
// 	rows1.value[rowIndex][colIndex] = res;
// 	return res;
// }
// function computeSumDouble(rows1, row, indices, rowIndex, colIndex) {
// 	if (!indices || !Array.isArray(indices)) return 0;
// 	const res = indices.reduce((acc, idx) => acc + Number(row[idx] || 0), 0);
// 	rows1.value[rowIndex][colIndex] = res * 2;
// 	return res * 2;
// }

function calculateRateFromPlnToEur(rows1, row, indices, rowIndex, colIndex) {
	if (!indices || !Array.isArray(indices)) return 0;

	if (!row[indices[1]]) return 0;

	const dividedBD = new bigDecimal(row[indices[0]]);
	const divisorBD = new bigDecimal(row[indices[1]]);

	const res = dividedBD.divide(divisorBD, 5).getValue(); // Ділення з точністю до 5 знаків після коми
	rows1.value[rowIndex][colIndex] = res;
	return res;
}
// subtractingPercentageFromAmount
function subtractingPercentageFromAmount(
	rows,
	row,
	indices,
	rowIndex,
	colIndex
) {
	'use strict';
	const numBD = new bigDecimal(row[indices[0]]);
	const percentBD = new bigDecimal(row[indices[1]]);
	const commission = numBD.multiply(percentBD).divide(new bigDecimal(100), 10); // multiply множення а потім ділення
	const res = numBD.subtract(commission).getValue(); // Віднімання комісії
	rows.value[rowIndex][colIndex] = res;
	return res;
}

function calculateMultiplicationByPurely(
	rows,
	row,
	indices,
	rowIndex,
	colIndex
) {
	const num = new bigDecimal(row[indices[0]]);
	const percent = new bigDecimal(row[indices[1]]);

	const res = num.multiply(percent).getValue();
	rows.value[rowIndex][colIndex] = res;
	return res;
}

function calculateSubtract(rows, row, indices, rowIndex, colIndex) {
	const num1BD = new bigDecimal(row[indices[0]]);
	const num2BD = new bigDecimal(row[indices[1]]);
	const res = num1BD.subtract(num2BD).getValue(); // Віднімання
	rows.value[rowIndex][colIndex] = res;
	return res;
}

const headers1 = reactive([
	{
		header_title: 'Fiat Acceptance from Client',
		type: 'select',
		options: ['Bank transfer', 'Cash', 'Voucher'],
		disabled: false,
	},
	{
		header_title: 'Date',
		type: 'date',
		disabled: false,
	},
	{
		header_title: 'Transaction Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Bank Name / Transaction Type / Cash Voucher',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Deposited Currency Type',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Deposited Currency Amount',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Client Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Client Type (Individual/Business)',
		type: 'select',
		options: ['Individual', 'Business'],
		disabled: false,
	},
	{ header_title: 'Client Name', type: 'text', disabled: false },
	{
		header_title: 'Exchange Rate to Euro / Euro Rate',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Euro Amount from Client',
		type: 'calculateRateFromPlnToEur',
		formula: (row, rowIndex, colIndex) =>
			calculateRateFromPlnToEur(rows1, row, [5, 9], rowIndex, colIndex),
		independent_on: [5, 9],
		disabled: true,
	},
	{ header_title: 'Commission %', type: 'number', disabled: false },
	{
		header_title: 'Euro Sold After Commission',
		type: 'calculateRateFromPlnToEur',
		formula: (row, rowIndex, colIndex) =>
			subtractingPercentageFromAmount(rows1, row, [10, 11], rowIndex, colIndex),
		independent_on: [10, 11],
		disabled: true,
	},
	{ header_title: 'Sold Crypto Type', type: 'text', disabled: false },
	{ header_title: 'Crypto Rate from Wanda', type: 'number', disabled: false },
	{
		header_title: 'Sold Crypto Amount',
		type: 'calculateMultiplicationByPurely',
		formula: (row, rowIndex, colIndex) =>
			calculateMultiplicationByPurely(rows1, row, [12, 14], rowIndex, colIndex),
		independent_on: [12, 14],
		disabled: true,
	},
	{
		header_title: 'Wallet Number',
		type: 'string',
		disabled: false,
	},
	{
		header_title: 'Profit',
		type: 'profit',
		formula: (row, rowIndex, colIndex) =>
			calculateSubtract(rows1, row, [10, 12], rowIndex, colIndex),
		independent_on: [10, 12],
		disabled: true,
	},
]);

const rows1 = ref([
	[
		'Bank transfer',
		'2025-03-07',
		'№12345',
		'PKO',
		'PLN',
		'100',
		'----',
		'Business',
		'Client Name',
		4.14,
		0,
		1,
		0,
		'USDT',
		1.04,
		0,
		'wallet',
		0,
	],
]);
const headers2 = reactive([
	{
		header_title: 'Acceptance of Crypto from a customer',
		type: 'select',
		options: ['Crypto transfer'],
		disabled: false,
	},
	{
		header_title: 'Date',
		type: 'date',
		disabled: false,
	},
	{
		header_title: 'Transaction Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Number of finalists',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Type of crypto deposited',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Amount of crypto deposited',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Customer registration number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Customer type',
		type: 'select',
		options: ['Individual', 'Business'],
		disabled: false,
	},
	{
		header_title: 'Client Name',
		type: 'text',
		disabled: false,
	},
	{
		header_title: "Wanda's exchange rate on the euro",
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Amount of Euros from the customer',
		type: 'calculateRateFromPlnToEur',
		formula: (row, rowIndex, colIndex) =>
			calculateMultiplicationByPurely(rows2, row, [5, 9], rowIndex, colIndex),
		independent_on: [5, 9],
		disabled: true,
	},
	{
		header_title: 'Commission %',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Euro Sold After Commission',
		type: 'subtractingPercentageFromAmount',
		formula: (row, rowIndex, colIndex) =>
			subtractingPercentageFromAmount(rows2, row, [10, 11], rowIndex, colIndex),
		independent_on: [10, 11],
		disabled: true,
	},
	{
		header_title: 'Type of final operation for the customer',
		type: 'text',
		disabled: false,
	},
	{
		header_title: "Customer's target currency",
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Target currency exchange rate',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Amount of target currency',
		type: 'calculateRateFromPlnToEur',
		formula: (row, rowIndex, colIndex) =>
			calculateMultiplicationByPurely(rows2, row, [12, 15], rowIndex, colIndex),
		independent_on: [12, 15],
		disabled: true,
	},
	{
		header_title: 'Customer account number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Profit',
		type: 'profit',
		formula: (row, rowIndex, colIndex) =>
			calculateSubtract(rows2, row, [10, 12], rowIndex, colIndex),
		independent_on: [10, 12],
		disabled: true,
	},
]);

const rows2 = ref([
	[
		'Crypto transfer',
		'2025-03-07',
		'№12345',
		'Number of finalists',
		'USDT',
		10000,
		'-',
		'Business',
		'Client Name',
		0.95,
		0,
		1,
		0,
		'Transfer',
		'PLN',
		4.14,
		'#123',
		0,
	],
]);

const headers3 = reactive([
	{
		header_title: 'Adopt your own Fiat',
		type: 'select',
		options: ['Bank transfer'],
		disabled: false,
	},
	{
		header_title: 'Date',
		type: 'date',
		disabled: false,
	},
	{
		header_title: 'Transaction Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Bank name/ type of operation/cash',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Type of currency deposited',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Amount of currency deposited',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Customer registration number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Origin / name of the exchange office',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Data of the depositor',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Target place of deposit',
		type: 'text',
		disabled: false,
	},
]);

const rows3 = ref([
	[
		'Bank transfer',
		'2025-03-07',
		'#123',
		'Bank transfer',
		'PLN',
		0,
		'#123',
		'Bank Reference',
		'Data of the depositor',
		'Target place of deposit',
	],
]);

const headers4 = reactive([
	{
		header_title: 'Account balance',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'operation number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'type of operation',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Currency type',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Amount of currency',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Place of appreciation(san, wanda, checkout, etc.).',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Course (informational)',
		type: 'number',
		disabled: false,
	},
]);

const rows4 = ref([
	[0, '#123', 'type', 'currency', 0, 'Place of appreciation', 0],
]);

const headers5 = reactive([
	{
		header_title: 'Currency conversion of your own Fiat',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Date',
		type: 'date',
		disabled: false,
	},
	{
		header_title: 'Transaction Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Type of initial currency',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Amount of initial currency',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Target currency type',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Target currency exchange rate',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Amount of target currency',
		type: 'calculateRateFromPlnToEur',
		formula: (row, rowIndex, colIndex) =>
			calculateRateFromPlnToEur(rows5, row, [4, 6], rowIndex, colIndex),
		independent_on: [4, 6],
		disabled: true,
	},
	{
		header_title: 'Supply location',
		type: 'text',
		disabled: false,
	},
]);

const rows5 = ref([
	['Currency', '2025-03-07', '#123', 'PLN', 0, 'EUR', 4.14, 0, 'Wanda'],
]);

const headers6 = reactive([
	{
		header_title: 'Bringing out your own for Fiat',
		type: 'select',
		options: ['Bank transfer'],
		disabled: false,
	},
	{
		header_title: 'Date',
		type: 'date',
		disabled: false,
	},
	{
		header_title: 'Transaction Reference Number',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Name of bank/type of operation',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Type of currency paid out',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Amount of currency paid out',
		type: 'number',
		disabled: false,
	},
	{
		header_title: 'Rate of currency paid out',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Target payout location',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Origin/name of the exchange office',
		type: 'text',
		disabled: false,
	},
	{
		header_title: 'Data of the paying agent',
		type: 'text',
		disabled: false,
	},
]);

const rows6 = ref([
	[
		'Bank transfer',
		'2025-03-07',
		'#123',
		'Bank name',
		'EUR',
		0,
		4.14,
		'Cash',
		'Wanda',
		'M Głuchowska',
	],
]);

function onAddRow(arr) {
	arr.push([...arr[arr.length - 1]]);
}

const exportToExcel = () => {
	// Перший заголовок та дані
	const headersArr = headers1.map((item) => item.header_title);
	const data = rows1.value.map((row) => [...row]);

	// Другий заголовок та дані
	const headersArr1 = headers2.map((item) => item.header_title);
	const data1 = rows2.value.map((row) => [...row]);

	// Об'єднуємо дані: перший блок, порожній рядок, другий блок
	const formattedData = [
		headersArr,
		...data,
		[], // порожній рядок як роздільник
		headersArr1,
		...data1,
	];

	// Створюємо робочий лист із форматованими даними
	const ws = XLSX.utils.aoa_to_sheet(formattedData);

	// Встановлюємо мінімальну ширину стовпців
	const minWidth = 20;
	const colWidths = headersArr.map(() => ({ wch: minWidth }));
	ws['!cols'] = colWidths;

	// Отримуємо діапазон використання листа
	const range = XLSX.utils.decode_range(ws['!ref']);

	// Задаємо висоту для заголовкових рядків (наприклад, 45 пунктів)
	// Створюємо або оновлюємо масив рядків ws['!rows']
	ws['!rows'] = ws['!rows'] || [];
	// Перший заголовковий рядок — індекс 0
	ws['!rows'][0] = { hpt: 45, customHeight: true };
	// Другий заголовковий рядок — знаходимо його індекс: 1 (headersArr) + data.length + 1 (порожній рядок)
	const secondHeaderRow = data.length + 2;
	ws['!rows'][secondHeaderRow] = { hpt: 40, customHeight: true };

	// Функція для стилізації заголовкових рядків
	const styleHeaderRow = (r) => {
		for (let C = range.s.c; C <= range.e.c; ++C) {
			const cellAddress = XLSX.utils.encode_cell({ c: C, r });
			if (!ws[cellAddress]) continue;
			ws[cellAddress].s = ws[cellAddress].s || {};
			// Задаємо жирний шрифт, темний колір, вирівнювання та бордери
			ws[cellAddress].s.font = {
				bold: true,
				color: { rgb: 'FF000000' },
				name: 'Arial',
				size: 14,
			};
			ws[cellAddress].s.alignment = {
				horizontal: 'center',
				vertical: 'center',
				wrapText: true,
				shrinkToFit: true,
			};
			ws[cellAddress].s.border = {
				top: { style: 'medium', color: { rgb: 'FF000000' } },
				bottom: { style: 'medium', color: { rgb: 'FF000000' } },
				left: { style: 'medium', color: { rgb: 'FF000000' } },
				right: { style: 'medium', color: { rgb: 'FF000000' } },
			};
		}
	};

	// Стилізуємо перший заголовковий рядок (рядок 0)
	styleHeaderRow(0);
	// Стилізуємо другий заголовковий рядок (індекс secondHeaderRow)
	styleHeaderRow(secondHeaderRow);

	// Створюємо книгу та додаємо лист
	const wb = XLSX.utils.book_new();
	XLSX.utils.book_append_sheet(wb, ws, 'TABLE_NAME');

	// Генеруємо файл Excel із увімкненими стилями (параметр cellStyles: true)
	const excelBuffer = XLSX.write(wb, {
		bookType: 'xlsx',
		type: 'array',
		cellStyles: true,
	});
	const blob = new Blob([excelBuffer], { type: 'application/octet-stream' });
	saveAs(blob, 'TABLE_NAME.xlsx');
};

const setDate = () => new Date().toLocaleDateString();

const calculateRateFromPlnToEurAld = (divided, divisor) => {
	const dividedBD = new bigDecimal(divided);
	const divisorBD = new bigDecimal(divisor);
	const res = dividedBD.divide(divisorBD, 5).getValue(); // Ділення з точністю до 10 знаків після коми
	return res;
};

const calculateRateFromEurToPln = (divided, divisor) => {
	const dividedBD = new bigDecimal(divided);
	const divisorBD = new bigDecimal(divisor);
	return dividedBD.multiply(divisorBD).getValue(); // множення
};

const calculateCommission2 = (num, percent) => {
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

// const ReceivingFiatFromCustomer = reactive([
// 	{
// 		header: 'Fiat Acceptance from Client',
// 		id: 1,
// 		value: 'Przelew bankowy',
// 		noEditable: false,
// 		input_type: 'select',
// 		select_options: ['Bank transfer', 'Cash', 'Voucher'],
// 		action: null,
// 	},
// 	{
// 		id: 2,
// 		header: 'Date',
// 		value: '10.02.2025',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: setDate, // Функція для @click
// 	},
// 	{
// 		id: 3,
// 		header: 'Transaction Reference Number',
// 		value: '№12345',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 4,
// 		header: 'Bank Name / Transaction Type / Cash Voucher',
// 		value: 'PKO',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 5,
// 		header: 'Deposited Currency Type',
// 		value: 'PLN',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 6,
// 		header: 'Deposited Currency Amount',
// 		value: 10000,
// 		noEditable: false,
// 		input_type: 'number',
// 		action: calculateRateFromPlnToEurAld,
// 		dependsOn: [6, 10],
// 		target: 11,
// 		focus: true,
// 	},
// 	{
// 		id: 7,
// 		header: 'Client Reference Number',
// 		value: '-----',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 8,
// 		header: 'Client Type (Individual/Business)',
// 		value: 'Individual',
// 		noEditable: false,
// 		input_type: 'select',
// 		select_options: ['Individual', 'business'],
// 		action: null,
// 	},
// 	{
// 		id: 9,
// 		header: 'Client Name',
// 		value: 'Borys',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 10,
// 		header: 'Exchange Rate to Euro / Euro Rate',
// 		value: 4.14,
// 		noEditable: false,
// 		input_type: 'number',
// 		action: calculateRateFromPlnToEurAld,
// 		dependsOn: [6, 10],
// 		target: 11,
// 	},
// 	{
// 		id: 11,
// 		header: 'Euro Amount from Client',
// 		value: 0,
// 		noEditable: true,
// 		input_type: 'number',
// 		action: calculateCommission2,
// 		dependsOn: [11, 12],
// 		target: 13,
// 	},
// 	{
// 		id: 12,
// 		header: 'Commission %',
// 		value: 1,
// 		noEditable: false,
// 		input_type: 'number',
// 		action: calculateCommission2,
// 		dependsOn: [11, 12],
// 		target: 13,
// 	},
// 	{
// 		id: 13,
// 		header: 'Euro Sold After Commission',
// 		value: 0,
// 		noEditable: true,
// 		input_type: 'number',
// 		action: null,
// 	},
// 	{
// 		id: 14,
// 		header: 'Sold Crypto Type',
// 		value: 'USDT',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 15,
// 		header: 'Crypto Rate from Wanda',
// 		value: 1.04,
// 		noEditable: false,
// 		input_type: 'number',
// 		action: calculateEuroToUsdt,
// 		dependsOn: [13, 15],
// 		target: 16,
// 	},
// 	{
// 		id: 16,
// 		header: 'Sold Crypto Amount',
// 		value: 0,
// 		noEditable: true,
// 		input_type: 'number',
// 		action: null,
// 	},
// 	{
// 		id: 17,
// 		header: 'Wallet Number',
// 		value: '1e12e1l;m1j21ioud1u01nd02d1nu0',
// 		noEditable: false,
// 		input_type: 'text',
// 		action: null,
// 	},
// 	{
// 		id: 18,
// 		header: 'Profit',
// 		value: '0',
// 		noEditable: true,
// 		input_type: 'number',
// 		action: calculateDifference,
// 		dependsOn: [11, 13],
// 		target: 18,
// 	},
// ]);
</script>

<style lang="css">
.primary {
	margin-top: 100px;
	color: #42b883;
	padding: 20px;
	display: flex;
	align-items: center;
	justify-content: center;
	font-weight: bold;
}
</style>
