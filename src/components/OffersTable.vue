<template>
  <div>
    <table class="table">
      <thead>
      <tr>
        <th scope="col">Kuupäev</th>
        <th scope="col">Kell</th>
        <th scope="col">Aadress</th>
        <th scope="col">Pakkuja</th>
        <th scope="col">Söök</th>
        <th scope="col">Hind</th>
        <th scope="col">Broneeringuid</th>
        <th scope="col">Hinnang</th>
        <th scope="col">Lähen sööma</th>
      </tr>
      </thead>

      <tbody>
      <tr v-for="offer in filteredOffers" :key="offer.offerId">
        <td><a>{{ formatDate(offer.date) }}</a></td>
        <td><a>{{ offer.time }}</a></td>
        <td><a>{{ offer.address }}</a></td>
        <td>
          <div class="hover-container" @click="openUserOfferView(offer.userId)">
          <a>{{ offer.firstName }} {{ offer.lastName }}</a>
            <div class="hover-text">Kasutaja pakkumised</div>
          </div>
        </td>
        <td>
          <div class="hover-container">
            <a>{{ offer.offerName }}</a>
            <div class="hover-text">{{ offer.description }}</div>
          </div>
        </td>
        <td><a>{{ offer.price }}€</a></td>
        <td><a>{{offer.bookings}}/{{offer.totalPortions}}</a></td>
        <td><a>{{ offer.userRating }}</a></td>
        <td>
            <a>
              <button v-show="offer.totalPortions > offer.bookings" type="button" class="btn btn-success" @click="launchConfirmModal(offer.offerId)">Go Eat A Bit!</button>
            </a>
        </td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<style>
.hover-container {
  position: relative;
  display: inline-block;
}

.hover-text {
  visibility: hidden;
  position: absolute;
  bottom: 100%;
  left: -5; /* Start from the left edge of the container */
  width: 200px; /* Adjust to your needs */

  transform: translate(-50%, -8px);
  background-color: #333;
  color: #fff;
  padding: 8px;
  border-radius: 4px;
  text-align: center;
}

.hover-container:hover .hover-text {
  visibility: visible;
}
</style>

<script>
import router from "@/router";

export default {
  name: "OffersTable",
  props: {
    filter: {}
  },

  data() {
    return {
      filteredOffers: [
        {
          offerId: 0,
          userId: 0,
          userRating: 0,
          time: '',
          date: '',
          price: 0,
          totalPortions: 0,
          offerName: '',
          description: '',
          foodGroupId: 0,
          offerStatus: '',
          address: '',
          districtId: 0,
          firstName: '',
          lastName: '',
          imageString: 0,
          bookings: 0
        }
      ]
    }
  },

  methods: {
    getFilteredOffers() {
      this.$http.get("/meals/filtered-offers", {
            params: {
              districtId: this.filter.selectedDistrictId,
              date: this.filter.selectedDate,
              foodGroupId: this.filter.selectedFoodGroupId,
              description: this.filter.description,
              priceLimit: Number(this.filter.priceLimit),
              userId: 0
            }
          }
      ).then(response => {
        this.filteredOffers = response.data
      }).catch(error => {
        const errorResponseBody = error.response.data
      })
    },
    launchConfirmModal(offerId) {
      this.$emit('event-book-meal', offerId)
    },
    openUserOfferView(userId) {
      this.$emit('event-open-user-offer-view', userId)
    },
    formatDate(date){
      const parts = date.split('-');
      return `${parts[2]}.${parts[1]}`
    }
  },
  mounted() {
    this.getFilteredOffers()
  },
}
</script>