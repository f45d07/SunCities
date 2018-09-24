<template>
  <div class="google-place-search">
	<input id="place-autocomplete" class="controls" type="text" placeholder="Введите населённый пункт">
  </div>
</template>

<script>
import Vue from 'vue'
import { EventBus } from './EventBus.js'

export default {
  name: 'PlaceSearch',
  props: ['name'],
  data: function () {
    return {
      
    }
  },
  mounted: function() {
    var input = document.getElementById('place-autocomplete');
                var searchBox = new google.maps.places.SearchBox(input);
                searchBox.addListener('places_changed', function() {
                    var places = searchBox.getPlaces();
                    
                    if(places.length == 0){
                        return;
                    }
                    places.forEach(function(place){
                        if(!place.geometry) {
                            console.log("Returned place contains no geometry");
                            return;
                        }
                        EventBus.$emit('add-new-place', [place.address_components[0].short_name, place.geometry.location.lat(), place.geometry.location.lng()]);
                    });
                });
  }
};
</script>