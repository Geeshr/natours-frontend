<template>
  <div class="create-tour">
    <div class="create-tour__wrapper">
      <form v-show="!showSuccessMessage" class="create-tour__form" @submit.prevent="submitForm">
        <p class="create-tour__header">Create new tour!</p>
        <label for="name"> Tour name: </label>
        <input class="create-tour__form__input" type="text" v-model="newTourData.name" />
        <br />
        <br />
        <label for="description">Description: </label>
        <textarea class="create-tour__form__input" type="text" v-model="newTourData.description" />
        <br />
        <br />
        <label for="startDate">Start Date: </label>
        <flat-pickr
          class="create-tour__form__date"
          id="startDate"
          v-model="newTourData.startDate"
          :config="datePickerConfig"
        ></flat-pickr>
        <label for="endDate"> &nbsp;End Date: </label>
        <flat-pickr
          class="create-tour__form__date"
          id="endDate"
          v-model="newTourData.endDate"
          :config="datePickerConfig"
        ></flat-pickr>
        <br />
        <br />
        <label for="price">Price: </label>
        <input class="create-tour__form__input" type="text" v-model="newTourData.price" />
        <br />
        <br />
        <label for="maxGroupSize">Group size: </label>
        <input class="create-tour__form__input" type="text" v-model="newTourData.maxGroupSize" />
        <br />
        <br />
        <label for="difficulty">Difficulty: </label>
        <input class="create-tour__form__input" type="text" v-model="newTourData.difficulty" />
        <br />
        <br />
        <label for="summary">Summary: </label>
        <textarea class="create-tour__form__input" type="text" v-model="newTourData.summary" />
        <br />
        <br />
        <button type="submit">Create Tour</button>
      </form>
      <div v-if="showSuccessMessage" class="success-message">New tour created successfully!</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { ref } from 'vue'
import FlatPickr from 'vue-flatpickr-component'
import 'flatpickr/dist/flatpickr.css'

export default {
  props: {},

  components: {
    FlatPickr
  },
  setup() {
    // Reactive variables for date picker configuration, tour data, and success message
    const datePickerConfig = ref({
      enableTime: false,
      dateFormat: 'Y-m-d'
    })
    const defaultTourData = {
      images: [],
      startDate: '',
      endDate: '',
      name: '',
      maxGroupSize: null,
      difficulty: '',
      price: null,
      summary: '',
      description: ''
    }
    const showSuccessMessage = ref(false)

    // Function to create a new tour
    async function createTour(newTourData) {
      try {
        const response = await axios.post(
          'https://natours-9mok.onrender.com/api/v1/tours',
          newTourData
        )
        return response
      } catch (error) {
        console.error('Error creating tour:', error)
      }
    }

    // Reactive variable for storing new tour data
    const newTourData = ref({ ...defaultTourData })

    // Function to submit the form
    async function submitForm() {
      try {
        await createTour(newTourData.value)

        // Reset form data and show success message
        newTourData.value = { ...defaultTourData }
        showSuccessMessage.value = true
        setTimeout(() => {
          showSuccessMessage.value = false
        }, 5000)
      } catch (error) {
        console.error('Error submitting form:', error)
      }
    }

    return {
      newTourData,
      submitForm,
      datePickerConfig,
      showSuccessMessage
    }
  }
}
</script>

<style lang="scss">
.create-tour {
  padding: 10px;
  width: 100%;

  &__wrapper {
    display: flex;
    justify-content: center;
  }
  &__header {
    font-size: 20px;
  }
  &__form {
    border-radius: 15px;
    margin-top: 20px;
    -webkit-box-shadow: 10px 10px 20px 5px lightgray;
    -moz-box-shadow: 10px 10px 20px 5px lightgray;
    box-shadow: 10px 10px 20px 5px lightgray;
    width: 50%;
    height: auto;
    padding: 35px;
    &__input {
      width: 100%;
      border-top: none;
      border-left: none;
      border-right: none;
    }
    &__date {
      border-top: none;
      border-left: none;
      border-right: none;
      &:focus-visible {
        outline: none;
        border-top: none;
        border-left: none;
        border-right: none;
      }
    }
  }
}
</style>
