<template>
  <div class="hello">
    <input v-model="searchQuery" placeholder="Search for an artist">
    <button @click="searchArtist">Search</button>

    <div v-if="searchResults.length > 0">
      <h2>Search Results:</h2>
      <ul>
        <li v-for="result in searchResults" :key="result.id">
          <span>{{ result.name }}</span>
          <button @click="getArtistInfo(result.id)">Get Artist Info</button>
        </li>
      </ul>
    </div>

    <div v-if="artistInfo">
      <h2>{{ artistInfo.name }}</h2>
      <p>Artist ID: {{ artistInfo.id }}</p>
      <!-- Add other properties you want to display -->
    </div>

    <div v-if="error">
      <p>Error: {{ error }}</p>
    </div> 
  </div>
</template>

<script>
import axios from 'axios';
let accessToken='';

export default {
  data() {
    return {
      searchQuery: '',
      searchResults: [],
      artistInfo: null,
      error: null,
    };
  },
  methods: {
    async getToken() {
      const url = 'https://accounts.spotify.com/api/token';
      const headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
      };

      const data = new URLSearchParams();
      data.append('grant_type', 'client_credentials');
      data.append('client_id', '30a9caca1b0549dd91e747767a6879fc');
      data.append('client_secret', 'c4611d2c7da84c85b82d6611b5ad98ac');

      try {
        const response = await axios.post(url, data, { headers });
        // Access the access token from the response object
         accessToken = response.data.access_token;
        // Perform further actions with the access token if needed
      } catch (error) {
        console.error('Error getting token:', error);
      }
    },
    async getArtistInfo(artistId) {
      const url = `https://api.spotify.com/v1/artists/${artistId}`;

      const headers = {
        'Authorization': `Bearer ${accessToken}`,
      };

      try {
        const response = await axios.get(url, { headers });
        // Access the artist information from the response object
        this.artistInfo = response.data;
        console.log('Artist Information:', this.artistInfo);
        // Perform further actions with the artist information if needed
      } catch (error) {
        console.error('Error getting artist information:', error);
      }
    },
    async searchArtist() {
      this.getToken();
      const url = `https://api.spotify.com/v1/search`;

      const headers = {
        'Authorization': `Bearer ${accessToken}`,
      };

      const params = {
        q: this.searchQuery,
        type: 'artist',
      };

      try {
        console.log('starting request');
        const response = await axios.get(url, { headers, params });
        this.searchResults = response.data.artists.items;
        console.log(this.searchResults);
      } catch (error) {
        this.error = error.message || 'An error occurred while searching for artists';
        console.error('Error searching for artists:', error);
      }
    },
    mounted() {
    // Call your async method when the component is mounted
    },
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
