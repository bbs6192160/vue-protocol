<template>
    <div>
        <v-data-table
            :headers="_headers"
            :items="_items"
            :items-per-page="_perPage"
            :page.sync="page"
            class="elevation-1"
        ></v-data-table>
    </div>
</template>
<script>
export default {
    props:['headers','items','perPage'],
    watch:{
        _items(v)
        {
            //获取数据后,默认跳转到最后一页
            let page = 1;
            let len = v.length;
            if(len){
                page = len / this._perPage;
                if(len % this._perPage)
                    page++;
            }
            this.page = page;
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
        _lastPage()
        {
            let res = 1;
            let len = this._items.length;
            if(len){
                res = len / this._perPage;
                if(len % this._perPage)
                    res++;
            }
            return res;
        },
    },
    methods:{
        LastPage()
        {
            this.page = this._lastPage();
        },
    },
    data(){
        return{
            page:1,
        }
    }
}
</script>