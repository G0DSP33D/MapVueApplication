<template>
  <v-app>
    <v-container>
      <h1>Frank's Web Vue Application</h1>
      <v-row>
        <v-col>
          <v-text-field
            v-model="searchQuery"
            @keyup.enter="searchLocation"
            label="Search Location"
            outlined
          ></v-text-field>
          <v-btn @click="searchLocation">Search</v-btn>
          <v-btn @click="getCurrentLocation">Get Current Location</v-btn>

        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <div ref="map" style="height: 500px;"></div>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-data-table
            :headers="headers"
            :items="locations"
            :items-per-page="10"
            item-key = "id"
            show-select
            @input="updateSelected"
          ></v-data-table>
          <v-btn @click="deleteSelected">Delete</v-btn>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <p v-if="latestLocation"> <b>Most Recent Information</b></p>
          
          <p v-if="latestLocation">Time Zone: {{ latestLocation.timeZone }}</p>
          <p v-if="latestLocation">Local Time: {{ latestLocation.localTime }}</p>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchQuery: '',
      map: null,
      markers: [],
      locations: [],
      selected: [],
      headers: [
        { text: 'Location', value: 'location' },
        { text: 'Latitude', value: 'latitude' },
        { text: 'Longitude', value: 'longitude' },

        { text: 'Time Zone', value: 'timeZone' },
        { text: 'Local Time', value: 'localTime' },
      ],
      latestLocation: null,
      nextId:1 //for giving each row a unique id.

    };
  },
  mounted() {
    this.$nextTick(() => {
      this.initMap();
    });
  },
  methods: {
    initMap() {
      this.map = new google.maps.Map(this.$refs.map, { // eslint-disable-line no-undef
        center: { lat: -34.397, lng: 150.644},
        zoom: 8,
      });
    },
    async getCurrentLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          async (position) => {
            const { latitude, longitude } = position.coords;
            await this.addMarkerAndLocation(latitude, longitude);
          },
          () => {
            alert('Failed to get current location');
          }
        );
      } else {
        alert('Geolocation is not supported by this browser.');
      }
    },
    async searchLocation() {
      if (!this.searchQuery) return;

      const geocoder = new google.maps.Geocoder(); // eslint-disable-line no-undef
      geocoder.geocode({ address: this.searchQuery }, async (results, status) => {
        if (status === 'OK') {
          const location = results[0].geometry.location;
          await this.addMarkerAndLocation(location.lat(), location.lng());
        } else {
          alert('Geocode was not successful for the following reason: ' + status);
        }
      });
    },
    async addMarkerAndLocation(lat, lng) {
      const marker = new google.maps.Marker({ // eslint-disable-line no-undef
        position: { lat, lng },
        map: this.map,
      });

  this.map.setCenter({ lat, lng });
  this.markers.push(marker);

  try {
    const timestamp = Math.floor(Date.now() / 1000); // Current timestamp in seconds
    const timeZoneResponse = await axios.get(`https://maps.googleapis.com/maps/api/timezone/json`, {
      params: {
        location: `${lat},${lng}`,
        timestamp,
        key: 'AIzaSyD552SEy5_TNtKslTtrb60EvJc0ZlksB-s',
      },
    });

    const timeZoneData = timeZoneResponse.data;

    if (timeZoneData.status !== 'OK') {
      alert('Failed to fetch time zone data');
      return;
    }

    // Calculate local time
    const localTime = new Date((timestamp + timeZoneData.dstOffset + timeZoneData.rawOffset) * 1000);

    const formattedLocalTime = localTime.toLocaleTimeString();

    const locationData = {
      id: this.nextId, // Assign a unique id to each location
      location: timeZoneData.timeZoneId || 'Unknown',
      latitude: lat,
      longitude: lng,
      timeZone: timeZoneData.timeZoneName || 'Unknown',
      localTime: formattedLocalTime,
    };

    this.locations.push(locationData);
    this.latestLocation = locationData;
    this.nextId += 1;
  } catch (error) {
    console.error('An error occurred!', error);
    alert('Failed to fetch time zone data');
  }
},




updateSelected(value) {
  this.selected = value;
},

deleteSelected() {
    this.selected.forEach((selectedItem) => {
      const index = this.locations.findIndex(
        (location) => location.id === selectedItem.id
      );
      if (index !== -1) {
        this.locations.splice(index, 1);
        this.markers[index].setMap(null);
        this.markers.splice(index, 1);
      }
    });

    this.selected = [];
    
  }
}

}
</script>

<style scoped>
.v-application {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
</style>
