<template>
        
    <el-menu
        mode="horizontal"
        :default-active="$route.path"
        class="el-menu-vertical-demo"
        @open="handleOpen"
        @close="handleClose"
        background-color="#34395D"
        text-color="#fff"
        
        router
        unique-opened
        active-text-color="#00B7AD">
        <el-menu-item index="/home/homeDetail" v-if="root != 2">
            <i class="el-icon-menu"></i>
            <span slot="title">主页</span>
        </el-menu-item>
            <el-menu-item index="/home/merManage" v-if="root != 2">
            <i class="el-icon-setting"></i>
            <span slot="title">商户管理</span>
        </el-menu-item>
        <el-submenu index="3" v-if="root != 2">
            <template slot="title">
                <i class="el-icon-goods"></i>
                <span>交易管理</span>
            </template>
            <el-menu-item index="/home/billList">
                <span slot="title">充值交易</span>
            </el-menu-item>
            <el-menu-item index="/home/payList">
                <span slot="title">代付交易</span>
            </el-menu-item>
        </el-submenu>
        <el-menu-item index="/home/sysApp" v-if="root != 2">
            <i class="el-icon-picture-outline"></i>
            <span slot="title">产品管理</span>
        </el-menu-item>
        <el-menu-item index="/home/cashList" v-if="root != 2">
            <i class="el-icon-printer"></i>
            <span slot="title">提现管理</span>
        </el-menu-item>
        <!-- <el-menu-item index="/home/agentList">
            <i class="el-icon-document"></i>
            <span slot="title">代理管理</span>
        </el-menu-item> -->
        <el-submenu index="7">
            <template slot="title">
                <i class="el-icon-goods"></i>
                <span>代理管理</span>
            </template>
            <el-menu-item index="/home/agentList">
                <span slot="title">支付代理商</span>
            </el-menu-item>
            <el-menu-item index="/home/agentPayList">
                <span slot="title">代付代理商</span>
            </el-menu-item>
        </el-submenu>

        <el-submenu index="5" v-if="root != 2">
            <template slot="title">
                <i class="el-icon-goods"></i>
                <span>通道管理</span>
            </template>
            <el-menu-item index="/home/channel">
                <span slot="title">充值通道</span>
            </el-menu-item>
            <el-menu-item index="/home/payBank">
                <span slot="title">代付通道</span>
            </el-menu-item>
        </el-submenu>
        <!-- <el-submenu index="6">
            <template slot="title">
                <i class="el-icon-goods"></i>
                <span>回调确认</span>
            </template>
            <el-menu-item index="/home/qrList">
                <span slot="title">收款码管理</span>
            </el-menu-item>
            <el-menu-item index="/home/callbackList">
                <span slot="title">回调列表</span>
            </el-menu-item>
            <el-menu-item index="/home/missList">
                <span slot="title">挂单列表</span>
            </el-menu-item>
        </el-submenu> -->
        <el-menu-item index="/home/zfbhb">
            <i class="el-icon-menu"></i>
            <span slot="title">支付宝H5</span>
        </el-menu-item>
        <el-menu-item index="/home/zfbToBank">
            <i class="el-icon-printer"></i>
            <span slot="title">支付宝转银行卡</span>
        </el-menu-item>
    </el-menu>
           
</template>

<script>
import Bus from './bus'
export default {
    name: 'tabs',
    data() {
        return{
            root: 1,     //  1:管理员  2：客服
            isCollapse:false
        }
    },
    methods: {
        handleOpen(key, keyPath) {
            console.log(key, keyPath)
        },
        handleClose(key, keyPath) {
            console.log(key, keyPath)
        }
    },
    mounted() {
        if(localStorage.rolesId == 1002) {
            this.root = 2
        }
        Bus.$on('log', content => { 
            this.isCollapse = content
        });
    }
}
</script>

<style lang="sass" scoped>
    // .el-menu-vertical-demo:not(.el-menu--collapse) 
    //     width: 220px;
    //     min-height: calc(100vh - 111px)
    //     flex-shrink: 0   
</style>


