<template>
  <input
    v-model="username"
    type="text"
    placeholder="Enter Instagram Username"
    name="username"
    class="inputbox"
    autocomplete="off"
    autocorrect="off"
    autocapitalize="off"
  />
  <button @click="getUserStories" type="submit">View Story</button>
  <p class="errortext">{{errorText}}</p>
  <div ref="loading" class="hidden loading-banner">
    <p>Loading.. Please wait</p>
  </div>
  <ul class="stories" ref="stories">
    <li v-for="(story, index) in stories" :key="index">
      <p>{{ new Date(story.taken_at * 1000) }}</p>
      <a :href="story.contentUrl" target="_blank" class="storybutton">
        <div v-if="story.isVideo == true">
          <img :src="require('../assets/play-button.png')" class="playbutton" />
          <video :src="story.contentUrl" alt="" crossorigin="anonymous" />
        </div>
        <img v-else :src="story.contentUrl" alt="" crossorigin="anonymous" class="storyimg" />
      </a>
    </li>
  </ul>
</template>

<script>
import axios from "axios";

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
      const storiesbox = this.$refs.stories;
      this.errorText = "";
      loader.classList.remove('hidden');
      storiesbox.classList.add('hidden')
      
      const options = {
        method: 'GET',
        url: process.env.VUE_APP_HOST_URL,
        params: {ig: this.username.toLowerCase(), response_type: 'story', corsEnabled: 'true'},
        headers: {
          'X-RapidAPI-Key': process.env.VUE_APP_API_KEY,
          'X-RapidAPI-Host': process.env.VUE_APP_API_HOST
        }
      };

      axios
        .request(options)
        .then((response) => {
          loader.classList.add('hidden');
          storiesbox.classList.remove('hidden')
          let storyData = response.data[0].story.data;

          if (storyData.length > 0) {
            storyData.forEach(function(story){
              if(story.media_type == 2) {
                story.contentUrl = story.video_versions[0].url
                story.isVideo = true;
              } else {
                story.contentUrl = story.image_versions2.candidates[0].url
              }
            })
            this.stories = storyData;
          } else {
            this.errorText = "The user has no stories available or is a private account.";
          }
        })
        .catch((error) => {
          this.errorText = "An error occurred. Check the username is spelled correctly. " + error;
        });
    }
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
  display: inline-block;
  width: 20rem;
}
.storybutton .storyimg, .storybutton video {
  width: 100%;
}
.playbutton {
  width: 100px;
  height: 100px;
  position: absolute;
  z-index: 100000000;
  right: 0;
  left: 0;
  margin: auto;
  margin-top: 150px;
}
</style>
