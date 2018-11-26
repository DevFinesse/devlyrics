<template>
  <div class="home">
    <nav>
      <div class="nav-wrapper teal darken-4">
        <router-link to="/" class="brand-logo center"
          >Devfinesse Lyrics Finder</router-link
        >
      </div>
    </nav>
    <div class="row col s12 all container">
      <div class="col s12">
        <div class="row">
          <div class="input-field col s12">
            <input
              type="text"
              v-on:keyup.enter="getDetails"
              v-model="title"
              required
            />
            <label>Song Title</label>
          </div>
        </div>
      </div>
      <div class="col s12">
        <ul class="collection with-header" v-if="result">
          <li class="collection-header text-center teal">
            <h4>Lyrics Results</h4>
          </li>
          <li class="collection-item" v-for="results in result">
            <div class="center">
              {{ results.track.artist_name }} - {{ results.track.track_name }}
              <p class="center">
                <a
                  @click="getLyrics(results.track.artist_name, results.track.track_name)"
                  class="waves-effect waves-light btn-small"
                  >Get Lyrics</a
                >
              </p>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="container">
      <div class="card-panel red darken-2" v-if="errored">
        <h4 class="center">Lyrics not found</h4>
      </div>
    </div>

    <div class="container">
      <div class="card-panel teal lighten-2" v-if="lyrics">
        <h6>{{ lyrics }}</h6>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: "home",
    data() {
      return {
        artiste: "",
        title: "",
        result: null,
        errored: false,
        lyrics: "",
        style: {
          marginTop: "10px"
        }
      };
    },
    methods: {
      getDetails() {
        axios
          .get(
            `https://cors-anywhere.herokuapp.com/http://api.musixmatch.com/ws/1.1/track.search?q_track=${
              this.title
            }&page_size=10&page=1&s_track_rating=desc&apikey=7adbf01b5869db197cef63173d01b2c3`
          )
          .then(
            response => (this.result = response.data.message.body.track_list)
          );
      },
      getLyrics(name, song_title) {
        axios
          .get(`https://api.lyrics.ovh/v1/${name}/${song_title}`)
          .then(
            response => (this.lyrics = response.data.lyrics),
            (this.result = null)
          )
          .catch(error => {
            this.errored = true;
          });
      }
    }
  };
</script>

<style scoped>
  .all {
    margin-top: 50px;
  }
  h4 {
    text-align: center;
  }
</style>
