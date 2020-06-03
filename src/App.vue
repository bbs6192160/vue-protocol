<template>
  <v-app>
    <v-content>
    <v-tabs
     v-model="panel"
     background-color="transparent"
    >
      <v-tab
        v-for="item in items"
        :key="item.title"
      >
        {{ item.title }}
      </v-tab>
    </v-tabs>

      <v-tabs-items v-model="panel">
      <v-tab-item
        v-for="(item,index) in items"
        :key="item.title"
      >
      <RunTimeView v-if="index==0"/>
      <histortDataView v-if="index==1"/>

      </v-tab-item>
    </v-tabs-items>
    </v-content>
  </v-app>
</template>

<script>
import RunTimeView from '@/view/RunTimeView';
import histortDataView from '@/view/histortDataView';

export default {
  name: 'App',

  components: {
    RunTimeView,
    histortDataView,
  },

  data: () => ({
    //
    panel:null,
    items:[{title:'实时数据'},{title:'历史数据'}],
  }),
};
</script>
