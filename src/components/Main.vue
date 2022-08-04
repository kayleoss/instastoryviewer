<template>
  <input
    v-model="username"
    type="text"
    placeholder="Enter Instagram Username"
    name="username"
    class="inputbox"
  />
  <button @click="getUserStories" type="submit">View Story</button>
  <p class="errortext">{{errorText}}</p>
  <div ref="loading" class="hidden loading-banner">
    <p>Loading.. Please wait</p>
  </div>
  <ul class="stories">
    <li v-for="(story, index) in stories" :key="index">
      <p>{{ new Date(story.taken_at * 1000) }}</p>
      <a :href="story.contentUrl" target="_blank" class="storybutton">Click to view story {{index + 1}}</a>
    </li>
  </ul>
</template>

<script>
import axios from "axios";
import {creds} from "./credentials.js";

export default {
  name: "InstaStoryViewer",
  data() {
    return {
      errorText: "",
      stories: []
    }
  },
  methods: {
    getUserStories () {
      const loader = this.$refs.loading;
      this.errorText = "";
      loader.classList.remove('hidden');
      
      const options = {
        method: 'GET',
        url: creds.HOST_URL,
        params: {ig: this.username, response_type: 'story'},
        headers: {
          'X-RapidAPI-Key': creds.API_KEY,
          'X-RapidAPI-Host': creds.API_HOST
        }
      };

      axios
        .request(options)
        .then((response) => {
          loader.classList.add('hidden');
          if (response.data[0].story.data.length > 0) {
            response.data[0].story.data.forEach(function(story){
              if(story.media_type == 2) {
                story.contentUrl = story.video_versions[0].url
              } else {
                story.contentUrl = story.image_versions2.candidates[0].url
              }
            })
            this.stories = response.data[0].story.data;
          } else {
            this.errorText = "The user has no stories available or is a private account.";
          }
        })
        .catch((error) => {
          this.errorText = error;
        });
    },
  }
};
</script>

<style>
.inputbox {
  width: 50%;
  height: 50px;
  background: #defcf9;
  padding: 5px;
  border-radius: 10px;
}
button {
  padding: 15px;
  display: block;
  margin: 15px auto;
  border-radius: 10px;
  background: #7dd5cc;
  box-shadow: 2px 5px black;
}
button:hover {
  cursor: pointer;
}
.errortext {
  color: red;
  font-weight: 700;
}
.hidden {
  display: none;
}
.loading-banner {
  padding: 25px;
  border-radius: 15px;
  background: #C6FFF9;
  font-weight: 700;
}
ul {
  padding: 0;
}
.stories li {
  list-style-type: none;
  margin-top: 15px;
  background: #D5F8F4;
  padding: 5px;
}
.storybutton {
  border: 1px solid black;
  border-radius: 15px;
  display: inline-block;
  padding: 15px;
  text-decoration: none;
  font-weight: 700;
  background: lightgreen;
  color: white;
}
</style>
