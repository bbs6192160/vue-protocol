<template>
  <div>
    <v-row>
      <v-col cols="3">
        <v-treeview 
					v-if="showTree"
          v-model="selection"
          :items="protcolTree"
          selectable
          return-object
					open-all
        ></v-treeview>
      </v-col>
      <!-- <v-divider vertical></v-divider> -->
      <v-col>
        <v-data-table
          :headers="headers"
          :items="desserts"
          :items-per-page=15
          class="elevation-1"
        ></v-data-table>
      </v-col>
    </v-row>

  </div>
</template>

<script>
import shortid from "shortid";
export default {
		props:['protocol','source'],
		watch:{
			protcolTree(v){
				//协议树更新,然后 selection选中所有的protcolTree
				//window.console.log(v);
         for(let it of v){
            for(let children of it.children){
              this.selection.push(children);
          }
				}
				//获取到数据后才去渲染组件
				this.showTree = true;
			}
		},
    computed:{
      headers()
      {
        let res = [];
        res.push({text:"Time",value:"time"});
        for(let s of this.selection) {
            res.push({text:s.name,value:s.name});
        }
        return res;
      },
      desserts()
      {
				//window.console.log(this.source.data);
        if(this.source && this.source.data)
					return this.source.data;
        return [];
      },
      protcolTree()
      {
				let res = [];
        if(!this.protocol)
					return res;
				for(let prot of this.protocol){
					let d = {id:shortid.generate(),name:prot.name,children:[]};
					for(let field of prot.segments){
						let children = {id:shortid.generate(),name:prot.name + '-' + field.name}
						d.children.push(children);
					}
					res.push(d);
				}
        return res;
      },

    },
    data(){
      return{
					selection:[],
					showTree:false,
      }
    }
}
</script>