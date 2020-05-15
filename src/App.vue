<template>
  <v-app>
    <v-content>
      <RunTimeView/>
    </v-content>
  </v-app>
</template>

<script>
import RunTimeView from '@/view/RunTimeView';

export default {
  name: 'App',

  components: {
    RunTimeView,
  },

  data: () => ({
    //
  }),
};
</script>
