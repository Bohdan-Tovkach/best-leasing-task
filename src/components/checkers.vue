<template>
<v-col cols="12" class="mt-3 d-inline-flex">
	<div class="radio">
		<input 
			@input="changeRoundTrip()" 
			id="radio-1" 
			type="radio" 
			v-model="roundTripProp" 
			:value="true"
			>
		<label for="radio-1" class="radio-label" >Round Trip</label>
	</div>

	<div class="radio">
		<input @input="changeRoundTrip()" 
			id="radio-2" 
			type="radio" 
			v-model="roundTripProp" 
			:value="false"
			>
		<label for="radio-2" class="radio-label">One way</label>
	</div>
	<div class="checkbox">
		<input id="checkbox" name="checkbox" type="checkbox">
		<label  for="checkbox" class="checkbox-label">Directs fly only</label>
	</div>
</v-col>
</template>

<script>
export default {
	props: ['tripValue'],
	data: () => ({
		roundTripProp: null,
	}),
	methods: {
		changeRoundTrip() {
			this.roundTripProp = !this.roundTripProp
		}
	},
	mounted() {
		this.roundTripProp = this.tripValue
	},
	watch: {
		roundTripProp(val) {
			if(this.roundTripProp !== null) this.$emit('listenChangeTrip', val)
			return
		}
	}
}
</script>