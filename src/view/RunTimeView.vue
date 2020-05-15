<template>
  <v-app>
    <v-content>
      <ProtocolTree :protocol="protocol" :source="source"/>
    </v-content>
  </v-app>
</template>

<script>
import ProtocolTree from '@/components/ProtocolTree';
import axios from 'axios'

axios.defaults.baseURL = "http://192.168.3.215:47112"

export default {
  components:{
    ProtocolTree
  },
  data(){
    return{
        protocol:[],
        source:{data:[]},
    }
  },
  created(){
    this.getProtocols();
    this.getSource();
  },
  methods:{
    getProtocols()
    {
        axios.get("/api/recorded/protocols")
          .then(res=>{
            this.protocol = res.data.data;
        });
    },
    getSource()
    {
        axios.get("/api/recorded/records?runtime=seg_test")
          .then(res=>{
            //window.console.log(res.data.data);
            let data = [];
            for(let it of res.data.data)
            {
              let row = {};
              this.$set(row,"time",it.timestamp);
              //获取协议值
              let protocol = it[it.path];
              for(let field in protocol){
                let name = it.path + "-" + field;
                this.$set(row,name,protocol[field]);
              }
              data.push(row);
            }
            this.source.data = data;   
        });
    }
  }
}
</script>