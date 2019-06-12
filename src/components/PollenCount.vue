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
    <h4 v-if="error" class="error">{{ errorMsg }}</h4>
    <h4 v-if="!error">{{ (location === '') ? "Today's Pollen Level" : `Pollen level for ${location}:` }}</h4>
    <transition name="fade">
      <h2 v-if="countKnown">{{ pollen_count }}</h2>
    </transition>
  </section>
</div>
</template>

<script>
  /* eslint-disable */
  import axios from 'axios';
  const PostcodesIO = require('postcodesio-client');
  const postcodes   = new PostcodesIO();
  
  export default {
    name: 'PollenCount',
    data() {
      return {
        error: false,
        errorMsg: '',
        postcode: '',
        countKnown: false,
        pollen_count: '',
        location: ''
      }
    },
    methods: {
      getCount(e) {
        // Setup
        e.preventDefault();
        this.countKnown = false;
        this.error = false;
        // Validate the postcode
        postcodes.validate(this.postcode)
          .then(exists => {
            if (!exists) {
              this.errorMsg = 'Invalid Postcode';
              this.error = true;
              } else {
                // Get the lat/long
                postcodes.lookupPostcode(this.postcode)
                  .then(data => {
                    const { longitude, latitude, nuts, admin_district } = data;
                    // Fetch the pollen count using the lat/long
                    axios.get(`https://cors-anywhere.herokuapp.com/https://socialpollencount.co.uk/api/forecast?location=[${latitude},${longitude}]`)
                      .then(res => {
                        // Today's date
                        const today = new Date().toISOString().substr(0,10);
                        // Get today's level
                        const todaysForecast = res.data.forecast.filter(forecast => forecast.date.startsWith(today));
                        this.pollen_count = todaysForecast[0].pollen_count;
                        this.countKnown = true;
                        // Get location to display
                        const location = nuts ? nuts : admin_district;
                        this.location = location;
                      });
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

  .error {
    color: #ff0000;
  }

  .pollen-box {
    margin-top: 2rem; }

  .location-inputs {
    justify-content: center; }

  #postcode {
    max-width: 50%;
    margin-left: auto;
    margin-right: auto;
    margin-bottom: 1.5rem;
    }

  .round-button {
    display: inline-block;
    padding: .75rem 1.25rem;
    margin: 0 0 .5rem 0;
    vertical-align: middle;
    text-align: center;
    cursor: pointer;
    text-decoration: none;
    line-height: 1;
    color: white;
    background: #0366EE;
    border: 1px solid #0366EE;
    border-radius: 40px;
    font-size: 1rem;
    font-weight: 600;
  }

  [type=color], [type=date], [type=datetime], [type=datetime-local], [type=email], [type=month], [type=number], [type=password], [type=search], [type=tel], [type=text], [type=url], [type=week], [type=time], select, textarea {
  display: block;
  border: 1px solid #cdcdcd;
  border-radius: 4px;
  padding: .75rem;
  outline: none;
  background: transparent;
  margin-bottom: .5rem;
  font-size: 1rem;
  width: 100%;
  max-width: 100%;
  line-height: 1;
  }

  input.has-error, input.has-error:hover, input.has-error:focus, input.has-error:active,
  select.has-error,
  select.has-error:hover,
  select.has-error:focus,
  select.has-error:active,
  textarea.has-error,
  textarea.has-error:hover,
  textarea.has-error:focus,
  textarea.has-error:active {
  border: 1px solid #D33C40;
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.1), 0 0 6px #f4cecf; }

  .fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
  }
  
  .fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
  }

</style>
