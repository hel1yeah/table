<template>
	<Spreadsheet ref="spreadsheet" :license="license">
		<Worksheet :data="data" :columns="columns" />
	</Spreadsheet>
	<input type="button" value="getData" @click="getData()" />
</template>

<script>
import { Spreadsheet, Worksheet } from '@jspreadsheet/vue';
import 'jsuites/dist/jsuites.css';
import 'jspreadsheet/dist/jspreadsheet.css';

const license =
	'ZDkwYjU1YzM4NDBmYTI5ZTQ2NWMzOWZkYmM4YWE2YWVhYjU5YzE0NDAzZTFhODMxMzcxMWNkZjkxYjRkYWQzM2M4NjAyYmRmZjk5YTFmMDMzMDMzYTdhMmU0NGUyZWJlM2Y3NWFjNzQ3NTQzZmEzMzk5MWNiNzkzYTY0NzE2N2EsZXlKamJHbGxiblJKWkNJNklpSXNJbTVoYldVaU9pSktjM0J5WldGa2MyaGxaWFFpTENKa1lYUmxJam94TnpReE5ESXhOVFF3TENKa2IyMWhhVzRpT2xzaWFuTndjbVZoWkhOb1pXVjBMbU52YlNJc0ltTnZaR1Z6WVc1a1ltOTRMbWx2SWl3aWFuTm9aV3hzTG01bGRDSXNJbU56WWk1aGNIQWlMQ0ozWldJaUxDSnNiMk5oYkdodmMzUWlYU3dpY0d4aGJpSTZJak0wSWl3aWMyTnZjR1VpT2xzaWRqY2lMQ0oyT0NJc0luWTVJaXdpZGpFd0lpd2lkakV4SWl3aVkyaGhjblJ6SWl3aVptOXliWE1pTENKbWIzSnRkV3hoSWl3aWNHRnljMlZ5SWl3aWNtVnVaR1Z5SWl3aVkyOXRiV1Z1ZEhNaUxDSnBiWEJ2Y25SbGNpSXNJbUpoY2lJc0luWmhiR2xrWVhScGIyNXpJaXdpYzJWaGNtTm9JaXdpY0hKcGJuUWlMQ0p6YUdWbGRITWlMQ0pqYkdsbGJuUWlMQ0p6WlhKMlpYSWlMQ0p6YUdGd1pYTWlYU3dpWkdWdGJ5STZkSEoxWlgwPQ==';

export default {
	components: {
		Spreadsheet,
		Worksheet,
	},
	methods: {
		getData() {
			console.log(this.$refs.spreadsheet.current[0].getData());
		},
	},
	data() {
		// Worksheet data
		const data = [
			[
				'Przewalutowanie własnego Fiat', // A
				'2/10/25', // B
				'2', // C
				'PLN', // D
				'10000', // E (Ilość waluty początkowej)
				'EUR', // F
				'4.14', // G (Kurs waluty docelowej)
				'=E1/G1', // H (Ilość waluty docelowej)
				'Wanda', // I
			],
		];

		// Columns
		const columns = [
			{ title: 'Przewalutowanie własnego Fiat', width: 200 },
			{ title: 'Data', width: 100, type: 'calendar' },
			{ title: 'Nr operacji', width: 100 },
			{ title: 'Typ waluty początkowej', width: 160 },
			{ title: 'Ilość waluty początkowej', width: 160 },
			{ title: 'Typ waluty docelowej', width: 150 },
			{ title: 'Kurs waluty docelowej', width: 150 },
			{ title: 'Ilość waluty docelowej', width: 160, freeze: true },
			{ title: 'Miejsce zasilenia', width: 150 },
		];

		return {
			data,
			columns,
			license,
		};
	},
};
</script>
