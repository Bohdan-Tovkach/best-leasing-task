<template>
<div>
	<v-card 
		class="ml-5 mr-5 mt-3 pl-5 pr-5 d-flex flex-column"
		color="green darken-3"
		elevation="8"
		>
		<!-- Radio, checkers -->
		<Checkers
			:tripValue="roundTrip"
			@listenChangeTrip="changeTripState($event)" 
		/>
		<v-row class="d-flex justify-center">
			<!-- From city - field -->
			<v-col class="input pr-0 pl-0" cols="12" lg="2" md="3" sm="6" xs="8">
				<v-autocomplete
					:items="cityArr"
					item-text="city"
					item-value="city"
					background-color="white"
					outlined
					v-model="ticket.fromCity"
					placeholder="From"
					prepend-inner-icon="mdi-map-marker"
				>
				<template #append>
					<v-icon @click="reversePosition()" class="reverse-icon">{{ 'mdi-swap-horizontal-bold' }}</v-icon>
				</template>
				</v-autocomplete>
			</v-col>
			<!-- To city - field -->
			<v-col class="input pr-0 pl-0" cols="12" lg="2" md="3" sm="6" xs="8">
				<v-autocomplete
					:items="cityArr"
					item-text="city"
					item-value="city"
					background-color="white"
					outlined
					v-model="ticket.toCity"
					placeholder="To"
					prepend-inner-icon="mdi-map-marker"
				>
				</v-autocomplete>
			</v-col>
			<!-- Ticket field -->
			<v-col class="input pr-0 pl-0" cols="12" lg="2" md="3" sm="6" xs="8">
				<v-menu
					v-model="tiketMenu"
					:close-on-content-click="false"
					transition="scale-transition"
					offset-y
					max-width="290px"
          min-width="290px"
					>
					<template v-slot:activator="{ on }">
						<v-text-field
							background-color="white"
							v-model="ticket.dateTicket"
							placeholder="Ticket"
							prepend-inner-icon="mdi-calendar-month"
							v-on="on"
							outlined
						></v-text-field>
					</template>
					<v-date-picker
						v-model="date"
						:min="currentDate"
						no-title 
						@input="tiketMenu = false"
						>
					</v-date-picker>
				</v-menu>
			</v-col>
			<!-- Return ticket field -->
			<v-col class="input pr-0 pl-0" cols="12" lg="2" md="3" sm="6" xs="8">
				<v-menu
					v-model="returnTicketMenu"
					:close-on-content-click="false"
					transition="scale-transition"
					offset-y
					max-width="290px"
          min-width="290px"
					>
					<template v-slot:activator="{ on }">
						<v-text-field
							:disabled="!roundTrip"
							background-color="white"
							v-model="ticket.dateReturnTicket"
							placeholder="Return ticket"
							prepend-inner-icon="mdi-calendar-month"
							v-on="on"
							outlined
						></v-text-field>
					</template>
					<v-date-picker
						v-model="returnDate"
						:min="currentDate"
						no-title 
						@input="returnTicketMenu = false"
						>
					</v-date-picker>
				</v-menu>
			</v-col>
			<!-- Ticket quantity field -->
			<v-col class="input pr-0 pl-0" cols="12" lg="2" md="3" sm="6" xs="8">
				<v-menu
					offset-y
					persist-on-click
					:close-on-content-click="false"
				>
					<template v-slot:activator="{ on }">
						<v-text-field
							background-color="white"
							v-model="amountTickets"
							placeholder="Number of ticket"
							v-on="on"
							outlined
							prepend-inner-icon="mdi-account"
						>
						</v-text-field>
					</template>
					<v-list>
						<v-list-item 
							v-for="(item, key) in passangerCategories"
							:key="key"
							>
							<v-list-item-title>{{ item }}</v-list-item-title>
							<v-btn @click="ticketMinus(item)" icon>
								<v-icon :color="ticket[item] > 0 ? 'primary' : ''">
									{{ 'mdi-minus-circle' }}
								</v-icon>
							</v-btn>
							<span class="pa-2">{{ ticket[item] }}</span>
							<v-btn @click="ticketPlus(item)" icon>
								<v-icon :color="checkAmount ? 'primary' : ''">{{ 'mdi-plus-circle' }}</v-icon>
							</v-btn>
						</v-list-item>
						<v-select
							:items="ticketClass"
							placeholder="select class"
							offset-y
							class="pl-3 pr-3 pt-3"
							dense
							outlined
							v-model="ticket.ticketClass"
						>
						</v-select>
					</v-list>
				</v-menu>
			</v-col>
			<!-- introduces the final object in console -->
			<v-btn 
				color="orange darken-3 white--text" 
				height="56" 
				class="mt-3 input ml-1 search-btn"
				@click="objToConsole()"
			>search
			</v-btn>
		</v-row>
	</v-card>
</div>
</template>

<script>
import Checkers from './checkers.vue'
import city from '../cities-array.js'

export default {
	data: () => ({
		/* v-models (input data) */
		date: null,
		returnDate: null,
		ticket: {
			dateTicket: null,
			dateReturnTicket: null,
			fromCity: null,
			toCity: null,
			ticketClass: '',
			adults: 0,
			children: 0,
			infants: 0,
		},

		/* booleans */
		checkAmount: true,
		roundTrip: true,
		tiketMenu: false,
		returnTicketMenu: false,

		/* arrays */
		cityArr: city,
		passangerCategories: [
			'adults',
			'children',
			'infants'
		],
		maxTicketForChild: 12,
		maxTicketForInfants: 2,
		ticketClass: [
			'economy',
			'business',
			'first'
		],
	}),
	computed: {
		amountTickets() {
			let quantity = this.ticket.adults + this.ticket.children + this.ticket.infants
			if(quantity) return `${quantity} ${this.ticket.ticketClass}`
			return ''
		},
		currentDate() {
			return new Date().toISOString().substr(0, 10)
		},
	},
	methods: {
		ticketMinus(item) {
			this.ticket[item] > 0 ? this.ticket[item]-- : false
		},
		ticketPlus(item) {
			console.log(item)
			if(item === 'adults') this.ticket[item]++
			else if(item === 'children') {
				if(this.ticket[item] < this.maxTicketForChild) {
					this.ticket[item]++
					this.checkAmount = true
				}else {
					this.checkAmount = false
					return
				}
			}
			else if(item === 'infants') {
				if(this.ticket[item] < this.maxTicketForInfants) {
					this.ticket[item]++
					this.checkAmount = true
				}else {
					this.checkAmount = false
					return
				}
			}
		},
		reversePosition() {
			[this.ticket.toCity, this.ticket.fromCity] = [this.ticket.fromCity, this.ticket.toCity]
		},
		changeTripState(val) {
			this.roundTrip = val
		},
		objToConsole() {
			console.log(this.ticket)
		}
	},
	watch: {
		date(val) {
			const [year, month, day] = val.split('-')
			this.ticket.dateTicket = `${day}.${month}.${year}`
		},
		returnDate(val) {
			const [year, month, day] = val.split('-')
			this.ticket.dateReturnTicket = `${day}.${month}.${year}`
		}
	},
	components: {
		Checkers
	}
}
</script>

<style lang="scss">
	.v-text-field__details {
		margin: 0!important;
		display: none!important;
	}
	.reverse-icon:hover {
		cursor: pointer;
	}
	.v-input__control {
		margin: 0 1px;
	}

	input[type=checkbox] {
		position: absolute;
		margin-top: 6px;
		width: 30px;
		height: 21px;
	}
	.checkbox {
		position: relative;
		display: flex;
		flex-wrap: wrap;
		min-width: 140px;
	}
	.checkbox-label {
		position: absolute;
		min-width: 150px;
		color: white;
		bottom: 11px;
		left: 35px;
	}
	.search-btn {
		min-width: 130px!important;
	}

	$color1: #f4f4f4;
	$color2: #3197EE;
	.radio {
		margin: 0.5rem;
		input[type="radio"] {
			position: absolute;
			opacity: 0;
			+ .radio-label {
				color: white;
      &:before {
        content: '';
        background: $color1;
        border-radius: 100%;
        border: 1px solid darken($color1, 25%);
        display: inline-block;
        width: 1.4em;
        height: 1.4em;
        position: relative;
        top: -0.2em;
        margin-right: 1em; 
        vertical-align: top;
        cursor: pointer;
        text-align: center;
				transition: all 250ms ease;
      }
    }
    &:checked {
      + .radio-label {
        &:before {
          background-color: $color2;
          box-shadow: inset 0 0 0 4px $color1;
        }
      }
    }
    &:focus {
      + .radio-label {
        &:before {
          outline: none;
          border-color: $color2;
        }
      }
    }
    &:disabled {
      + .radio-label {
        &:before {
          box-shadow: inset 0 0 0 4px $color1;
          border-color: darken($color1, 25%);
          background: darken($color1, 25%);
        }
      }
    }
    + .radio-label {
      &:empty {
        &:before {
					margin-right: 0;
        }
      }
    }
  }
}
</style>