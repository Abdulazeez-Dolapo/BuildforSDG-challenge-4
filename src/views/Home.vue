<template>
	<div class="home">
		<div class="form">
			<p>COVID19 IMPACT ESTIMATOR</p>
			<form @submit.prevent="getImpactResult">
				<label for="population">Population</label>
				<input
					id="population"
					for="data-population"
					name="data-population"
					data-population=""
					v-model.number="dataInput.population"
					type="number"
					placeholder="Enter population size"
				/>

				<label for="time-period">Time Period</label>
				<input
					id="time-period"
					for="data-time-to-elapse"
					name="data-time-to-elapse"
					data-time-to-elapse=""
					v-model.number="dataInput.timeToElapse"
					type="number"
					placeholder="Enter time period"
				/>

				<label for="time-period-type">Type of time period</label>
				<select id="time-period-type" v-model="dataInput.periodType">
					<option
						for="data-period-type"
						name="data-period-type"
						:data-period-type="typeOfPeriod"
						:value="type"
						v-for="type in typeOfPeriod"
						:key="type"
					>
						{{ type }}
					</option>
				</select>

				<label for="reported-cases">Number of Reported Cases</label>
				<input
					id="reported-cases"
					for="data-reported-cases"
					name="data-reported-cases"
					data-reported-cases=""
					v-model.number="dataInput.reportedCases"
					type="number"
					placeholder="Enter number of reported cases"
				/>

				<label for="total-hospital-beds">Total Hospital Beds</label>
				<input
					id="total-hospital-beds"
					for="data-total-hospital-beds"
					name="data-total-hospital-beds"
					data-total-hospital-beds=""
					v-model.number="dataInput.totalHospitalBeds"
					type="number"
					placeholder="Enter number of hospital beds"
				/>

				<button
					for="data-go-estimate"
					name="data-go-estimate"
					data-go-estimate=""
					type="submit"
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
			console.log(data - period - type)
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

p {
	font-style: italic;
	font-size: 1.4em;
	margin-top: 0.5em;
}

.form {
	background-color: white;
	min-height: 75vh;
	width: 30%;
	margin: 1em auto;
	padding: 0.1rem 2rem 0.5rem;
	font-weight: bolder;
	font-size: 1.1em;
}
input,
label,
select {
	display: block;
	min-width: 80%;
	height: 2em;
	font-size: 1em;
}

select {
	margin-bottom: 1em;
}

input {
	margin-bottom: 1em;
	border: solid black 1px;
	border-radius: 1px;
	font-size: 1em;
}

button {
	border-radius: 0.5em;
	width: 50%;
	height: 2em;
	font-size: 1em;
	font-weight: bolder;
	background-color: whitesmoke;
}
</style>
