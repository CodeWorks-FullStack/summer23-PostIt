<template>
  <!-- FIXME album collaborations go here-->
  <div class="container-fluid">
    <div class="row">
      <div class="col-3">
        <h1 class="text-light" id="v-step-0" >My Albums</h1>
      </div>
      <div class="col-md-3"></div>
    </div>
    <!-- <v-tour name="myTour" :steps="steps"></v-tour> -->
  </div>

  <div class="container">
    <div class="row">
      <div v-for="album in albums" :key="album.id" class="col-md-3" id="v-step-1">
        <!-- <div class="bg-light">
          <img :src="album.coverImg" :alt="album.title" class="img-fluid">
          <h2>{{ album.title }}</h2>
        </div> -->
        <AlbumCard :albumProp="album" />
      </div>
    </div>
  </div>

  <Tour />
</template>

<script>
import { computed, onMounted } from 'vue';
import Pop from '../utils/Pop.js';
import { albumsService } from '../services/AlbumsService.js'
import { AppState } from '../AppState.js';
// import { logger } from '../utils/Logger.js';

export default {
  // name: 'my-tour',
  setup() {

    async function getAlbums() {
      try {
        await albumsService.getAlbums()
      } catch (error) {
        Pop.error(error.message)
      }
    }
    onMounted(() => {
      getAlbums()
    })


    return {
      albums: computed(() => AppState.albums),

      // steps: [
      //   {
      //     target: '#v-step-0',
      //     header: {
      //       title: 'Get Started'
      //     },
      //     content: `Discover <strong>Vue Tour</strong>!`
      //   }
      // ]
    }
},
  // mounted: function(){
  //   logger.log('touring')
  //   this.$tours['myTour'].start()
  // }
}
</script>

<style scoped lang="scss"></style>
