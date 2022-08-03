<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Places",
      places: [],
      newPlaceParams: {},
      showErrorMessage: false,
      errorMessage: "",
      currentPlace: {},
      editPlaceParams: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/places").then((response) => {
        this.places = response.data;
        console.log("All Places: ", this.places);
      });
    },
    newPlace: function () {
      document.querySelector("#place-create").showModal();
    },
    createPlace: function () {
      axios
        .post("places/", this.newPlaceParams)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.push(response.data);
          this.newPlaceParams = {};
          this.showErrorMessage = false;
        })
        .catch((error) => (this.errorMessage = error))
        .then((this.showErrorMessage = true));
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      this.editPlaceParams = place;
      document.querySelector("#place-info").showModal();
    },
    updatePlace: function (place) {
      axios.patch("places/" + place.id, this.editPlaceParams).then((response) => {
        console.log("Success!", response.data);
      });
    },
    destroyPlace: function (place) {
      axios.delete("places/" + place.id).then((response) => {
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <br />
    <button v-on:click="newPlace()">Add a Place</button>
    <div>
      <dialog id="place-create">
        <form method="dialog">
          <h5>Add a Place</h5>
          <h6>
            Name:
            <input type="text" v-model="newPlaceParams.name" />
          </h6>
          <h6>
            Address:
            <input type="text" v-model="newPlaceParams.address" />
          </h6>
          <button v-on:click="this.createPlace()">Create</button>
          <button>Close</button>
        </form>
      </dialog>
    </div>
    <div>
      <h3>Places</h3>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h5>{{ place.name }}</h5>
      <button v-on:click="showPlace(place)">Show Info</button>
      <br />
    </div>
    <dialog id="place-info">
      <form method="dialog">
        <h5>Edit Place</h5>
        <h6>
          Name:
          <input type="text" v-model="editPlaceParams.name" />
        </h6>
        <h6>
          Address:
          <input type="text" v-model="editPlaceParams.address" />
        </h6>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>
