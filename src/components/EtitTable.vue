<template>
	<div class="p-4">
		<h2 class="text-lg font-bold mb-4">Fiat Table {{ props.tableName }}</h2>

		<!-- Таблиця -->
		<table class="border-collapse border w-full">
			<thead>
				<tr class="bg-gray-200">
					<th v-for="(header, index) in props.tableData" :key="index">
						{{ header.header }}
					</th>
				</tr>
			</thead>
			<tbody>
				<tr>
					<td v-for="(row, rowIndex) in props.tableData" :key="rowIndex">
						<span>id: {{ row.id }}</span>
						<input
							v-if="row.input_type === 'text' || row.input_type === 'number'"
							:type="row.input_type"
							v-model="row.value"
							:disabled="row.noEditable"
							@input="handleInput(row)"
							@click="handleClick(row)"
							:focus="row.focus"
						/>
						<select
							v-else-if="row.input_type === 'select' && row.select_options"
						>
							<option
								v-for="option in row.select_options"
								:value="option"
								:key="option"
							>
								{{ option }}
							</option>
						</select>
					</td>
				</tr>
			</tbody>
		</table>

		<button @click="exportToExcel">Export to Excel</button>
	</div>
</template>

<script setup>
import { defineProps } from 'vue';
import * as XLSX from 'xlsx-js-style';
import { saveAs } from 'file-saver';

const props = defineProps({
	tableData: Array,
	tableName: String,
});

// Обробник для @input
const handleInput = (row) => {
	if (row.action && row.dependsOn) {
		// Отримуємо значення залежних полів
		const values = row.dependsOn.map((id) => {
			const dependentItem = props.tableData.find((item) => item.id === id);
			return dependentItem ? parseFloat(dependentItem.value) || 0 : 0;
		});

		// Викликаємо функцію тільки якщо є всі потрібні значення
		if (values.length === row.dependsOn.length) {
			const result = row.action(...values);

			// Знаходимо поле для збереження результату
			const target = props.tableData.find((item) => item.id === row.target);
			if (target) {
				target.value = result;

				// Додаємо каскадні виклики handleInput для залежних полів
				props.tableData.forEach((item) => {
					if (item.dependsOn && item.dependsOn.includes(target.id)) {
						handleInput(item); // Повторний виклик для залежних полів
					}
				});
			}
		}
	}
};

// Обробник для @click
const handleClick = (row) => {
	if (row.action && row.input_type === 'text') {
		row.value = row.action(); // Встановлюємо нову дату
	}
};

// Функція експорту (залишила як у твоєму коді)
// const exportToExcel = () => {
// 	const headers = props.tableData.map((item) => item.header);
// 	const data = [headers, props.tableData.map((item) => item.value)];

// 	const ws = XLSX.utils.aoa_to_sheet(data);
// 	const wb = XLSX.utils.book_new();
// 	XLSX.utils.book_append_sheet(wb, ws, 'ReceivingFiatFromCustomer');

// 	const excelBuffer = XLSX.write(wb, { bookType: 'xlsx', type: 'array' });
// 	const blob = new Blob([excelBuffer], { type: 'application/octet-stream' });
// 	saveAs(blob, 'ReceivingFiatFromCustomer.xlsx');
// };

const exportToExcel = () => {
	const headers = props.tableData.map((item) => item.header);
	const data = props.tableData.map((item) => item.value);

	// 🟢 Додаємо порожні рядки зверху для заголовків
	const formattedData = [headers, data];

	// 🟢 Створюємо таблицю з формулюванням стилів
	const ws = XLSX.utils.aoa_to_sheet(formattedData);

	// 🟢 Встановлюємо мінімальну ширину стовпців (100 символів)
	const minWidth = 20;
	const colWidths = headers.map(() => ({ wch: minWidth }));
	ws['!cols'] = colWidths;

	// 🟢 Стилізація комірок
	Object.keys(ws).forEach((cell) => {
		if (cell.startsWith('!')) return; // Пропускаємо службові властивості типу !cols

		const cellRef = XLSX.utils.decode_cell(cell);
		const isHeader = cellRef.r === 2; // Перевіряємо, чи це рядок з заголовками (після порожніх рядків)
		const isData = cellRef.r > 2; // Перевіряємо, чи це рядок з даними

		// Стилі для заголовків
		if (isHeader) {
			ws[cell].s = {
				font: { bold: true, color: { rgb: 'FFFFFF' } },
				fill: { fgColor: { rgb: '4F81BD' } },
				alignment: { horizontal: 'center', vertical: 'center', wrapText: true },
				border: {
					top: { style: 'thin', color: { rgb: '000000' } },
					bottom: { style: 'thin', color: { rgb: '000000' } },
					left: { style: 'thin', color: { rgb: '000000' } },
					right: { style: 'thin', color: { rgb: '000000' } },
				},
			};
		}
		// Стилі для даних
		if (isData) {
			ws[cell].s = {
				alignment: { horizontal: 'center', vertical: 'center' },
				border: {
					top: { style: 'thin', color: { rgb: '000000' } },
					bottom: { style: 'thin', color: { rgb: '000000' } },
					left: { style: 'thin', color: { rgb: '000000' } },
					right: { style: 'thin', color: { rgb: '000000' } },
				},
			};
		}
	});

	// 🟢 Створюємо книгу та додаємо таблицю
	const wb = XLSX.utils.book_new();
	XLSX.utils.book_append_sheet(wb, ws, props.tableName);

	// 🟢 Генерація файлу
	const excelBuffer = XLSX.write(wb, { bookType: 'xlsx', type: 'array' });
	const blob = new Blob([excelBuffer], { type: 'application/octet-stream' });
	saveAs(blob, `${props.tableName}.xlsx`);
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
	position: relative;
}

td span {
	position: absolute;
	top: 0;
	right: 0;
	font-size: 12px;
}
th {
	background-color: #4f81bd;
	color: white;
	font-weight: bold;
	text-align: center;
	min-width: 100px;
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
