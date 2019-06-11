<template>
  <div class="xsmall-container text-center pollen-box">
  <header><h1>Pollen Count</h1></header>
  <form @submit="getCount">
    <div class="flex-row location-inputs">
      <input type="text" id="postcode" v-model="postcode" class="text-center"
        placeholder="Enter a UK Postcode" autofocus required>
    </div>
    <input id="submit" class="round-button" type="submit" value="Find">
  </form>

  <section id="results">
    <h4 id="foundLocation">Today's Pollen Level</h4>
    <h2 id="levelText" class="animated">&nbsp;</h2>
  </section>
</div>
</template>

<script>
  /* eslint-disable */
  // import axios from 'axios';
  //const isValid = require('uk-postcode-validator');
  const PostcodesIO = require('postcodesio-client');
  const postcodes   = new PostcodesIO();
  
  export default {
    name: 'PollenCount',
    data() {
      return {
        postcode: ''
      }
    },
    methods: {
      getCount(e) {
        // Validate the postcode
        e.preventDefault();
        postcodes.validate(this.postcode)
          .then(exists => {
            if (!exists) {
              console.log('BAD POSTCODE');
              } else {
                // Get the lat/long
                postcodes.lookupPostcode(this.postcode)
                  .then(data => {
                    const { longitude, latitude } = data;
                    console.log(`${longitude},${latitude}`);
                  });
              }
            });        
      }
    }

  }
</script>

<style scoped>
  header h1 {
  font-size: 4rem; }

  .pollen-box {
    margin-top: 2rem; }

  .location-inputs {
    justify-content: center; }

  #postcode {
    max-width: 50%; }
</style>
