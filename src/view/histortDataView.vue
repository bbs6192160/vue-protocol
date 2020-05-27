<template>
    <div>
        <v-row>
            <v-col cols="3">
                <ProjectTree :items="items" @change="selectChange"/>
                <ProtocolTree :items="protocols" @change="protocolChange"/>
            </v-col>
            <v-col>
                <DataTable :headers="headers" :items="source" :perPage="perPage"  :length="pageCount" @change="PageChange"/>
            </v-col>
        </v-row>
    </div>
</template>
<script>
import shortid from "shortid"; // for tree id
import axios from "axios"

import ProjectTree from "@/components/ProjectTree"
import ProtocolTree from '@/components/ProtocolTree';
import DataTable from '@/components/DataTable';

axios.defaults.baseURL = "http://192.168.3.215:47112"

export default {
    components:{
        ProjectTree,
        ProtocolTree,
        DataTable,
    },
    watch:{
        projects(v)
        {
            //获取的项目数据转成项目树
            for(let project in v)
            {
                let proj = v[project];
                let children = [];
                for(let it of proj){
                    if(it.project == project){
                        let res = this.findTestCase(children,it.testcase);
                        res.children.push({id:it.idx,name:it.timestamp});
                    }
                }
                this.items.push({id:shortid.generate(),name:project,children:children});
            }
        }
    },
    methods:{
        selectChange(item)
        {
            //console.log(item);
            this.curRuntime = item.id;
            if(item.id){
                this.getSourceSize();
                this.getSource();
                this.getProtocols();
            }else{
                //重置数据
                this.source = [];
                this.protocols = [];
                this.pageCount = 0;
            }
        },
        protocolChange(item)
        {
            this.headers = item;
        },
        PageChange(v)
        {
            this.page = v;
            this.getSource()
        },
        getProtocols()
        {
            axios.get(`/api/recorded/protocols?runtime=${this.curRuntime}`)
            .then(res=>{
                this.protocols = res.data.data;
            });
        },
        getRuntimes()
        {
            // let items = [
            //     {time:"2020-05-19 15:22",project:"项目1",testcase:"用例1",idx:"1"},
            //     {time:"2020-05-19 16:22",project:"项目1",testcase:"用例1",idx:"2"},
            //     {time:"2020-05-19 17:22",project:"项目1",testcase:"用例1",idx:"3"},
            //     {time:"2020-05-19 18:22",project:"项目2",testcase:"用例1",idx:"4"},
            //     {time:"2020-05-19 19:22",project:"项目2",testcase:"用例2",idx:"5"},
            //     {time:"2020-05-19 20:22",project:"项目2",testcase:"用例3",idx:"6"},
            //     {time:"2020-05-19 21:22",project:"项目3",testcase:"用例1",idx:"7"},
            // ];
            
            axios.get("/api/recorded/runtimes")
            .then(res=>{
                let items = res.data.data;
                //提取项目节点
                for(let item of items)
                {
                    if(!this.projects[item.project])
                        this.$set(this.projects,item.project,[])
                    this.projects[item.project].push(item);
                }
                //console.log(this.projects);
            });
        },
        findTestCase(items,testcase)
        {
            for(let item of items)
            {
                if(item.name == testcase)
                    return item;
            }
            //没有则添加节点
            let res = {id:shortid.generate(),name:testcase,children:[]};
            items.push(res);
            return res;
        },
        getSourceSize()
        {
            axios.get(`/api/recorded/records/size?runtime=${this.curRuntime}`)
            .then(res=>{
                //window.console.log(res.data.data);
                let size = res.data.data.size;
                this.pageCount = Math.ceil(size / this.perPage);
            });
        },
        getSource()
        {
            axios.get(`/api/recorded/records?runtime=${this.curRuntime}&index=${this.perPage*(this.page -1 )}&limit=${this.perPage}`)
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
                this.source = data;
            });            
        }
    },
    created(){
        this.getRuntimes();
    },
    data()
    {
        return{
            items:[],
            projects:{},
            headers:[],
            source:[],
            protocols:[],
            curRuntime:"",
            page:1,
            pageCount:0,
            perPage:30,
        }
    }
}
</script>