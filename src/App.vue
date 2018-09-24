<template>
  <div class="row">
            <div class="col s6 offset-s3">
                    <h5>Site title here...</h5>
                    <PlaceSearch  />
            </div>
            <div class="col s6 offset-s3">
                    <datepicker  v-model="date"></datepicker>
            </div>
            <div id="places" class="col s6 offset-s3">

                    <div class="card blue-grey darken-1" 
                    v-for="place in places"
                    v-bind:key="place.id"
                    v-bind:post="place" >
                            <div class="card-content white-text">
                                    <a href="#" @click="remove(place)"><span class="new badge red" data-badge-caption="Удалить"></span> </a>
                             <span class="card-title place-name">{{ place.location[0] }}</span>
                              <div class="row without-bottom-padding ">
                                    <div class="col s4">Рассвет</div>
                                    <div class="col s3">{{ place.times.dawn.getHours() + ':' + place.times.dawn.getMinutes() }}</div>
                              </div>
                              <div class="row without-bottom-padding ">
                                    <div class="col s4">Восход</div>
                                    <div class="col s3">{{ place.times.sunrise.getHours() + ':' + place.times.sunrise.getMinutes() }}</div>
                              </div>
                              <div class="row without-bottom-padding ">
                                    <div class="col s4">Полдень</div>
                                    <div class="col s3">{{ place.times.solarNoon.getHours() + ':' + place.times.solarNoon.getMinutes() }}</div>
                              </div>   
                              <div class="row without-bottom-padding ">
                                    <div class="col s4">Закат</div>
                                    <div class="col s3">{{ place.times.sunset.getHours() + ':' + place.times.sunset.getMinutes() }}</div>
                              </div>
                              <div class="row without-bottom-padding ">
                                    <div class="col s4">Сумерки</div>
                                    <div class="col s3">{{ place.times.dusk.getHours() + ':' + place.times.dusk.getMinutes() }}</div>
                              </div>
                            </div>
                            
                          </div>

            </div>
            <div class="col s6 offset-s3">
              
              <Chart />
            </div>
        </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker';
import PlaceSearch from './components/PlaceSearch.vue'
import Chart from './components/Chart.vue'
import { EventBus } from './components/EventBus.js'

function random_rgba() {
    var o = Math.round, r = Math.random, s = 255;
    return 'rgba(' + o(r()*s) + ',' + o(r()*s) + ',' + o(r()*s) + ',' + 255 + ')';
}

var SunCalc = require('suncalc');

export default {
  name: 'App',
  components: { PlaceSearch, Chart, Datepicker },
  data: function() {
    return {
      places: [],
      date: new Date(),
      chartdata: {
          datasets: []
        }
      }
  },
  methods:{
    remove: function (e) {
      var index = this.places.indexOf(e);
      this.places.splice(index, 1)
      this.chartdata.datasets.splice(index, 1);
      EventBus.$emit('chartdata', this.chartdata);
    }
  },
  mounted: function() {
    EventBus.$on('add-new-place', lastPlace => {
    var times = SunCalc.getTimes(this.date, lastPlace[1], lastPlace[2]);
    var place = {
      location: lastPlace,
      times: times
    };
  this.places.push(place);
  this.chartdata.datasets.push({
                    label: place.location[0],
                    data: [{
                        x: place.times.dawn.getHours() + ':' + place.times.dawn.getMinutes(), y: 0
                    }, {
                        x: place.times.sunrise.getHours() + ':' + place.times.sunrise.getMinutes(), y: 1
                    }, {
                        x: place.times.solarNoon.getHours() + ':' + place.times.solarNoon.getMinutes(), y: 2
                    }, {
                        x: place.times.sunset.getHours() + ':' + place.times.sunset.getMinutes(), y: 1
                    }, {
                        x: place.times.dusk.getHours() + ':' + place.times.dusk.getMinutes(), y: 0
                    }],
                    fill: false,
                    borderColor: random_rgba(),
                    cubicInterpolationMode: 'monotone'
                });
                 EventBus.$emit('chartdata', this.chartdata);
                
});
  }
}
</script>

<style>
.without-bottom-padding {
  margin-bottom: 0px;
}
</style>
