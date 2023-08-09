!<template>
  <v-tour name="myTour" :steps="steps"></v-tour>
</template>


<script>
import { AppState } from '../AppState';
import { computed, reactive, onMounted } from 'vue';
import { logger } from '../utils/Logger.js';
export default {
  name: 'my-tour',
  setup(){
  return {  
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
          content: `Click on an <strong>Album Card</strong> to open up its <strong>Album Details</strong> on a new page`
        }
      ]
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