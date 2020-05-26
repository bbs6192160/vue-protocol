<template>
    <div>
        <v-data-table
            :headers="_headers"
            :items="_items"
            :items-per-page="_perPage"
            hide-default-footer
            class="elevation-1"
            @page-count="pageCount = $event"
        ></v-data-table>
        <v-pagination v-model="page" v-if="length" :length="length" total-visible="10"></v-pagination>
    </div>
</template>
<script>
//:page.sync="page"
//@page-count="pageCount = $event"
export default {
    props:['headers','items','perPage','length'],
    watch:{
        page(v)
        {
            //console.log(v);
            this.$emit("change",v);
        },
        length(v)
        {
            //跳转到最后一页
            this.page = v;
        }
    },
    computed:{
        _headers()
        {
            let res = [{text:"Time",value:"time"}];
            if(this.headers){
                //window.console.log(this.selection);
                for(let s of this.headers) {
                    res.push({text:s,value:s});
                }
            }
            return res;
        },
        _items()
        {
            if(this.items)
                return this.items;
            return [];
        },
        _perPage()
        {
            if(this.perPage)
                return this.perPage;
            return 20;
        },
    },
    data(){
        return{
            page:this.initPage?this.initPage:1,
            pageCount:0,
        }
    }
}
</script>