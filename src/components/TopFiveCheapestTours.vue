<template>
  <div v-if="currentPage === 'tourList'">
    <div class="tour" v-for="(tours, index) in tours.data" :key="index">
      <p>Top 5 cheapest tours!</p>

      <div class="tour__container" v-for="(tour, idx) in tours" :key="idx">
        <p class="tour__container__tour-title">{{ tour.name }}!</p>
        <div class="tour__container__description">
          <span class="tour__container__description__title"> Summary: </span>
          <span class="tour__container__description__text"> {{ tour.summary }} </span>
        </div>
        <div class="tour__container__info">
          <span class="tour__container__info__text"> Duration: {{ tour.duration }} days</span>

          <span class="tour__container__info__text">Price: ${{ tour.price }}</span>
          <span class="tour__container__info__text">
            Rating total: {{ tour.ratingsQuantity }}
          </span>
          <span class="tour__container__info__text">
            Ratings:
            {{ tour.ratingsAverage }}
          </span>
          <span class="tour__container__info__text"> Group size: {{ tour.maxGroupSize }}</span>
          <span class="tour__container__info__text"> Difficulty: {{ tour.difficulty }}</span>
        </div>
        <div class="tour__container__book-tour">
          <button class="tour__container__book-tour__button" @click="bookTour(tour.id)">
            Book tour!
          </button>
          <button class="tour__container__book-tour__button" @click="deleteTour(tour.id)">
            Delete tour!
          </button>
        </div>
      </div>
    </div>
  </div>
  <div v-else-if="currentPage === 'bookingForm'">
    <BookingForm :tourId="selectedTourId" />
  </div>
</template>

<script>
import axios from 'axios'
import { onMounted, ref } from 'vue'
import BookingForm from './BookingForm.vue'

export default {
  components: {
    BookingForm
  },
  setup() {
    // Reactive variables for tours, current page, and selected tour ID
    const tours = ref([])
    const currentPage = ref('tourList')
    const selectedTourId = ref()

    // Function to fetch tours from the server
    async function fetchTours() {
      try {
        const response = await axios.get(
          'https://natours-9mok.onrender.com/api/v1/tours/top-5-cheap'
        )
        tours.value = response.data
      } catch (error) {
        console.error('Error fetching tours:', error)
      }
    }

    // Function to handle booking a tour
    async function bookTour(tourId) {
      try {
        selectedTourId.value = tourId
        currentPage.value = 'bookingForm'
      } catch (error) {
        console.error('Error booking tour:', error)
      }
    }

    // Function to handle deleting a tour
    async function deleteTour(tourId) {
      try {
        await axios.delete(`https://natours-9mok.onrender.com/api/v1/tours/${tourId}`)
        await fetchTours()
      } catch (error) {
        console.error('Error deleting tour:', error)
      }
    }

    // Fetch tours on component mount
    onMounted(() => {
      fetchTours()
    })
    return {
      tours,
      bookTour,
      currentPage,
      selectedTourId,
      deleteTour
    }
  }
}
</script>

<style lang="scss" scoped>
.tour {
  font-family: sans-serif;
  padding: 10px;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  &__container {
    border-radius: 15px;

    margin-top: 20px;
    -webkit-box-shadow: 10px 10px 20px 5px lightgray;
    -moz-box-shadow: 10px 10px 20px 5px lightgray;
    box-shadow: 10px 10px 20px 5px lightgray;
    width: 40%;
    height: auto;
    &__tour-title {
      display: flex;
      justify-content: center;
      text-transform: capitalize;
      font-weight: bold;
      font-size: 18px;
    }
    &__description {
      padding: 15px;
      &__title {
        font-weight: bold;
        display: flex;
        justify-content: center;
        text-transform: capitalize;
      }
      &__text {
        display: flex;
        justify-content: center;
        text-transform: capitalize;
        font-size: 16px;
      }
    }
    &__info {
      display: flex;
      flex-direction: column;
      padding-bottom: 10px;
      &__text {
        display: flex;
        justify-content: center;
      }
    }
    &__book-tour {
      display: flex;
      justify-content: center;
      padding: 10px;
      border: 1px solid lightgray;
      border-radius: 0px 0px 12px 12px;
      background: lightgrey;
      &__button {
        background: none;
        border: none;
        text-decoration: underline;
        font-size: 18px;
      }
    }
  }
}
</style>
