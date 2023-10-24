# Map-Vue-Application

This project is a web-based application developed using Vue.js, integrated with Google Maps API. The application provides users with a rich, interactive interface to search for locations, view them on a map, and retrieve relevant time zone information.


![plot](https://github.com/G0DSP33D/MapVueApplication/blob/main/Demonstration.png)


**Key Features**

Interactive Map: The application includes an interactive map powered by Google Maps. Users can visualize locations on the map, with each location marked by a pin. The API is called by using Axios.

Location Search: Users can search for specific locations using the search bar. On pressing 'Enter' or clicking the 'Search' button, the application utilizes Google Maps' Geocoding API to find the location and centers the map on this point.

Current Location: Users can also retrieve and display their current location on the map by clicking the 'Get Current Location' button. This feature utilizes the browser's Geolocation API.

Time Zone and Local Time Information: For every location marked on the map, the application fetches and displays time zone data and local time information using the Google Maps API. This data is displayed in a tabular format below the map.

Location Management: The displayed locations are listed in a data table with the ability to select and delete them. Users can multi-select locations and remove them using the 'Delete' button, which also removes the corresponding pins from the map.

Responsive Design: The application is designed to be responsive, ensuring a seamless user experience across various device sizes.

Error Handling: Adequate error handling is implemented to alert the user in case of issues like failure to fetch the current location, failure in location search, or issues in fetching time zone data.

Vue.js and Vuetify: The project is built using Vue.js, utilizing the Vuetify library to provide pre-designed Vue components for faster UI development.

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
