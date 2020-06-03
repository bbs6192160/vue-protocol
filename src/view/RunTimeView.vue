<template>
  <div>
      <v-row>
        <v-col cols='3'>
          <ProtocolTree :items="protocols" @change="selectChange"/>
        </v-col>
        <v-col>
          <DataTable :headers="headers" :items="items" :perPage="perPage"/>
        </v-col>
      </v-row>
  </div>
</template>

<script>
import ProtocolTree from '@/components/ProtocolTree';
import DataTable from '@/components/DataTable';
import axios from 'axios'

axios.defaults.baseURL = "http://192.168.3.215:47112"

export default {
  components:{
    ProtocolTree,
    DataTable,
  },
  data(){
    return{
        protocols:[],
        headers:[],
        items:[],
        perPage:20,
        revData:[],
        reqParams:{refresh:'true',from:0,index:0},
    }
  },
  created(){
    this.getProtocols();
    this.getSource();
  },
  methods:{
    selectChange(v)
    {
      this.headers = v;
    },
    getProtocols()
    {
        axios.get("/api/recorded/protocols")
          .then(res=>{
            this.protocols = res.data.data;
        });
    },
    getSource()
    {
        axios.get("/api/recorded/records",{params:this.reqParams})
          .then(res=>{
            //window.console.log(res.data);
            if(res.data.upperIdx){
              if(res.data.upperIdx <= this.reqParams.index)
                this.getProtocols();//刷新协议
              this.reqParams.index =  res.data.upperIdx;
            }
            //console.log(this.reqParams.index);
            if(res.data.data.length)
            {
              for(let it of res.data.data)
              {
                let row = {};
                this.$set(row,"time",it.timestamp);
                //获取协议值
                let protocol = it[it.path];
                if(!protocol)
                for(let field in protocol){
                  let name = it.path + "-" + field;
                  this.$set(row,name,protocol[field]);
                }
                this.revData.push(row);
              }
              this.items = this.items.concat(this.revData).slice(-this.perPage);
            }
            //继续刷新
            this.getSource();
        });
    },
  }
}
</script>