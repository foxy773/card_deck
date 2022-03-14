<template>
	<div class="deck-container">
		<div class="deck-container__control">
		<h1>Deck</h1>
		<h2>Deck ID: {{ cardDeck.id }} </h2>
		<h2>Remaining Cards: {{ cardDeck.remaining }} </h2>
		<h2>Deck Shuffled: {{ cardDeck.shuffled }} </h2>
		<input type="number" name="" id="" v-model="cardsToDraw" :max="cardDeck.remaining">
		<button @click="drawCard()">Draw Card</button>
		<button @click="fetchNewDeck()">New Deck</button>
		<button @click="getHistory()">Get history</button>
		<button @click="getAllLastCards()">Get all last cards</button>
		</div>
		<h3>Card Value: {{ cardDeck.totalValueDrawn }}</h3>
		<div class="deck-container__card">
			<img v-for="(card, index) in cardDeck.onHand" :src="card.image" alt="">
		</div>
		<div class="deck-container__history">
			<img v-for="(card, index) in cardDeck.history" :src="card.image" alt="">
		</div>
	</div>
</template>

<script>

export default {
	data() {
		return {
			cardsToDraw: 1,
			cardDeck: {
				id: null,
				remaining: null,
				shuffled: null,
				onHand: [

				],
				history: [

				],
				totalValueDrawn: null
			}
		}
	},

	async created() {
		this.fetchNewDeck()
	},

	methods: {

		async getAllLastCards() {
			  const lastDrawnCards = this.cardDeck.onHand
			  let cardCode = []
			  lastDrawnCards.forEach(card => {
					cardCode.push(card.code)
					return cardCode.join(",")
			  });
			  return cardCode
		  },

		async fetchNewDeck() {
			this.cardDeck.history = []
			this.cardDeck.onHand = []

            let API = `https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1`;
            const res = await fetch(API);
            const results = await res.json();
				console.log(results, "New deck created")
				this.cardDeck.id = results.deck_id
				this.cardDeck.remaining = results.remaining
				this.cardDeck.shuffled = results.shuffled
        },

		async calculateCards() {
			let calculatedTotal = 0
			   this.cardDeck.onHand.map((value)=> {
				   console.log(value.value, "Calcuate cards value")
				  switch (value.value) {
					case "ACE":
						calculatedTotal += 1
						break;
					case "2":
						calculatedTotal += 2
						break;
					case "3":
						calculatedTotal += 3
						break;
					case "4":
						calculatedTotal += 4
						break;
					case "5":
						calculatedTotal += 5
						break;
					case "6":
						calculatedTotal += 6
						break;
					case "7":
						calculatedTotal += 7
						break;
					case "8":
						calculatedTotal += 8
						break;
					case "9":
						calculatedTotal += 9
						break;
					case "10":
						calculatedTotal += 10
						break;
					case "JACK":
						calculatedTotal += 10
						break;
					case "QUEEN":
						calculatedTotal += 10
						break;
					case "KING":
						calculatedTotal += 10
						break;
			  	}
			})
			return calculatedTotal
		},

		  async drawCard() {
			  let history = this.cardDeck.history
			  let onHand = this.cardDeck.onHand
			  let lastDrawnCards = (await this.getAllLastCards()).toString()
				let API = `https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/draw/?count=${this.cardsToDraw}`;
				const res = await fetch(API);
         		const results = await res.json();

				this.cardDeck.onHand = await results.cards
				results.cards.forEach(card => {
					history.push(card)
				})

				/* this.cardDeck.history.push(results.cards[0]) */
				this.cardDeck.remaining = await results.remaining
	
				let API2 = `https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/pile/discarded/add/?cards=${lastDrawnCards}`;
				const res2 = await fetch(API2);
				const results2 = await res2.json();
				console.log(results2, "adding to history")

				this.cardDeck.totalValueDrawn = (await this.calculateCards()).toString()
		  },

		 async getHistory() {
			  let API = `https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/pile/discarded/list/`;
			  const options = {
				  method: 'POST',
				  body: ''
			  }
				const res = await fetch(API, options);
         		const results = await res.json();
				
				console.log(results, "History")
				console.log(this.cardDeck.history)
		  },
    },

	mounted() {
		
	}
}
</script>

<style scoped>
	img {
		width: 250px;
		height: auto;
	}

	.deck-container {
		height: 100%;
		width: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		background-image: url(".\..\..\public\images\casino-table.jpg");
	}

	.deck-container__control {
		width: 50%;
		display: flex;
		flex-direction: column;
		text-align: center;
	}

	.deck-container__card {
		height: 100%;
		width: 60%;
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		grid-template-rows: repeat(auto, 1fr);
		gap: 1rem;
	}

	.deck-container__card > img {
		align-self: center;
		justify-self: center;
		padding: 1rem;
	}

	.deck-container__history {
		display:flex;
		width: 100%;
		height: 10rem;
		background: rgb(24, 24, 24);
		overflow-x: scroll;
	}

	.deck-container__history > img{
		padding: 0.4rem;
		height: 100%;
		width: auto;

	}
</style>
