<template>
  <div class="container-fluid">
    <div class="row">
      <!-- SECTION Album details -->
      <div v-if="album" class="col-md-3" id="v-step-2" >
        <div class="d-flex justify-content-between align-items-start">
          <img :src="album.coverImg" :alt="album.title" class="cover-img rounded light-shadow">
          <div class="rounded bg-warning p-1 light-shadow">
            <p class="fs-4 fw-bold">
              {{ album.title }}
            </p>
            <p class="fs-4">
              by: {{ album.creator.name }}
            </p>
          </div>
          <div>
            <!-- TODO disable or HIDE button if not album creator OR collaborator -->
            <button :disabled="album.archived == true" data-bs-toggle="modal" data-bs-target="#createPictureModal"
              class="btn btn-info rounded text-white" id="v-step-3"><i class="mdi mdi-plus-outline"></i> add picture</button>
          </div>
        </div>
        <div class="d-flex pt-2">
          <div class="rounded bg-info light-shadow p-2">
            <h2>Collaborators: {{ album.memberCount }}</h2>
          </div>
          <button v-if="!isCollaborator" class="btn btn-secondary" @click="becomeCollaborator()">Collab <i
              class="mdi mdi-heart"></i></button>
          <button v-else class="btn btn-secondary" @click="removeCollaborator()">Uncollab <i
              class="mdi mdi-heart-broken"></i></button>
        </div>
        <div class="d-flex pt-3">
          <img class="collab-img mx-1" v-for="c in collaborators" :src="c.profile?.picture" alt="">
        </div>
      </div>
      <!-- SECTION Album pictures -->
      <div class="col-md-9">
        <div class="row">
          <!-- REVIEW these two lines were how we created a 'dummy' for-loop to test our styling -->
          <!-- <div class="col-3 p-2" v-for="n in 10"> -->
          <!-- <img class="album-picture" :src="pictures[0].imgUrl" alt=""> -->
          <div class="col-3 p-2" v-for="p in pictures" :key="p">
            <img class="album-picture" :src="p.imgUrl" alt="">
          </div>
        </div>
      </div>
    </div>
  </div>

  <Tour v-if="account.needsTour" :steps="steps" :tourCallBacks="tourCallBacks"/>
</template>


<script>
import { computed, onMounted, watchEffect } from 'vue';
import { useRoute } from 'vue-router';
import Pop from '../utils/Pop.js';
import { albumsService } from '../services/AlbumsService.js';
import { picturesService } from '../services/PicturesService.js';
import { collaboratorsService } from '../services/CollaboratorsService.js'
import { AppState } from '../AppState.js';
import { logger } from '../utils/Logger.js';
import { accountService } from '../services/AccountService.js';

export default {

  setup() {
    const route = useRoute()

    async function getAlbumById(albumId) {
      try {
        // const albumId = route.params.albumId
        await albumsService.getAlbumById(albumId)
      } catch (error) {
        Pop.error(error.message)
      }
    }

    async function getPicturesByAlbumId() {
      try {
        const albumId = route.params.albumId
        await picturesService.getPicturesByAlbumId(albumId)
      } catch (error) {
        logger.error(error)
        Pop.toast(error.message, 'error')
      }
    }

    async function getCollaboratorsByAlbumId() {
      try {
        const albumId = route.params.albumId
        await collaboratorsService.getCollaboratorsByAlbumId(albumId)
      } catch (error) {
        logger.error(error)
        Pop.toast(error.message, 'error')
      }
    }

    // onMounted(() => {
    //   getAlbumById()
    // })

    watchEffect(() => {
      getAlbumById(route.params.albumId)
      getPicturesByAlbumId()
      getCollaboratorsByAlbumId()
    })

    return {
      album: computed(() => AppState.activeAlbum),
      pictures: computed(() => AppState.pictures),
      collaborators: computed(() => AppState.albumCollabs),
      isCollaborator: computed(() => AppState.albumCollabs.find(c => c.id == AppState.account.id)),
      account: computed(()=> AppState.account),

      steps: [
        {
          target: '#v-step-2',
          header: {
            title: 'Album Details'
          },
          content: `The Album Details provides information such as the creator and album coverImg`
        },
        {
          target: '#v-step-3',
          header: {
            title: 'Post Picture'
          },
          content: `Add a picture to this album`
        }
      ],

      tourCallBacks: {
        onFinish: (() => accountService.editAccount({needsTour: false})),
        onSkip: (() => accountService.editAccount({needsTour: false}))
      },
    }
  }
}
</script>


<style lang="scss" scoped>
.cover-img {
  max-height: 20vh;
  width: 40%;
}

.album-picture {
  height: 35vh;
  width: 25vh;
  object-fit: cover;
  object-position: center;
  border-radius: 5px;
  box-shadow: 2px 2px white;
}

.collab-img {
  height: 10vh;
  width: 10vh;
  border-radius: 5px;
  box-shadow: 2px 2px white;

}

.picture-btn {
  width: 100%;
  border-radius: 5px;
  border: none;
  padding: .5em;
  color: white;
  background-color: #CA51A8;
  font-weight: bold;
}
</style>