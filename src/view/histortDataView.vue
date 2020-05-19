<template>
    <div>
        <ProjectTree :items="items" @change="selectChange"/>
    </div>
</template>
<script>
import shortid from "shortid"; // for tree id
import axios from "axios"
import ProjectTree from "@/components/ProjectTree"

axios.defaults.baseURL = "http://192.168.3.215:47112"

export default {
    components:{
        ProjectTree
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
            console.log(item);
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
                console.log(this.projects);
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
    },
    created(){
        this.getRuntimes();
    },
    data()
    {
        return{
            items:[],
            projects:{},
        }
    }
}
</script>