<template>
  <!-- FIXME album collaborations go here-->
  <div class="container-fluid">
    <div class="row">
      <div class="col-3">
        <h1 class="text-light" id="v-step-0" >My Albums</h1>
      </div>
      <div class="col-md-3" v-for="c in myCollabs" :key="c.id">
        <AlbumCard :albumProp="c.album" />
      </div>
    </div>
    <!-- <v-tour name="myTour" :steps="steps"></v-tour> -->
  </div>

  <div class="container">
    <div class="row">
      <div v-for="album in albums" :key="album.id" class="col-md-3">
      </div>  
    <div class="row justify-content-center">
      <div class="col-8">
        <div class="bg-info d-flex justify-content-around p-2 rounded">
          <button class="btn btn-outline-light" @click="filterBy = ''">All</button>
          <button class="btn btn-outline-light" @click="filterBy = 'dogs'">Dogs</button>
          <button class="btn btn-outline-light" @click="filterBy = 'animals'">Animals</button>
          <button class="btn btn-outline-light" @click="filterBy = 'games'">Games</button>
          <button class="btn btn-outline-light" @click="filterBy = 'gachamon'">Gachamon</button>
          <button class="btn btn-outline-light" @click="filterBy = 'misc'">Misc.</button>
        </div>
      </div>
    </div>
    <div class="row pt-3">
      <div v-for="album in albums" :key="album.id" class="col-md-3" id="v-step-1">
        <!-- <div class="bg-light">
          <img :src="album.coverImg" :alt="album.title" class="img-fluid">
          <h2>{{ album.title }}</h2>
        </div> -->
        <AlbumCard :albumProp="album" />
      </div>
    </div>

  </div>
  </div>

  <Tour v-if="account.needsTour" :steps="steps" :tourCallBacks="tourCallBacks"/>
</template>

<script>
import { computed, onMounted, ref } from 'vue';
import Pop from '../utils/Pop.js';
import { albumsService } from '../services/AlbumsService.js'
import { AppState } from '../AppState.js';
import { useRouter } from 'vue-router';
import { accountService } from '../services/AccountService.js';
import { logger } from '../utils/Logger.js';
// import { logger } from '../utils/Logger.js';

export default {
  // name: 'my-tour',
  setup() {
    const filterBy = ref('')
    const router = useRouter()
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
      filterBy,
      albums: computed(() => {
        if (filterBy.value == "") {
          return AppState.albums
        } else {
          return AppState.albums.filter(a => a.category == filterBy.value)
        }
      }),
      myCollabs: computed(() => AppState.myCollabs),
      account: computed(() => AppState.account),
      
      steps: [
        {
          target: '#v-step-0',
          header: {
            title: 'Welcome to PostIt!'
          },
          content: `Discover <strong>Vue Tour PostIt</strong>!`
        },
        {
          target: '#v-step-1',
          header: {
            title: 'Album Card'
          },
          content: `Click on an <strong>Album Card</strong> to open up its <strong>Album Details</strong> on a new page`,
        }
      ],

      
      tourCallBacks: {
        onFinish: (()=> {
          // console.log(AppState.albums[0]);
          // console.log(router);
          router.push({name: 'Album', params:{albumId: AppState.albums[0].id}})
        }),
        onSkip: (() => accountService.editAccount({needsTour: false}))
      },
    }
},
  // mounted: function(){
  //   logger.log('touring')
  //   this.$tours['myTour'].start()
  // }
}
</script>

<style scoped lang="scss"></style>
