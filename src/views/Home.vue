<template>
	<div class="home">
		<div class="form">
			<!-- COVID19 IMPACT ESTIMATOR -->
			<form @submit.prevent="getImpactResult">
				<div class="form-group">
					<label for="population" class="mb-0">Population</label>
					<input
						id="population"
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
					<label for="time-period" class="mb-0">Time Period</label>
					<input
						id="time-period"
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
					<label for="time-period-type" class="mb-0"
						>Type of time period</label
					>
					<select
						id="time-period-type"
						v-model="dataInput.periodType"
						class="form-control"
					>
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
					<label for="reported-cases" class="mb-0"
						>Number of Reported Cases</label
					>
					<input
						id="reported-cases"
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
					<label for="total-hospital-beds" class="mb-0"
						>Total Hospital Beds</label
					>
					<input
						id="total-hospital-beds"
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
		covid19ImpactEstimator: (data) => {
			const impact = {}
			const severeImpact = {}

			let numberOfDays
			if (data.periodType === "days") {
				numberOfDays = data.timeToElapse
			} else if (data.periodType === "weeks") {
				numberOfDays = 7 * data.timeToElapse
			}
			if (data.periodType === "months") {
				numberOfDays = 30 * data.timeToElapse
			}

			impact.currentlyInfected = data.reportedCases * 10
			severeImpact.currentlyInfected = data.reportedCases * 50

			const exponent = Math.trunc(numberOfDays / 3)

			const infections = impact.currentlyInfected * 2 ** exponent
			const sInfections = severeImpact.currentlyInfected * 2 ** exponent

			const severeCases = 0.15 * infections
			const sSevereCases = 0.15 * sInfections

			const bedAvailability = 0.35 * data.totalHospitalBeds

			impact.infectionsByRequestedTime = Math.trunc(
				impact.currentlyInfected * 2 ** exponent
			)
			severeImpact.infectionsByRequestedTime = Math.trunc(
				severeImpact.currentlyInfected * 2 ** exponent
			)

			impact.severeCasesByRequestedTime = Math.trunc(severeCases)
			severeImpact.severeCasesByRequestedTime = Math.trunc(sSevereCases)

			impact.hospitalBedsByRequestedTime = Math.trunc(
				bedAvailability - severeCases
			)
			severeImpact.hospitalBedsByRequestedTime = Math.trunc(
				bedAvailability - sSevereCases
			)

			impact.casesForICUByRequestedTime = Math.trunc(
				0.05 * impact.infectionsByRequestedTime
			)
			severeImpact.casesForICUByRequestedTime = Math.trunc(
				0.05 * severeImpact.infectionsByRequestedTime
			)

			impact.casesForVentilatorsByRequestedTime = Math.trunc(
				0.02 * impact.infectionsByRequestedTime
			)
			severeImpact.casesForVentilatorsByRequestedTime = Math.trunc(
				0.02 * severeImpact.infectionsByRequestedTime
			)

			const majorityEarning = Number(data.region.avgDailyIncomePopulation)
			const avgDailyIncome = Number(data.region.avgDailyIncomeInUSD)
			const days = Number(numberOfDays)

			const dollarsInFlight =
				(impact.infectionsByRequestedTime *
					majorityEarning *
					avgDailyIncome) /
				days
			const sDollarsInFlight =
				(severeImpact.infectionsByRequestedTime *
					majorityEarning *
					avgDailyIncome) /
				days

			impact.dollarsInFlight = Math.trunc(dollarsInFlight)
			severeImpact.dollarsInFlight = Math.trunc(sDollarsInFlight)

			const result = {
				data,
				impact,
				severeImpact,
			}
			return result
		},
		getImpactResult() {
			this.covid19ImpactEstimator(this.dataInput)
			this.dataInput = this.getFreshData()
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
.form-control::-webkit-input-placeholder {
	color: #575757 !important;
	opacity: 1;
}
</style>
