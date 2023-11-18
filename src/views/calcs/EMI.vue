<template>
	<div class="p-4">
		<div>
			<h3>Inputs:</h3>
			<div class="flex space-x-8">

				<div title="Amount of money you're borrowing">
					<label for="principalInput">Principal Amount </label>
					<input id="principalInput" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded"
						type="number" v-model.number="principal">
				</div>
				<div title="12 * Monthly interest rate percentage">
					<label for="rateInput">Annual rate of interest</label>
					<input id="rateInput" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded" type="number"
						v-model.number="annual_rate">
				</div>
				<div title="Number of years that you would like to pay the loan for">
					<label for="tenure">Tenure in years</label>
					<input id="tenure" class="w-full px-3 py-1 bg-gray-50 border border-gray-300 rounded" type="number"
						v-model.number="tenure_years">
				</div>
			</div>
		</div>
		<div>
			<h3>Outputs:</h3>
			<div class="">

				<div>EMI: {{ emi }}</div>
				<div>For the loan amount of <span class="font-bold text-xl text-red-900">{{
					principal.toLocaleString('en-IN') }}</span>, you will end up paying <span
						class="font-bold text-xl text-red-900">{{ (totalInterest + principal).toLocaleString('en-IN')
						}}</span> in the span of {{ tenure_years }} years. Out of this, <span
						class="font-bold text-xl text-red-900">{{ totalInterest.toLocaleString('en-IN') }}</span> will be
					paid towards the interest, which means you will have paid an interest that's <span
						class="font-bold text-xl text-red-900">{{ (totalInterest / principal).toFixed(2) }}</span> times the
					principal and is <span class="font-bold text-xl text-red-900">{{ Math.round(totalInterest * 100 /
						(totalInterest + principal)) }}%</span> of total amount paid. </div>
				{{ totalPrepaid }} {{ totalInterestWithPrepayments }}
				<div v-if="totalPrepaid > 0">
					With the extra prepayments of <span class="font-bold text-xl text-red-900">{{
						totalPrepaid.toLocaleString('en-IN') }}</span>, you will end up paying <span
						class="font-bold text-xl text-red-900">{{ totalInterestPaidWithPrepayments.toLocaleString('en-IN')
						}}</span> in the span of <span class="font-bold text-xl text-red-900">{{ closingMonthStats?.sn
}}</span> months (<span class="font-bold text-xl text-red-900">{{ ((closingMonthStats?.sn ?? 0)
	/ 12).toFixed(1) }}</span> years) towards the interest (saving <span
						class="font-bold text-2xl text-red-900">{{ (totalInterest -
							totalInterestPaidWithPrepayments).toLocaleString('en-IN') }}</span>) and have finished the loan
					<span class="font-bold">{{ sums.length - (closingMonthStats?.sn ?? 0) }} months</span> early. </div>
			</div>
			<table class="table-auto border-collapse border">
				<thead class="">

					<tr>
						<th>Sr. No.</th>
						<th>Month</th>
						<th title="Color split indicates interest vs principal component">EMI</th>
						<th>Principal (% of remaining principal)</th>
						<th>Interest (% of remaining interest)</th>
						<th>Balance Interest (% of total interest)</th>
						<th>Balance Principal (% of total principal)</th>
						<th>Extra prepayment <input type="number" class="border"
								@input="e => extra_prepayments = new Array(12 * tenure_years).fill(Number((e.target as HTMLInputElement)?.value) ?? 0)" />
						</th>
					</tr>
				</thead>
				<tbody>

					<tr v-for="m in sums" :key="m.sn" :class="m.principalBalance < 0 ? 'no-longer-needed' : ''">
						<td>{{ m.sn }}</td>
						<td>{{ m.month }} </td>
						<td :style="`background: linear-gradient(90deg, #03C03C 0%, #03C03C ${m.principalCentOfEMI}%, #EF0107 ${m.principalCentOfEMI}%, #EF0107 100%);color: white`"
							:title="`${m.principalCentOfEMI}% of this EMI is going towards the principal`">{{
								m.emi.toLocaleString('en-IN') }} </td>
						<td>{{ m.principal.toLocaleString('en-IN') }} ({{ m.currentPrincipalCent }}%) </td>
						<td>{{ m.interest.toLocaleString('en-IN') }} ({{ m.currentInterestCent }}%) </td>
						<td>{{ m.interestBalance.toLocaleString('en-IN') }} ({{ m.currentInterestBalanceCent }}%) </td>
						<td>{{ m.principalBalance.toLocaleString('en-IN') }} ({{ m.currentPrincipalBalanceCent }}%) </td>
						<td><input type="number" class="border" v-model.number="extra_prepayments[m.sn - 1]" /> </td>
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
			tenure_years: 20,
			extra_prepayments: new Array(20 * 12).fill(0)
		}
	},
	watch: {
		tenure_years(newTensure, oldTenure) {
			if (newTensure > oldTenure) {
				this.extra_prepayments.push(...new Array((newTensure - oldTenure) * 12).fill(0));
			} else {
				this.extra_prepayments.splice(0, (oldTenure - newTensure) * 12);
			}
		}
	},
	computed: {
		emi() {
			const r = this.annual_rate / 1200;
			const n = this.tenure_years * 12;
			const p = this.principal;
			return Math.round(p * r * Math.pow(1 + r, n) / (Math.pow(1 + r, n) - 1));
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
				const currentPrincipal = Math.round(this.emi - currentInterest) + Number(this.extra_prepayments[m]);
				months.push({
					sn: m + 1,
					month: `${Math.floor(m / 12) + 1}Y ${(m % 12) + 1}M`,
					emi: this.emi,
					principal: currentPrincipal,
					currentPrincipalCent: (100 * currentPrincipal / this.principal).toFixed(2),
					currentInterestCent: (100 * currentInterest / this.totalInterest).toFixed(2),
					interest: currentInterest,
					principalCentOfEMI: (100 - (100 * currentInterest / this.emi)).toFixed(2),
					principalBalance: principalBalance - currentPrincipal,
					currentPrincipalBalanceCent: (100 * (principalBalance - currentPrincipal) / this.principal).toFixed(2),
					interestBalance: interestBalance - currentInterest,
					currentInterestBalanceCent: (100 * (interestBalance - currentInterest) / this.totalInterest).toFixed(2),
				});
				principalBalance -= currentPrincipal;
				interestBalance -= currentInterest;
			}
			return months;
		},
		closingMonthStats() {
			return this.sums.find(m => m.principalBalance < 0)
		},
		totalPrepaid() {
			return this.extra_prepayments.slice(0, this.closingMonthStats?.sn).reduce((a, c) => a + Number(c), 0)
		},
		totalInterestPaidWithPrepayments() {
			return this.sums.slice(0, this.closingMonthStats?.sn).reduce((a, c) => a + c.emi + Number(this.extra_prepayments[c.sn - 1]), 0) - this.principal;
		},
		totalInterestWithPrepayments() {
			return this.sums.slice(0, this.closingMonthStats?.sn).reduce((a, c) => a + c.principal + c.interest, 0) + - this.principal;
		}
	}

})
</script>

<style>table {
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

.no-longer-needed td {
	background: repeating-linear-gradient(45deg, #ccc 1px, transparent 5px) !important;
}</style>
