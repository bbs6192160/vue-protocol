<template>
  <div>
    <v-treeview
        :items="project"
        :active.sync="active"
        :open.sync="open"
        :load-children="loadChildren"
        
        activatable
        return-object
        open-on-click
        open-all
        dense
    ></v-treeview>
  </div>
</template>

<script>
export default {
    props:['items'],
    watch:{
        active(v)
        {
            let res = {};
            if(v.length){
                res = v[0];
            }
            this.$emit("change",res);
        },
        project(v)
        {
            //打开所有的用例节点
            for(let project of v)
            {
                let children = project.children;
                for(let it of children){
                    this.open.push(it);
                }
            }
        }
    },
    computed:{
        project()
        {
            let res = [];
            if(this.items)
                return this.items;
            return res;
        },
    },
    methods:{
        loadChildren(item)
        {
            console.log("loadChildren : " + item.name);
        }
    },
    data(){
        return{
            active:[],
            open:[],
            show:true,
        }
    }
}
</script>