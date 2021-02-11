<template>
  <div class="home">
    <h1>All Places</h1>
    
    <div v-for="error in errors" v-bind:key="error.id">
      {{ error }}
    </div>

    <div>
      <div>
        Name: <input type="text" v-model="newPlaceName"> <br>
        Address: <input type="text" v-model="newPlaceAddress"> <br>
      </div>
      <button v-on:click="createPlace">Create Place</button>
    </div>

    <div v-for="place in places" v-bind:key="place.id">
      <h1>Name: {{ place.name }}</h1>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog>
      <form method="dialog">
        {{ currentPlace }}
        <h2>Product Info</h2>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>address: <input type="text" v-model="currentPlace.address"></p>
        <button>Close</button>
        <button v-on:click="updatePlace()">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Destroy</button>
      </form>
    </dialog>

  </div>
</template>

<style>
img {
  width: 250px;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js with Actualize!",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/api/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("http://localhost:3000/api/places", params)
        .then((response) => {
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("dialog").showModal();
    },
    updatePlace: function () {
      var params = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
      };
      axios
        .patch(`api/places/${this.currentPlace.id}`, params)
        .then((response) => {
          console.log("success", response.data);
        });
    },
    // destroyProduct: function (inputRecipe) {},
  },
};
</script>