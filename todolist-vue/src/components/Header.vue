<template>
    <div class="big-container" :style="bgColor">
        <div class="head-container" :style="bgColor">
        <el-menu :default-active="activeIndex" class="el-menu-demo" :style="bgColor" mode="horizontal" @select="handleSelect"
                 text-color="#646464"
                 active-text-color="#2F55D4">
            <el-button type="text" class="head-tag" @click="toIndex"><img src="../assets/geeker-logo.png"/><span>Geeker</span></el-button>
            <el-menu-item index="1" id="item-1" @click="toIndex">主页</el-menu-item>
            <el-menu-item index="2" id="item-2">我的任务</el-menu-item>
            <el-submenu index="3" id="item-3">
                <template slot="title">筛选</template>
                <el-menu-item index="3-1">今日</el-menu-item>
                <el-menu-item index="3-2">已完成</el-menu-item>
                <el-menu-item index="3-3">未完成</el-menu-item>
            </el-submenu>
            <el-menu-item index="4" id="item-4">详情</el-menu-item>
            <el-menu-item index="5" id="item-5">历史</el-menu-item>
            <el-submenu index="6" class="flexible-content">
                <template slot="title">📋</template>
                <el-menu-item index="6-1">主页</el-menu-item>
                <el-submenu index="6-2">
                    <template slot="title">筛选</template>
                    <el-menu-item index="6-2-1">今日</el-menu-item>
                    <el-menu-item index="6-2-2">已完成</el-menu-item>
                    <el-menu-item index="6-2-3">未完成</el-menu-item>
                </el-submenu>
                <el-menu-item index="6-3" >登录</el-menu-item>
            </el-submenu>

            <el-submenu index="7" id="item-login" style="float: right" v-if="this.$store.state.isLogin">
                <template slot="title">{{$store.state.user.email}}</template>
                <el-menu-item index="7-1"><i class="el-icon-user"></i>个人中心</el-menu-item>
                <el-menu-item index="7-2" @click="quit"><i class="el-icon-close"></i>退出登录</el-menu-item>
            </el-submenu>
            <el-button type="text" class="head-login" :style="bgColor" @click="loginEvent" id="item-login" v-if="!this.$store.state.isLogin">登录</el-button>

        </el-menu>
        <div class="line"></div>
    </div>
    </div>

</template>

<script>
    import router from "../router";
    export default {
        name: "Header",
        data() {
            return {
              activeIndex: '',bgColor:''
            }
        },
        mounted() {
            window.addEventListener('scroll',this.handleScroll,true);
        },
        created() {
            if (this.$route.path === "/index" || this.$route.path === "/") {
                this.bgColor = "background: #F8F9FC;"
                /*this.activeIndex = '1'*/
            } else {
                this.bgColor = ""
                console.log(this.$route.path)
                switch (this.$route.path) {
                    case "/tasks":this.activeIndex = '2'
                        break;
                }
            }
          let token = localStorage.getItem("userToken");
            let email = localStorage.getItem("email");
            let param = new FormData;
            param.append("token", token);
            if( token ) {
              this.$axios.post("/geeker/api/tasks", param).then(res=>{
                if(res.data.msg==="success") {
                  this.$store.commit("LOGIN");
                  this.$store.commit("SET_EMAIL", email);
                  this.$store.commit("SET_TOKEN", token);
                }
              })
            }
        },
        methods:{
            handleSelect(key, keyPath) {
                //console.log(key);
                switch (key) {
                    case "2":
                        this.$router.push("/tasks")
                        break;
                }
            },
            loginEvent() {
                router.push({name:"Login"});
            },
            toIndex() {
                router.push({name:'Index'});
            },
              quit(){
                this.$store.commit("QUIT");
                this.$router.push({name:"Index"});
              },
            handleScroll(){
                let scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
                if (scrollTop === 0 && (this.$route.path === '/' || this.$route.path === '/index')) {
                    this.bgColor = "background: #F8F9FC;"
                } else {
                    this.bgColor = ""
                }
            },

        }
    }
</script>

<style scoped>
    .big-container {
        width: 100%;
        background: white;
        z-index: 100;
        position: sticky;
        top: 0;
    }
    .head-container {
        max-width: 1080px;
        margin: 0 auto;
        height: 70px;
    }
    .head-tag {
        color: #409EFF;
        font-size: larger;
        font-weight: bolder;
        margin-top: 4px;
        width: 220px;
        height: 100%;
        float: left;
        margin-right: 30px;

    }
    .head-tag img {
        margin-top: -10px;
        margin-left: 40px;
        height: 50px;
        width: 50px;
        float: left;
    }
    .head-tag span {
        margin-left: 6px;
        margin-top: 8px;
        display:block;
        float: left;
        color: #2F55D4;
    }
    .el-menu-demo{
        width: 100%;
        float: left;
        height: 100%;
        /*background: #F8F9FC;*/
    }
    .head-login {
        float: right;
        width: 100px;
        margin-right: 20px;
        margin-top: 15px;
        color: #2F55D4;
        /*background: #F8F9FC;*/
    }
    .flexible-content {
        display: none;
        float: right;
        margin-left: 300px;
        margin-top: 7px;
    }
    @media (max-width: 768px) {
        .flexible-content {
            display: block;
        }
        .el-menu #item-1{
            display: none;
        }
        .el-menu #item-2{
            display: none;
        }
        .el-menu #item-3{
            display: none;
        }
        .el-menu #item-4{
            display: none;
        }
        .el-menu #item-5{
            display: none;
        }
        .el-menu #item-login{
            display: none;
        }
    }
    /*再修改 这样不好*/
    @media (max-width: 660px) {
        .flexible-content {
            display: block;
            margin-left: 200px;
        }
    }
    @media (max-width: 560px) {
        .flexible-content {
            display: block;
            margin-left: 140px;
        }
    }
    @media (max-width: 494px) {
        .flexible-content {
            display: block;
            margin-left: 100px;
        }
    }
    @media (max-width: 456px) {
        .flexible-content {
            display: block;
            margin-left: 70px;
        }
    }
    @media (max-width: 430px) {
        .flexible-content {
            display: block;
            margin-left: 50px;
        }
    }
    @media (max-width: 405px) {
        .flexible-content {
            display: block;
            margin-left: 20px;
        }
    }
    @media (max-width: 372px) {
        .flexible-content {
            display: block;
            margin-left: -10px;
        }
    }
</style>
