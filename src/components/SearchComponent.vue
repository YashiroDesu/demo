<template>
    <div>
      <div class="input-group">
        <div class="form-outline">
          <!--Input field for getting address-->
          <input ref="autocomplete" type="search" id="form1" class="form-control" placeholder="Enter an address" @keyup.enter="search" />
        </div>
        <button type="button" class="btn btn-primary" @click="search">
          <i class="fas fa-search">search</i>
        </button>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  
  export default {
    name: "SearchBar",
    mounted() {
      // Initialize Google Places autocomplete
      const autocomplete = new window.google.maps.places.Autocomplete(
        this.$refs.autocomplete, {
          types: ['geocode']
        }
      )
  
      // Listen for place selection
      autocomplete.addListener('place_changed', this.onPlaceChanged);
    },
    data() {
      return {
        address: '',
        coordinates: {}
      };
    },
    methods: {
      onPlaceChanged() {
        //Updating address when a place is changed
        this.address = this.$refs.autocomplete.value;
      },
      async search() {
        if (this.address) {
          try {
            //Fetching latitude and longitude of the searched location using google maps geocoding api
            const response = await axios.get(
              `https://maps.googleapis.com/maps/api/geocode/json?address=${encodeURIComponent(this.address)}&key=AIzaSyAXkem4RNrrY4D2d80W5GPpVtowFYsk4e8`
            )
  
            const { results } = response.data
            
            if (results && results.length > 0) {
              //Extracting latitude and longitude from received data and emitting the result to parent component through an event
              const { lat, lng } = results[0].geometry.location;
              this.coordinates = { lat, lng }
              this.$emit('searchedResult', {
                coordinates: this.coordinates,
                address: this.address
              })
            }
          } catch (error) {
            console.error('Error retrieving coordinates:', error);
          }
        }
      }
    }
  }
  </script>
  