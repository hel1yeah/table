<template>
	<div class="p-4">
		<h2 class="text-lg font-bold mb-4">Редагована таблиця</h2>

		<!-- Таблиця -->
		<table class="border-collapse border w-full">
			<thead>
				<tr class="bg-gray-200">
					<th v-for="(header, index) in headers" :key="index">
						{{ header }}
					</th>
				</tr>
			</thead>
			<tbody>
				<tr v-for="(row, rowIndex) in tableData" :key="rowIndex">
					<td v-for="(cell, cellIndex) in row" :key="cellIndex">
						<button
							@click="showAlert(rowIndex, cellIndex)"
							v-if="cellIndex === 0"
						>
							{{ tableData[rowIndex][cellIndex] }}
						</button>
						<!-- Якщо це колонка "Сумма мінус % комісії", блокуємо поле та рахуємо значення -->
						<input
							v-else-if="cellIndex === 10"
							:value="amountInEurosFromClient(row, rowIndex, cellIndex)"
							disabled
						/>
						<input
							v-else
							v-model="tableData[rowIndex][cellIndex]"
							:disabled="cell === 'EUR'"
						/>
					</td>
				</tr>
			</tbody>
		</table>

		<!-- Кнопка експорту -->
		<button @click="exportToExcel">Експортувати в Excel</button>
	</div>
</template>

<script setup>
import { ref, reactive } from 'vue';
import * as XLSX from 'xlsx-js-style';
import { saveAs } from 'file-saver';

// Заголовки таблиці
const headers = ref([
	'Fiat Acceptance from Client',
	'Date',
	'Transaction Reference Number',
	'Bank Name / Transaction Type / Cash Voucher',
	'Deposited Currency Type',
	'Deposited Currency Amount',
	'Client Reference Number',
	'Client Type (Individual/Business)',
	'Client Name',
	'Exchange Rate to Euro / Euro Rate ',
	'Euro Amount from Client',
	'Commission %',
	'Euro Sold After Commission',
	'Sold Crypto Type',
	'Crypto Rate from Wanda',
	'Sold Crypto Amount',
	'Wallet Number',
	'Profit',
]);

// Дані таблиці (початкові)
const tableData = reactive([
	[
		'Przelew bankowy',
		'10.02.2025',
		'№12345',
		'PKO',
		'PLN',
		10000,
		'-',
		'indywidualny',
		'Borys',
		Number(4.14),
		'2415,45893719807',
		'1%',
		'2391,30584882609',
		'USDT',
		'1,4',
		'2487,0',
		'xyz',
		'24,2',
	],
	[
		'Przelew bankowy',
		'10.02.2025',
		'№12345',
		'PKO',
		'PLN',
		'10000',
		'-',
		'indywidualny',
		'Borys',
		'j3 4,14',
		'2415,45893719807',
		'1%',
		'2391,30584882609',
		'USDT',
		'1,4',
		'2487,0',
		'xyz',
		'24,2',
	],
]);
function showAlert(row, column) {
	tableData[row][column] = 'Clicked';
}
// Функція для розрахунку "Сумма мінус % комісії"
const amountInEurosFromClient = (row, rowIndex, cellIndex) => {
	const euroAmount = row[5];
	const commissionPercent = row[9];

	// Розрахунок

	const amountInEuros = euroAmount / commissionPercent;
	tableData[rowIndex][cellIndex] = amountInEuros;
	return amountInEuros;
};

// Функція експорту
const exportToExcel = () => {
	// Додаємо обчислені значення до таблиці
	const data = [headers.value, ...tableData];

	// Створюємо робочий лист
	const ws = XLSX.utils.aoa_to_sheet(data);

	// Додаємо стилі для заголовків
	headers.value.forEach((_, index) => {
		const cellRef = XLSX.utils.encode_cell({ r: 0, c: index });
		if (!ws[cellRef]) return;
		ws[cellRef].s = {
			font: { bold: true, color: { rgb: 'FFFFFF' } },
			fill: { fgColor: { rgb: '4F81BD' } },
			alignment: { horizontal: 'center' },
		};
	});

	// Встановлення ширини для всіх колонок (по 30 символів)
	ws['!cols'] = headers.value.map(() => ({ wch: 30 }));
	ws['!rows'] = headers.value.map(() => ({ hpt: 20 }));

	// Створюємо книгу
	const wb = XLSX.utils.book_new();
	XLSX.utils.book_append_sheet(wb, ws, 'Транзакції');

	// Генеруємо файл
	const excelBuffer = XLSX.write(wb, { bookType: 'xlsx', type: 'array' });
	const blob = new Blob([excelBuffer], { type: 'application/octet-stream' });
	saveAs(blob, 'Транзакції.xlsx');
};
</script>

<style scoped>
.container {
	max-width: 800px;
	margin: auto;
	padding: 20px;
}
table {
	width: 100%;
	border-collapse: collapse;
}
th,
td {
	border: 1px solid #ddd;
	padding: 8px;
	text-align: left;
}
th {
	background-color: #4f81bd;
	color: white;
	font-weight: bold;
	text-align: center;
}
input {
	width: 100%;
	border: none;
	padding: 4px;
}
input:disabled {
	background-color: #f3f3f3;
	color: #888;
}
button {
	margin-top: 10px;
	padding: 10px;
	background-color: #007bff;
	color: white;
	border: none;
	cursor: pointer;
}
button:hover {
	background-color: #0056b3;
}
</style>
