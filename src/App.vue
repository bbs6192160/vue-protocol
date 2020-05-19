<template>
  <v-app>
    <v-content>
      <!-- <RunTimeView/> -->
      <histortDataView/>
    </v-content>
  </v-app>
</template>

<script>
//import RunTimeView from '@/view/RunTimeView';
import histortDataView from '@/view/histortDataView';

export default {
  name: 'App',

  components: {
    //RunTimeView,
    histortDataView,
  },

  data: () => ({
    //
  }),
};
</script>
