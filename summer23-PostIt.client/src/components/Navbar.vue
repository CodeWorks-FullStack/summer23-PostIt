<template>
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark px-3">
    <router-link class="navbar-brand d-flex" :to="{ name: 'Home' }">
      <div class="d-flex flex-column align-items-center">
        <img alt="logo" src="../assets/img/cw-logo.png" height="45" />
      </div>
    </router-link>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarText"
      aria-controls="navbarText" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarText">
      <ul class="navbar-nav me-auto">
        <!-- <li>
          <router-link :to="{ name: 'About' }" class="btn text-success lighten-30 selectable text-uppercase">
            About
          </router-link>
        </li> -->
        <li>
          <button v-if="account.id" class="btn btn-success" type="button" data-bs-toggle="modal"
            data-bs-target="#createAlbumModal">
            <i class="mdi mdi-plus-box"></i> new album
          </button>
        </li>
        <li>
          <button v-if="route.name == 'Album' && account.id && account.id == album?.creatorId" @click="archiveAlbum()"
            class="btn btn-success ms-2" type="button">
            <i class="mdi mdi-close-circle text-danger"></i> archive album
          </button>
        </li>
      </ul>
      <!-- LOGIN COMPONENT HERE -->
      <Login />
    </div>
  </nav>
</template>

<script>
import { useRoute } from 'vue-router';
import Login from './Login.vue';
import Pop from '../utils/Pop.js';
import { albumsService } from '../services/AlbumsService.js';
import { computed } from 'vue';
import { AppState } from '../AppState.js';
export default {
  setup() {
    const route = useRoute()
    return {
      route,
      account: computed(() => AppState.account),
      album: computed(() => AppState.activeAlbum),
      async archiveAlbum() {
        try {
          const wantsToArchive = await Pop.confirm()

          if (!wantsToArchive) {
            return
          }

          const albumId = route.params.albumId
          await albumsService.archiveAlbum(albumId)
        } catch (error) {
          Pop.error(error.message)
        }
      }
    }
  },
  components: { Login }
}
</script>

<style scoped>
a:hover {
  text-decoration: none;
}

.nav-link {
  text-transform: uppercase;
}

.navbar-nav .router-link-exact-active {
  border-bottom: 2px solid var(--bs-success);
  border-bottom-left-radius: 0;
  border-bottom-right-radius: 0;
}

@media screen and (min-width: 768px) {
  nav {
    height: 64px;
  }
}
</style>
