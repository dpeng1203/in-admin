<template>
    <div class="mer-manage">
        <div class="title">
            <span>充值记录</span>
        </div>  
        <div class="search">
            <div class="search-ct">
                <div class="search-name">商户号</div>
                <el-input class="inline-input" v-model="data.mch_id" placeholder="请输入商户号" clearable></el-input>
                <div class="search-btn" @click="searchBtn">搜索</div>
            </div>
        </div>

        <div class="table">
            <el-table
                :data="tableData"
                size="small"
                border
                style="width: 100%">
                <el-table-column property="mch_id" label="商户号" width="80"></el-table-column>
                <el-table-column property="account_name" label="姓名" width="80"></el-table-column>
                <el-table-column property="account_no" label="银行卡号" width="160"></el-table-column>
                <el-table-column property="recevie_bank" label="备付金账户标识" width="120"></el-table-column>
                <el-table-column property="auth_name" label="操作人" width="100"></el-table-column>
                <el-table-column property="money" label="金额" width="100"></el-table-column>
                <el-table-column property="type" label="类型" width="130"></el-table-column>
                <el-table-column property="status" label="状态" width="100"></el-table-column>
                <el-table-column property="create_time" label="创建时间"></el-table-column>
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
import { rechargeList } from '../../config/api'
export default {
    name: 'record',
    data() {
        return{
            tableData: [],
            currentPage: 1,
            total: 0,
            data: {
                mch_id: '',
                offset: 0,
                limit: 10
            }
        }
    },
    methods: {
        getList() {
            if(this.data.mch_id == '') {
                delete this.data.mch_id
            }
            rechargeList(this.data).then((res) => {
                this.tableData = res.data.data_list
                this.total = res.data.total_count
                this.tableData.forEach( ele => {
                    if(ele.money) {
                        ele.money = ele.money/100
                    }
                    ele.create_time = changeData(ele.create_time)
                    if(ele.type == 1) {
                        if(ele.recevie_bank&&ele.recevie_bank!='') {
                            ele.type = '线下充值-商户'
                        }else{
                            ele.type = '线下充值-系统'
                        }
                    }else if(ele.type == 2) {
                        ele.type = '余额转换'
                    }
                    if(ele.status == 1) {
                        ele.status = '进行中'
                    }else if(ele.status == 2) {
                        ele.status = '充值失败'
                    }else if(ele.status == 3) {
                        ele.status = '充值成功'
                    }
                    if(ele.recevie_bank === 'UPOPJS') {
                        ele.recevie_bank = '银联'
                    }else if(ele.recevie_bank === 'NUCC') {
                        ele.recevie_bank = '网联'
                    }
                })
            })
        },

        searchBtn() {
            this.data.offset = 0
            this.getList()
        },

        handleClick(row) {
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
        width: 1050px
        .block
            padding: 30px 0
            text-align: center 
</style>
