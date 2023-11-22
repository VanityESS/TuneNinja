<template>
  <div class="hello">
    <button @click="getToken">Get Token</button>
    <button @click="getArtistInfo">Get Artist Info</button>

    <div v-if="artistInfo">
      <h2>{{ artistInfo.name }}</h2>
      <p>{{ artistInfo.genres.join(', ') }}</p>
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
        console.log('Access Token:', accessToken);
        // Perform further actions with the access token if needed
      } catch (error) {
        console.error('Error getting token:', error);
      }
    },
    async getArtistInfo() {
      const artistId = '4wyNyxs74Ux8UIDopNjIai';
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
    } 
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
