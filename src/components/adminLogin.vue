<template>
    <div class="login">
           
        <div class="wrapper">
            <div class="title">Alian后台管理系统</div>
            
            <div>
                <input type="text" placeholder="手机号" v-model="account">
            </div>
            <div v-if="!showMes">
                <div class="input-wrap" v-if="eyeOpen">
                    <input type="password" placeholder="密码" v-model="pw" @keyup.enter="login">
                    <img src="../assets/img/eye_close.png" alt=""  @click="showPw" > 
                </div>
                <div class="input-wrap" v-if="!eyeOpen">
                    <input type="text" placeholder="密码" v-model="pw" @keyup.enter="login">
                    <img src="../assets/img/eye_open.png" alt="" @click="showPw" > 
                </div>
            </div>
            <div class="mes-login" v-if="showMes"> 
                <input type="text" placeholder="短信验证码" v-model="code" class="mes-input">
                <span v-show="show" @click="getCode" class="get-code">获取验证码</span>
                <span v-show="!show" class="count">{{count}} s</span>
            </div>
            <div class="btn" @click="showMes = !showMes" style="margin-top: 40px">切换登录方式</div>
            <div class="btn" @click="login">登录</div>
        </div>
    </div>    
</template>

<script>
import {login,code} from '../config/api'
export default {
    name: 'adminLogin',
    data() {
        return{ 
            show: true,      //60秒倒计时
            count: '',
            timer: null,
            showMes: true,     //登录方式
            eyeOpen: true,
            account: '',
            pw: '',
            code: ''         //短信码
        }
    },
    methods: {
        getCode() {
            if(this.account == '') {
                this.$message.error('请输入手机号')
                return false
            }
            const TIME_COUNT = 60;
            if (!this.timer) {
                this.count = TIME_COUNT;
                this.show = false;
                let data = {
                    phone: this.account
                }
                code(data).then( res => {
                    this.$message.success('请查收短信！')
                })
                this.timer = setInterval(() => {
                if (this.count > 0 && this.count <= TIME_COUNT) {
                    this.count--;
                    } else {
                    this.show = true;
                    clearInterval(this.timer);
                    this.timer = null;
                    }
                }, 1000)
            }
            
        },
        showPw() {
            this.eyeOpen = !this.eyeOpen
        },
        login() {
            localStorage.clear()
            if(this.account == '') {
                this.$message.error('请输入手机号')
                return false
            }
            let data = {}
            if(this.showMes) {
                if(this.code== '') {
                    this.$message.error('请输入短信验证码！')
                    return
                }
                data = {
                    phone: this.account,
                    code: this.code
                }
            }else{
                if(this.pw == '') {
                    this.$message.error('请输入密码!')
                    return false
                }
                data = {
                    phone: this.account,
                    password: this.pw
                }
            }
            login(data).then((res) => {
                console.log(res)
                localStorage.id = res.id
                localStorage.nickname = res.nickname
                if(res.base.mch_name) {
                    localStorage.name = res.base.mch_name
                }
                if(res.roles && res.roles.length != 0) {
                    res.roles.forEach(ele => {
                        if(ele.id == 1002) {
                            localStorage.rolesId = 1002
                        }
                    });
                }
                this.$router.push('/home')
            })
        },
    }
}
</script>

<style lang="sass" scoped>
.login
    width: 100%;
    height: 100vh
    background-image: url(../assets/img/background.jpg)
    background-size: 100% 100%;
    position: relative;
    
    .wrapper
        position: absolute
        top: 50%
        left: 50%
        margin-top: -190px
        margin-left: -185px
        width: 370px
        background: rgba(255,255,255, 0.2)
        border-radius: 5px
        padding: 40px 30px
        text-align: center
        .title 
            font-size: 20px
            font-weight: bold
            padding-bottom: 10px
        input
            border: 1px solid #B1B3C1;
            border-radius: 5px;
            font-size: 16px
            width: 100%
            height: 40px
            line-height: 40px
            margin-top: 20px
            padding-left: 20px
        .input-wrap
            position: relative
            img
                position: absolute
                right: 10px
                top: 32px
                width: 20px
                height: 20px
        .mes-login
            margin-top: 20px
            .mes-input
                width: 70%
                margin-top: 0px
            span
                display: inline-block
                font-size: 14px
                border: 1px solid #B1B3C1;
                width: 27%
                line-height: 38px
                margin-top: 0px
                border-radius: 5px;
            .get-code
                cursor: pointer
        .btn
            margin-top: 10px
            background: #00BFA6
            border: 1px solid #B1B3C1;
            border-radius: 5px;
            color: #fff
            height: 40px
            font-size: 16px
            line-height: 40px
            cursor: pointer
</style>
