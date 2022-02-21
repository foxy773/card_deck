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
		<button @click="getHistory()">Get drawn cards</button>
		<button @click="getAllLastCards()">GET</button>
		</div>
		<h3>Card Value: </h3>
		<div class="deck-container__card">
			<img v-for="(card, index) in cardDeck.lastDrawnCards" :src="card.image" alt="">
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
				lastDrawnCards: [

				],
				totalValueDrawn: null
			}
		}
	},

	async created() {
		this.fetchNewDeck()
	},

	methods: {

		getAllLastCards() {
			  const lastDrawnCards = this.cardDeck.lastDrawnCards
			  let cardCode = []
			  lastDrawnCards.forEach(card => {
					cardCode.push(card.code)
					console.log(cardCode)
					return cardCode.join(",")
			  });
			  return cardCode
		  },

		async fetchNewDeck() {
            let API = `https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1`;
            const res = await fetch(API);
            const results = await res.json();

				console.log(results)

				this.cardDeck.id = results.deck_id
				this.cardDeck.remaining = results.remaining
				this.cardDeck.shuffled = results.shuffled
        },

		  async drawCard() {
			  let lastDrawnCards = this.getAllLastCards()
				let API = `https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/draw/?count=${this.cardsToDraw}`;
				const res = await fetch(API);
         	const results = await res.json();

				this.cardDeck.lastDrawnCards = results.cards
				this.cardDeck.remaining = results.remaining
	
				let API2 = console.log(`https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/pile/discarded/add/?cards=${lastDrawnCards}`);
				const res2 = await fetch(API2);
				const results2 = await res2.json();
				console.log(results2, "adding history")
				
		  },

		 async getHistory() {
			  let API = `https://deckofcardsapi.com/api/deck/${this.cardDeck.id}/pile/discarded/draw/?count=2`;
				const res = await fetch(API);
         	const results = await res.json();
				
				console.log(results, "History")
		  },

		   calculateCards() {
			  /* this.cardsDeck.lastDrawnCards.map((value)=> {
				  console.log(hello)
				  switch (value) {
					case ACE:
						this.cardsDecktotalValueDrawn =+ 1
						break;
					case 2:
						totalValueDrawn =+ 2
						break;
					case 3:
						totalValueDrawn =+ 3
						break;
					case 4:
						totalValueDrawn =+ 4
						break;
					case 5:
						cardDeck.totalValueDrawn =+ 5
						break;
					case 6:
						cardDeck.totalValueDrawn =+ 6
						break;
					case 7:
						cardDeck.totalValueDrawn =+ 7
						break;
					case 8:
						cardDeck.totalValueDrawn =+ 8
						break;
					case 9:
						cardDeck.totalValueDrawn =+ 9
						break;
					case 10:
						cardDeck.totalValueDrawn =+ 10
						break;
					case JACK:
						cardDeck.totalValueDrawn =+ 10
						break;
					case QUEEN:
						cardDeck.totalValueDrawn =+ 10
						break;
					case KING:
						cardDeck.totalValueDrawn =+ 10
						break;
			  	}
			})	   */
		}
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
</style>
