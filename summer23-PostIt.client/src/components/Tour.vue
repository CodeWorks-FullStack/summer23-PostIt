!<template>
  <v-tour name="myTour" :steps="steps" :callbacks="tourCallBacks"></v-tour>
</template>


<script>
import { AppState } from '../AppState';
import { computed, reactive, onMounted } from 'vue';
import { logger } from '../utils/Logger.js';
import { accountService } from '../services/AccountService.js';
export default {
  name: 'my-tour',
  props:{
    steps: {type: Array, required: true},
    tourCallBacks: {type: Object, required: true}
  },
  setup(){
  return {  


  }
  },
    mounted: function(){
    logger.log('touring')
    this.$tours['myTour'].start()
  }
};
</script>


<style lang="scss" scoped>

</style>