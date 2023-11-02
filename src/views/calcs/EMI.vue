<template>
	<div class="p-4">
		<div>
			<h3>Inputs:</h3>
			<div class="flex space-x-8">

				<div>
					<label for="principalInput">Principal Amount</label>
					<input id="principalInput" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded" type="number" v-model="principal">
				</div>
				<div>
					<label for="rateInput">Annual rate of interest</label>
					<input id="rateInput" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded" type="number"  v-model="annual_rate">
				</div>
				<div>
					<label for="tenure">Tenure in years</label>
					<input id="tenure" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded" type="number" v-model="tenure_years">
				</div>
			</div>
		</div>
		<div>
			<h3>Outputs:</h3>
			<p>EMI: {{emi}}</p>
			<table class="table-auto border-collapse border">
				<thead>

					<tr>
						<th>Sr. No.</th>
						<th>Month</th>
						<th>EMI</th>
						<th>Principal (% of total principal)</th>
						<th>Interest (% of total interest)</th>
						<th>Balance Interest (% of total interest)</th>
						<th>Balance Principal (% of total principal)</th>
					</tr>
				</thead>
				<tbody>

					<tr v-for="m in sums" :key="m.sn">
						<td>{{ m.sn }}</td>
						<td>{{ m.month }} </td>
						<td>{{ m.emi.toLocaleString('en-IN') }} </td>
						<td>{{ m.principal.toLocaleString('en-IN') }} ({{ m.currentPrincipalCent }}%) </td>
						<td>{{ m.interest.toLocaleString('en-IN') }} ({{ m.currentInterestCent }}%) </td>
						<td>{{ m.interestBalance.toLocaleString('en-IN') }} ({{ m.currentInterestBalanceCent }}%) </td>
						<td>{{ m.principalBalance.toLocaleString('en-IN') }} ({{ m.currentPrincipalBalanceCent }}%) </td>
					</tr>
				</tbody>
			</table>
		</div>
  	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
	data() {
		return {
			principal: 50_00_000,
			annual_rate: 9,
			tenure_years: 20
		}
	},
	computed: {
		emi() {
			const r = this.annual_rate / 1200;
			const n = this.tenure_years * 12;
			const p = this.principal;
			return Math.round(p * r * Math.pow(1 + r, n) / (Math.pow(1 + r, n) - 1 ));
		},
		totalInterest() {
			return (this.emi * 12 * this.tenure_years) - this.principal;
		},
		sums() {
			const months = [];
			let principalBalance = this.principal;
			let interestBalance = this.totalInterest;
			for (let m = 0; m < 12 * this.tenure_years; m++) {
				const currentInterest = Math.round(principalBalance * this.annual_rate / 1200);
				const currentPrincipal = Math.round(this.emi - currentInterest);
				months.push({
					sn: m + 1,
					month: `${Math.floor(m/12) + 1}Y ${(m % 12 ) + 1}M`,
					emi: this.emi,
					principal: currentPrincipal,
					currentPrincipalCent: (100 * currentPrincipal/this.principal).toFixed(2),
					currentInterestCent: (100 * currentInterest/this.totalInterest).toFixed(2),
					interest: currentInterest,
					principalBalance: principalBalance - currentPrincipal,
					currentPrincipalBalanceCent: (100 * (principalBalance - currentPrincipal)/this.principal).toFixed(2),
					interestBalance: interestBalance - currentInterest,
					currentInterestBalanceCent: (100 * (interestBalance - currentInterest)/this.totalInterest).toFixed(2),
				});
				principalBalance -= currentPrincipal;
				interestBalance -= currentInterest;
			}
			return months;
		}
	}

})
</script>

<style>
table {
	@apply text-center;
}
table thead tr th {
	@apply border border-slate-600 p-2 bg-slate-50;
}
table tbody tr td {
	@apply border border-slate-700 p-2;
}
table tbody tr:nth-child(12n) {
	@apply bg-slate-200;
}
</style>