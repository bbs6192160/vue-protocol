<template>
  <div>
    <v-treeview 
        v-if="showTree"
        v-model="selection"
        :items="protcolTree"
        :active.sync="active"
        :open.sync="open"
        item-key="name"
        
        selectable
        return-object
        open-on-click
        open-all
        dense
    ></v-treeview>
  </div>
</template>

<script>
//import shortid from "shortid"; // for tree id
export default {
    props:['items'],
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
        },
        selection(v){
            window.console.log(v);
            let res = [];
            for(let it of v){
                res.push(it.name)
            }
            this.$emit("change",res);
        },
    },
    computed:{
        protcolTree()
        {
            let res = [];
            if(!this.items)
            return res;
            for(let prot of this.items){
                let d = {name:prot.name,children:[]};
                if(prot.segments)
                {
                    for(let field of prot.segments){
                    let children = {name:prot.name + '-' + field.name}
                    d.children.push(children);
                    }
                }
                    res.push(d);
                }
            return res;
        },
    },
    data(){
        return{
            selection:[],
            active:[],
            open:[],
            showTree:false,
        }
    }
}
</script>