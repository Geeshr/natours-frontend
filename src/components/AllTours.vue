<template>
  <div v-if="currentPage === 'tourList'">
    <div class="tour" v-for="(tours, index) in tours.data" :key="index">
      <p>All available tours</p>

      <div class="tour__container" v-for="(tour, idx) in tours" :key="idx">
        {{ console.log("tour", tour) }}
        <p class="tour__container__tour-title">{{ tour.name }}!</p>
        <div class="tour__container__description">
          <span class="tour__container__description__text">
            Description: {{ tour.description }}
          </span>
        </div>
        <div class="tour__container__info">
          <span class="tour__container__info__text">
            Duration: {{ tour.duration }} days</span
          >

          <span class="tour__container__info__text">Price: ${{ tour.price }}</span>
          <span class="tour__container__info__text">
            Rating total: {{ tour.ratingsQuantity }}
          </span>
          <span class="tour__container__info__text">
            Ratings:
            {{ tour.ratingsAverage }}
          </span>
          <span class="tour__container__info__text">
            Group size: {{ tour.maxGroupSize }}</span
          >
          <span class="tour__container__info__text">
            Difficulty: {{ tour.difficulty }}</span
          >
        </div>
        <div class="tour__container__book-tour">
          <button class="tour__container__book-tour__button" @click="bookTour(tour.id)">
            Book tour!
          </button>
        </div>
        <div class="tour__container__book-tour">
          <button class="tour__container__book-tour__button" @click="deleteTour(tour.id)">
            Delete tour!
          </button>
        </div>
      </div>
    </div>
  </div>
  <div v-else-if="currentPage === 'bookingForm'">
    <!-- Render your booking form component here -->
    <BookingForm :tourId="selectedTourId" />
  </div>
</template>

<script>
import axios from "axios";
import { onMounted, ref } from "vue";
import BookingForm from "./BookingForm.vue"; // Import your BookingForm component here

export default {
  props: {
    userData: { type: Object, default: null },
  },
  components: {
    BookingForm, 
  },
  setup(props) {
    const tours = ref([]);
    const currentPage = ref("tourList"); 
    const selectedTourId = ref();

    async function fetchTours() {
      try {
        const response = await axios.get(
          "https://natours-9mok.onrender.com/api/v1/tours"
        );
        tours.value = response.data;
        console.log("tours", tours.value);
      } catch (error) {
        console.error("Error fetching tours:", error);
      }
    }
    async function deleteTour(tourId) {
      try {
        await axios.delete(`https://natours-9mok.onrender.com/api/v1/tours/${tourId}`);
        await fetchTours();
      } catch (error) {
        console.error("Error deleting tour:", error);
      }
    }

    async function bookTour(tourId) {
      try {
        selectedTourId.value = tourId;
        currentPage.value = "bookingForm"; 
      } catch (error) {
        console.error("Error booking tour:", error);
      }
    }
    console.log("props.user id", props.userData);

    onMounted(() => {
      fetchTours();
    });
    return {
      tours,
      currentPage,
      bookTour,
      deleteTour,
      selectedTourId,
    };
  },
};
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
    // border: 1px solid black;
    border-radius: 15px;

    margin-top: 20px;
    -webkit-box-shadow: 10px 10px 20px 5px lightgray;
    -moz-box-shadow: 10px 10px 20px 5px lightgray;
    box-shadow: 10px 10px 20px 5px lightgray;
    width: 70%;
    height: auto;
    // display: flex;
    // justify-content: center;
    // align-items: center;
    &__tour-title {
      display: flex;
      justify-content: center;
      text-transform: capitalize;
      font-weight: bold;
      font-size: 18px;
    }
    &__description {
      padding: 15px;
      &__text {
        display: flex;
        justify-content: center;
        text-transform: capitalize;
        font-size: 16px;
      }
    }
    &__info {
      display: flex;
      /* justify-content: center; */
      flex-direction: column;
      &__text {
        display: flex;
        justify-content: center;
      }
    }
    &__book-tour {
      display: flex;
      justify-content: center;
      padding: 10px;
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
