<template>
    <div class="mer-manage">
        <div class="title">
            <span>代付通道</span>
            <p>(备注：代付通道可同时开启多个，系统优先先锋通道)</p>
        </div>  
        <div class="table">
            <el-table
                :data="tableData"
                border
                size="small"
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
                    prop="name"
                    label="通道名称"
                    width="200">
                </el-table-column>
                <el-table-column
                    prop="code"
                    label="通道标识"
                    width="150">
                </el-table-column>
                <el-table-column
                    prop="state"
                    label="通道状态"
                    width="150">
                </el-table-column>
                <el-table-column
                label="操作"
                >
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="danger" size="mini">{{scope.row.state == '开启' ? '关闭' : '开启'}}</el-button>
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
import { payBankList,changePayBankState } from '../../config/api'
export default {
    name: 'payBank',
    data() {
        return{
            tableData: [],
            currentPage: 1,
            total: 0,
            data: {
                offset: 0,
                limit: 10
            }
        }
    },
    methods: {
        getList() {
            payBankList(this.data).then((res) => {
                this.total = res.data.total_count
                this.tableData = res.data.data_list
                this.tableData.forEach( ele => {
                    if(ele.state) {
                        ele.state = '开启'
                    }else {
                        ele.state = '关闭'
                    }
                    if( ele.create_time ) {
                        ele.create_time = changeData(ele.create_time)
                    }
                })
                console.log(res)
            })
        },

        handleClick(row) {
            this.$confirm('确定切换商户状态?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                let data = {
                    id: row.id,
                    is_open: row.state == '开启' ? false : true
                }
                changePayBankState(data).then( res => {
                    this.$message({
                        type: 'success',
                        message: '切换成功!'
                    });
                    this.getList()
                })
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消'
                });          
            });
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
        p
            font-size: 16px
            color: red
    .table
        margin-top: 40px
        width: 1002px
        .block
            padding: 30px 0
            text-align: center 
</style>
