<template>
	<table border="1" cellspacing="0" cellpadding="5">
		<thead>
			<tr>
				<!-- Виводимо заголовки з header_title -->
				<th v-for="(col, colIndex) in headers" :key="colIndex">
					{{ col.header_title }}
				</th>
			</tr>
		</thead>
		<tbody>
			<!-- Проходимо по кожному рядку -->
			<tr v-for="(row, rowIndex) in rows" :key="rowIndex">
				<!-- Для кожного стовпця (використовуємо header-об'єкт) -->
				<td v-for="(col, colIndex) in headers" :key="colIndex">
					<span>{{ rowIndex }}/{{ colIndex }}</span>
					<!-- Якщо тип "sum": обчислюємо суму за допомогою independent_on -->
					<!-- <template v-if="col.type === 'sum'">
						<input
							type="number"
							:value="computeSum(row, col.independent_on)"
							:disabled="col.disabled"
						/>
					</template> -->
					<!-- Якщо тип "formula": використовуємо функцію formula, задану в header -->
					<!-- <template v-if="col.type === 'formula' || col.type === 'sum'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template> -->
					<!-- Якщо тип "calculateRateFromPlnToEur": використовуємо функцію formula, задану в header -->
					<template v-if="col.type === 'calculateRateFromPlnToEur'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "subtractingPercentageFromAmount": використовуємо функцію formula, задану в header -->
					<template v-else-if="col.type === 'subtractingPercentageFromAmount'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "calculateCommission": використовуємо функцію formula, задану в header -->
					<template v-else-if="col.type === 'calculateCommission'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "calculateMultiplicationByPurely": використовуємо функцію formula, задану в header -->
					<template v-else-if="col.type === 'calculateMultiplicationByPurely'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "profit": використовуємо функцію formula, задану в header -->
					<template v-else-if="col.type === 'profit'">
						<input
							type="number"
							:value="col.formula(row, rowIndex, colIndex)"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "date": input типу date -->
					<template v-else-if="col.type === 'date'">
						<input
							type="date"
							v-model="row[colIndex]"
							:disabled="col.disabled"
						/>
					</template>
					<!-- Якщо тип "select": відображаємо select з варіантами -->
					<template v-else-if="col.type === 'select'">
						<select v-model="row[colIndex]" :disabled="col.disabled">
							<option
								v-for="option in col.options"
								:key="option"
								:value="option"
							>
								{{ option }}
							</option>
						</select>
					</template>
					<!-- Якщо тип "number": input типу number з двостороннім зв'язком -->
					<template v-else-if="col.type === 'number'">
						<input
							type="number"
							v-model.number="row[colIndex]"
							:disabled="col.disabled"
							placeholder="Enter the value"
						/>
					</template>
					<!-- За замовчуванням (тип "text"): input типу text -->
					<template v-else>
						<input
							type="text"
							v-model="row[colIndex]"
							:disabled="col.disabled"
							placeholder="Enter the value"
						/>
					</template>
				</td>
			</tr>
		</tbody>
	</table>
	<button @click="addRow">Додати рядок</button>
	<!-- <button @click="deleteRow">Видалити рядок</button> -->
	<!-- <button @click="console.log(rows)">log data</button> -->
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
	headers: {
		type: Array,
		required: true,
	},
	rows: {
		type: Array,
		required: true,
	},
});

const emits = defineEmits(['addRow', 'deleteRow']);

function addRow() {
	emits('addRow');
}
</script>

<style scoped>
table {
	border-collapse: collapse;
	margin: 20px 0;
	box-sizing: border-box;
}

th,
td {
	box-sizing: border-box;
	text-align: center;
	padding: 0px;
	position: relative;
}

th {
	min-height: 30px;
	height: 70px;
	padding: 10px;
	min-width: 200px;
}

td span {
	position: absolute;
	top: 0;
	right: 0;
	font-size: 12px;
}

td input {
	width: fit-content;
	padding: 10px 20px;
	width: -webkit-fill-available;
}
td input:disabled {
	width: fit-content;
	width: -webkit-fill-available;
	padding: 10px 20px;
	background-color: rgba(204, 204, 204, 0.288);
}
td select {
	width: fit-content;
	width: -webkit-fill-available;
	padding: 10px 20px;
}
</style>
