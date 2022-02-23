<template>
	<h1>Create an event</h1>
	<div class="form-container">
		<form @submit.prevent="onSubmit">
			<label>Select a category: </label>
			<select v-model="event.category">
				<option v-for="option in categories" :value="option" :key="option"
					:selected="option === event.category">{{ option }}</option>
			</select>
			<h3>Name & describe your event</h3>
			<label>Title</label>
			<input v-model="event.title" type="text" placeholder="Title">
			<label>Description</label>
			<input v-model="event.description" type="text" placeholder="Description" />
			<h3>Where is your event?</h3>
			<label>Location</label>
			<input v-model="event.location" type="text" placeholder="Location" />
			<h3>When is your event?</h3>
			<label>Date</label>
			<datepicker v-model="event.date" :inputFormat="'LLLL d yyyy'">
			</datepicker>
			<label>Time</label>
			<input v-model="event.time" type="text" placeholder="Time" />
			<button type="submit">Submit</button>
		</form>
	</div>
</template>
<script>
	import { v4 as uuidv4 } from 'uuid'
	import Datepicker from 'vue3-datepicker'
	import { mapState, mapActions } from 'vuex'
	export default {
		components: { Datepicker },
		data() {
			return {
				categories: [
					'sustainability',
					'nature',
					'animal welfare',
					'housing',
					'education',
					'food',
					'community'
				],
				months: [
					'January',
					'February',
					'March',
					'April',
					'May',
					'June',
					'July',
					'August',
					'September',
					'October',
					'November',
					'December'
				],
				event: {
					id: '',
					category: '',
					title: '',
					description: '',
					location: '',
					date: new Date(),
					time: '',
					organizer: '',
				}
			}
		},
		computed: {
			...mapState(['user'])
		},
		methods: {
			...mapActions('event', ['createEvent']),
			onSubmit() {
				this.event.id = uuidv4()
				this.event.organizer = this.user.userInfo.name
				const d = this.event.date
				this.event.date = `${this.months[d.getMonth()]} ${d.getDate()}, ${d.getFullYear()}`
				this.createEvent(this.event)
					.then(() => {
						this.$router.push({
							name: 'EventDetails',
							params: { id: this.event.id }
						})
					})
					.catch(error => {
						this.router.push({
							name: 'ErrorDisplay',
							params: { error: error }
						})
					})
			},
		}
	}
</script>