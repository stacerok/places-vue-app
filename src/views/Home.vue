<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div>
      <h2>Add a place.</h2>
      <p>
        Name:
        <input type="text" placeholder="Place Name" v-model="newPlaceName" />
      </p>
      <p>
        Description:
        <input type="text" placeholder="Place Address" v-model="newPlaceAddress" />
        <br />
      </p>
      <p>
        <button v-on:click="createPlace()">Add Place</button>
      </p>
      <div v-for="place in places" v-bind:key="place.id">
        <h3>{{ place.title }}</h3>
        {{ place.name }}
        <br />
        {{ place.address }}
        <br />
        <button v-on:click="showPlace(place)">More Info.</button>
        <dialog id="place-details">
          <form method="dialog">
            <h1>Place Info</h1>
            <p>
              Name:
              <input type="text" v-model="currentPlace.name" />
            </p>
            <p>
              Address:
              <input type="text" v-model="currentPlace.address" />
            </p>
            <button v-on:click="updatePlace(currentPlace)">Update</button>
            <button v-on:click="destroyPlace(currentPlace)">Delete</button>
            <button>Close</button>
          </form>
        </dialog>
      </div>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "My Places Database",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        this.places = response.data;
        console.log("All Places:", this.places);
      });
    },
    createPlace: function () {
      console.log("Adding a Place!");
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log("Successfully added added a place!", response.data);
          this.places.push(response.data);
        })
        .catch((error) => console.log(error.response));
    },
    showPlace: function (place) {
      console.log(place), (this.currentPlace = place), document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios.patch("/api/places/" + place.id, params).then((response) => {
        console.log("Success", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("/api/places/" + place.id).then((response) => {
        console.log("Place deleted", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
