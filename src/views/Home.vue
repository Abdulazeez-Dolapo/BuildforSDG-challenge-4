<template>
	<div class="home">
		<div class="form">
			<!-- COVID19 IMPACT ESTIMATOR -->
			<form @submit.prevent="getImpactResult">
				<div class="form-group">
					<label class="mb-0">Population</label>
					<input
						for="data-population"
						name="data-population"
						class="form-control"
						attribute="data-population"
						v-model.number="dataInput.population"
						type="number"
						placeholder="Enter population size"
					/>
				</div>

				<div class="form-group">
					<label class="mb-0">Time Period</label>
					<input
						for="data-time-to-elapse"
						name="data-time-to-elapse"
						attribute="data-time-to-elapse"
						class="form-control"
						v-model.number="dataInput.timeToElapse"
						type="number"
						placeholder="Enter time period"
					/>
				</div>

				<div class="form-group">
					<label class="mb-0">Type of time period</label>
					<select v-model="dataInput.periodType" class="form-control">
						<option
							for="data-period-type"
							name="data-period-type"
							attribute="data-period-type"
							:value="type"
							v-for="type in typeOfPeriod"
							:key="type"
						>
							{{ type }}
						</option>
					</select>
				</div>

				<div class="form-group">
					<label class="mb-0">Number of Reported Cases</label>
					<input
						for="data-reported-cases"
						name="data-reported-cases"
						attribute="data-reported-cases"
						class="form-control"
						v-model.number="dataInput.reportedCases"
						type="number"
						placeholder="Enter number of reported cases"
					/>
				</div>

				<div class="form-group">
					<label class="mb-0">Total Hospital Beds</label>
					<input
						for="data-total-hospital-beds"
						name="data-total-hospital-beds"
						attribute="data-total-hospital-beds"
						class="form-control"
						v-model.number="dataInput.totalHospitalBeds"
						type="number"
						placeholder="Enter number of hospital beds"
					/>
				</div>

				<button
					for="data-go-estimate"
					name="data-go-estimate"
					attribute="data-go-estimate"
					type="submit"
					class="btn btn-block btn-primary"
				>
					Submit
				</button>
			</form>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			typeOfPeriod: ["days", "weeks", "months"],
			dataInput: this.getFreshData(),
		}
	},
	methods: {
		getFreshData() {
			return {
				region: {
					name: "Africa",
					avgAge: 19.7,
					avgDailyIncomeInUSD: 5,
					avgDailyIncomePopulation: 0.71,
				},
				periodType: "",
				timeToElapse: "",
				reportedCases: "",
				population: "",
				totalHospitalBeds: "",
			}
		},
		getImpactResult() {
			console.log(this.dataInput)
			covid19ImpactEstimator(this.dataInput)
			this.getFreshData()
		},
	},
}
</script>

<style>
.home {
	font-family: helvetica, cursive, Arial, sans-serif;
	width: 100%;
	height: 100%;
	box-sizing: border-box;
	margin: 0;
	padding: 0;
	background-color: teal;
	position: absolute;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
}
.form {
	background-color: white;
	min-height: 75vh;
	width: 40%;
	margin: 2em auto;
	padding: 2rem;
	font-weight: bolder;
}
</style>
