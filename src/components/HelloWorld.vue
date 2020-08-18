<template>
    <div class="container">
        <div>
            <h1>拼图</h1>
        </div>
        <a-row>
            <a-col :span="8">
                <a-select
                    :default-value="svalue"
                    @change="handleChange"
                    style="width: 100%;"
                    v-model="svalue"
                    :options="assets"
                >
                </a-select>
            </a-col>
            <a-col :span="16" v-show="svalue == 'customQQ'">
                <a-input-search
                    placeholder="请输入输入QQ号码"
                    size="default"
                    v-model="qq"
                >
                    <a-button
                        slot="enterButton"
                        :disabled="qq != '' ? false : true"
                        @click.native="getQQUrl"
                    >
                        开始
                    </a-button>
                </a-input-search>
            </a-col>
        </a-row>
        <div class="hello" :key="svalue">
            <div v-show="visible"  class="defaultImg">
                <img  :src="imgUrl" alt="" />
                <div>{{timer}}</div>
            </div>
            <draggable
                class="main"
                ghost-class="ghost"
                :animation="400"
                @end="moveEvent"
            >
                <li
                    class="list-group-item"
                    v-for="(element, idx) in list"
                    :key="idx"
                >
                   <!--  <img :src="element.url" alt="" /> -->
                   <canvas class="item" :id="'mycnavas_'+element.id" :sortid="element.id" width="100" height="100"></canvas>
                </li>
            </draggable>
        </div>
        <div>
            <h3>第{{ step }}步</h3>
            <h3>{{ msg }}</h3>
            <a-button type="dashed" @click="start">开始</a-button>
        </div>
        <div class="note" v-if="false">
            游戏说明：空格键space 暂停游戏,方向键开始游戏
        </div>
        <canvas v-show="false" id="mycnavas" width="100" height="100"></canvas>
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
            svalue: "youting",
            imgUrl: "",
            qq: "",
            visible:true,
            assets:[
                {
                    value:'q1',
                    lable:'q',
                    title:'萨摩耶',
                    url:require('../assets/dog.jpg')
                },
                {
                    value:'youting',
                    lable:'youting',
                    title:'游艇',
                    url:require('../assets/youting.jpg')
                },
                {
                    value:'customQQ',
                    lable:'q',
                    title:'QQ头像',
                    url:"require('../assets/qq.jpg')"
                }
            ],
            list: [
                {
                    id: 0,
                },
                {
                    id: 1,
                },
                {
                    id: 2,
                },
                {
                    id: 3,
                },
                {
                    id: 4,
                },
                {
                    id: 5,
                },
                {
                    id: 6,
                },
                {
                    id: 7,
                },
                {
                    id: 8,
                },
            ],
            step: 0, //步数
            msg: "", //描述信息
            timer:"",//计时器
        };
    },
    watch: {
        svalue(newval,oldval){
            if(newval==="customQQ"){
                if(this.qq!==""){
                    this.getQQUrl()
                }
            }else{
                this.assets.map(item=>{
                if(item.value === newval){
                    this.imgUrl = item.url
                }
                })
                this.start()
            }
        }
    },
    created() {
        this.svalue = this.assets[0].value
        this.imgUrl = this.assets[0].url
        //this.getImageBlob("http://q1.qlogo.cn/g?b=qq&nk=1793371376&s=640&a=" +new Date().getTime())
    },
    methods: {
        start(){
            window.clearInterval(timer)
            this.visible=true
            this.step=0;
            this.msg="";
            this.list = this.randomArray(this.list);
            this.picCut9(this.imgUrl) 
            setTimeout(()=>{
                this.visible=false
            },3000)
            this.timer=3;
            let timer = setInterval(()=>{
                this.timer--;
                if(this.timer<=0){
                    window.clearInterval(timer)
                }
            },1000)
        },
        //打乱数组的顺序
        randomArray(arr) {
            return arr.sort(function () {
                return Math.random() > 0.5 ? -1 : 1;
            });
        },
        //拖动事件
        moveEvent(){
            this.step++;
            let arr =  Array.from(document.getElementsByClassName("item")).map(item=>{
                return parseInt(item.getAttribute("sortid"))
            })
            this.result(arr)
        },
        //判断结果
        result(data) {
            let arr = [...data];
            let bool = true;
            arr.sort(function (a, b) {
                if (b > a) {
                    bool = false;
                }
            });
            if (bool) {
                this.msg = "恭喜你拼图完成,共使用" + this.step + "步！";
                alert("恭喜你拼图完成,共使用" + this.step + "步！");
            }
        },
        //获取qq头像
        getQQUrl() {
            this.imgUrl =
                "http://q1.qlogo.cn/g?b=qq&nk=" +
                this.qq +
                "&s=640&a=" +
                new Date().getTime();
                this.start()
        },
        handleChange() {},
        //九宫格切图
        picCut9(imgurl) {
            let mycnavas = document.getElementById("mycnavas"); //现将图片放上去
            let ctx = mycnavas.getContext("2d");
            ctx.clearRect(0,0,300,300);
            let img = new Image();
            img.onload = function () {
                let imgwidth = img.width
                let len = imgwidth / 3;
                let arr = [];
                for (let i = 0; i < imgwidth; ) {
                    for (let j = 0; j < imgwidth; ) {
                        arr.push({
                            x: j,
                            y: i,
                        });
                        j = j + len;
                    }
                    i = i + len;
                }
                arr = arr.map((item,k) => {
                    let canvas = document.getElementById("mycnavas_"+k); //现将图片放上去
                    let ctx = canvas.getContext("2d");
                    ctx.drawImage(
                        img,
                        item.x,
                        item.y,
                        len,
                        len,
                        0,
                        0,
                        100,
                        100
                    );
                    return item
                });
            };
            img.src = imgurl
        },
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  scoped>
.container {
    width: 300px;
    text-align: center;
    position: absolute;
    left: 0;
    right: 0;
    margin: auto;
    top: 100px;
}
.hello {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    margin: 20px 0;
}
.main {
    display: flex;
    flex-wrap: wrap;
    width: 300px;
    box-shadow: 0 0 2px 2px #929090;
    border-radius: 3px;
}
.defaultImg{
    position: absolute;
}
.defaultImg img{
    width: 300px;
    height: 300px;
}
.defaultImg div{
    position: absolute;
    top: 60px;
    font-size: 100px;
    margin: auto;
    text-align: center;
    left: 0;
    right: 0;
    color: cyan;
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
