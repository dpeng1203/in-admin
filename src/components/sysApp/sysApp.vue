<template>
    <div class="mer-manage">
        <div class="title">
            <span>产品管理</span>
        </div>  
        <div class="search">
            <div class="search-ct">
                <div class="search-name">状态</div>
                <el-select v-model="data.status" placeholder="请选择">
                    <el-option
                    v-for="item in options1"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                    </el-option>
                </el-select>
            </div>
            <div class="search-ct">
                <div class="search-name">应用名称</div>
                <el-input class="inline-input" v-model="data.app_name" placeholder="请输入内容" clearable></el-input>
                <div class="search-btn" @click="searchBtn">搜索</div>
                <div class="search-btn" @click="addBtn">新增</div>
            </div>
        </div>

        <div class="table">
            <el-table
                :data="tableData"
                size="small"
                border
                style="width: 100%">
                <el-table-column
                    type="index"
                    width="50">
                </el-table-column>
                <el-table-column
                    prop="create_time"
                    label="创建时间"
                    width="200">
                </el-table-column>
                <el-table-column
                    prop="app_name"
                    label="应用名称"
                    width="200">
                </el-table-column>
                <el-table-column
                    prop="rate"
                    label="费率（%）"
                    width="150">
                </el-table-column>
                <el-table-column
                    prop="status"
                    label="状态"
                    width="150">
                </el-table-column>
                <el-table-column
                label="操作"
                >
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small">修改</el-button>
                </template>
                </el-table-column>
            </el-table>
            <div class="block">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-sizes="[10,20,50,100, 200, 300, 400]"
                    :page-size="data.limit"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
                </el-pagination>
            </div>
        </div>

    </div>
</template>

<script>
import changeData from '../../config/formatData'
import { sysApp } from '../../config/api'
export default {
    name: 'accountManage',
    data() {
        return{
            tableData: [],
            currentPage: 1,
            total: 0,
            options1: [{
                value: null,
                label: '请选择'
                },{
                value: '0',
                label: '关闭'
                }, {
                value: '1',
                label: '开启'
                }],
            data: {
                app_name: null,
                status: null,
                offset: 0,
                limit: 10
            }
        }
    },
    methods: {
        getList() {
            if(this.data.app_name == '') {
                delete this.data.app_name
            }
            sysApp(this.data).then((res) => {
                this.total = res.data.total_count
                this.tableData = res.data.data_list
                this.tableData.forEach( ele => {
                    if(ele.status && ele.status == true) {
                        ele.status = '开启'
                    }else {
                        ele.status = '关闭'
                    }
                    if(ele.rate) {
                        ele.rate = ele.rate/100
                    }
                    if( ele.create_time ) {
                        ele.create_time = changeData(ele.create_time)
                    }
                })
                console.log(res)
            })
        },

        searchBtn() {
            this.data.offset = 0
            this.getList()
        },

        handleClick(row) {
            console.log(row);
            this.$router.push({path: '/home/changeSysApp',query: {detail: row}})
        },

        handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
            this.data.limit = val
            this.getList()
        },
        handleCurrentChange(val) {
            console.log(`当前页: ${val}`);
            this.data.offset = (val - 1) * this.data.limit
            this.getList()
        },
        addBtn() {
            this.$router.push('/home/addSysApp')
        }

    },
    mounted() {
        this.getList()
    }
}
</script>

<style lang='sass' scoped>
.mer-manage
    color: #3D4060;
    padding-left: 30px
    .title 
        font-size: 20px
        font-weight: bold
    .search
        display: flex
        margin-top: 10px
        .search-ct
            margin-left: 20px
            .search-name
                font-size: 14px
                line-height: 18.2px
                padding-bottom: 10px
            .inline-input
                width: 200px
            .search-btn
                display: inline-block
                width: 80px
                height: 35px
                margin-top: 60px
                line-height: 35px
                text-align: center
                color: #fff
                background: #00BFA6;
                border-radius: 25px;
                font-size: 14px
                margin: 0 0 0 30px
        .search-ct:first-child
            margin-left: 0
    .table
        margin-top: 40px
        width: 1002px
        .block
            padding: 30px 0
            text-align: center 
</style>
