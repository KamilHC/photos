<template>
  <div>
    <nav class="navbar">
      <h1>Flicker find</h1>
      <form class="searchbar">
        <label>
          <span class='screen-reader-only'>Search:</span>
          <input 
            v-model="tag" 
            placeholder=""
            type="text" 
            class="searchbar-input">
        </label>
        <button 
          type="submit" 
          class="btn btn--orange btn--go" 
          @click.prevent="search">
            Search
        </button>
      </form>
    </nav>
    <div class="wrapper">
      <p v-if="loading" class="text-centered">
        Loading...
      </p>
      <ul v-else class="image-card-grid">
        <image-card
          v-for="image in images"
          :key="image.id"
          :image="image" />
      </ul>
    </div>
  </div>
</template>

<script>
import config from '../../config';
import axios from 'axios';
import ImageCard from '@/components/ImageCard';
export default {
  name: 'home',
  components: {
    ImageCard
  },
  data() {
    return {
      loading: false,
      tag: '',
      images: []
    }
  },
  methods: {
    search() {
      this.loading = true;
      this.fetchImages()
        .then((response) => {
          this.images = response.data.photos.photo;
          this.loading = false;
        })
        .catch((error) => {
          console.log("An error ocurred: ", error);
        })
    },
    fetchImages() {
      return axios({
        method: 'get',
        url: 'https://api.flickr.com/services/rest',
        params: {
          method: 'flickr.photos.search',
          api_key: config.api_key,
          tags: this.tag,
          extras: 'url_n, owner_name, date_taken, views',
          page: 1,
          format: 'json',
          nojsoncallback: 1,
          per_page: 9,
        }
      })
    },
  }
};
</script>

<style lang="scss">
.screen-reader-only {
  height: 1px;
  width: 1px;
  position: absolute;
  left: -100000px;
}
.text-centered {
  text-align: center;
}
.wrapper {
  margin: 0 auto;
  max-width: 800px;
  @media only screen and (max-width: 799px) {
    max-width: 100%;
    margin: 0 1.5rem;
  }
}
.image-card-grid {
  list-style: none;
  margin: .5rem 0;
  padding: 0;
  display: flex;
  align-items: flex-start;
  flex-wrap: wrap;
}
.navbar {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  background: white;
}
.searchbar {
  width: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  @media only screen and (max-width: 549px) {
    width: 100%;
    label {
      width: 80%;
    }
  }
}
.searchbar-input {
  padding: .5rem 1rem;
  border-radius: 5px;
  font-size: 1rem;
  border: 1px solid gray;
  min-width: 200px;

  @media only screen and (max-width: 549px) {
    min-width: 0;
    width: 100%;
  }

  &:focus {
      outline: none;
    }
}
.btn {
  padding: .5rem 1rem;
  font-size: 1rem;
  border-radius: 5px;
  background: transparent;
  border: 1px solid gray;
  outline: none;

  &:hover {
    opacity: 0.8;
    cursor: pointer;
  }
}
.btn--orange {
  background: gray;
  color: white;
  font-weight: bold;
}
.btn--go {
  padding: .5rem 2rem;
  margin-left: 1rem;
}
</style>