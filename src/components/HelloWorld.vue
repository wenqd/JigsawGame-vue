<template>
    <div>
        <div>
            <h1>拼图</h1>
        </div>
        <div class="hello">
            <draggable
                class="main"
                :list="list"
                ghost-class="ghost"
                :animation="400"
            >
                <li
                    class="list-group-item"
                    v-for="(element, idx) in list"
                    :key="idx"
                >
                    <img :src="element.url" alt="" />
                </li>
            </draggable>
        </div>
        <div>
            <h3>第{{step}}步</h3>
            <h3>{{msg}}</h3>
        </div>
        <div class="note" v-if="false">
            游戏说明：空格键space 暂停游戏,方向键开始游戏
        </div>
    </div>
</template>

<script>
import draggable from "vuedraggable";
export default {
    name: "HelloWorld",
    components: {
        draggable,
    },
    data() {
        return {
            list: [
                {
                    url: require('../assets/1/壁纸1_0.jpg'),
                    id: 0,
                },
                {
                    url: require('../assets/1/壁纸1_1.jpg'),
                    id: 1,
                },
                {
                    url: require('../assets/1/壁纸1_2.jpg'),
                    id: 2,
                },
                {
                    url: require('../assets/1/壁纸1_3.jpg'),
                    id: 3,
                },
                {
                    url: require('../assets/1/壁纸1_4.jpg'),
                    id: 4,
                },
                {
                    url: require('../assets/1/壁纸1_5.jpg'),
                    id: 5,
                },
                {
                    url: require('../assets/1/壁纸1_6.jpg'),
                    id: 6,
                },
                {
                    url: require('../assets/1/壁纸1_7.jpg'),
                    id: 7,
                },
                {
                    url: require('../assets/1/壁纸1_8.jpg'),
                    id: 8,
                },
            ],
            step:0,//步数
            msg:"",//描述信息
        };
    },
    watch: {
        list(){
            console.log("移动")
            this.result(this.list)
            this.step++
        }
    },
    mounted() {
        this.list = this.randomArray(this.list)
    },
    methods: {
        //打乱数组的顺序
        randomArray(arr){
            return arr.sort(function(){return Math.random()>0.5?-1:1;});
        },
        //判断结果
        result(data){
            let arr = [...data]
            let bool = true
            arr.sort(function(a,b){
                if(b.id>a.id){
                    bool = false
                }
            })
            if(bool){
                this.msg = "恭喜你拼图完成,共使用"+this.step+"步！"
                alert("恭喜你拼图完成,共使用"+this.step+"步！")
            }
        }
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  scoped>
.hello {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
}
.main {
    display: flex;
    flex-wrap: wrap;
    width: 300px;
}
.list-group-item {
    width: 100px;
    height: 100px;
    list-style: none;
    cursor: move;
}
.list-group-item img {
    width: 100%;
    height: 100%;
}
.note {
    margin: 40px;
    font-size: 12px;
}
.ghost {
    opacity: 0.5;
    background: #c8ebfb;
}
</style>
