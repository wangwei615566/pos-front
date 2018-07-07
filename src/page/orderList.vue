<template>
    <div class="fillcontain">
        <head-top>
        </head-top>
        <div>
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;订单号:
            <el-input v-model="orderSearch" placeholder="请输入订单号" style="width:150px"></el-input>
            <el-button type="primary" @click="doFilter">搜索</el-button>
        </div>
        <div class="table_container">
            <el-table
                :data="tableData"
                style="width: 100%">
                <el-table-column
                    label="订单号"
                    prop="orderNo">
                </el-table-column>
                <el-table-column
                    label="总价格"
                    prop="amount">
                </el-table-column>
                <el-table-column
                    label="创建时间"
                    prop="createTime">
                </el-table-column>
                <el-table-column label="操作" show-tooltip-when-overflow>
                    <template scope="scope">
                       <el-button type="primary" size="small" @click="handleEdit(scope.$index, scope.row)">修改</el-button>
                       <el-button type="info" size="small" @click="query(scope.$index, scope.row)">详情</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <div class="Pagination" style="text-align: left;margin-top: 10px;">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="10"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>
            <el-dialog title="修改订单信息" v-model="visible">
                <el-form :model="selectTable">
                    <el-form-item label="订单号" label-width="100px">
                        <el-input v-model="selectTable.orderNo" auto-complete="off"></el-input>
                    </el-form-item>
                    <!--<el-form-item label="详细地址" label-width="100px">-->
                        <!--<el-autocomplete-->
                            <!--v-model="address.address"-->
                            <!--:fetch-suggestions="querySearchAsync"-->
                            <!--placeholder="请输入地址"-->
                            <!--style="width: 100%;"-->
                            <!--@select="addressSelect"-->
                        <!--&gt;</el-autocomplete>-->
                        <!--<span>当前城市：{{city.name}}</span>-->
                    <!--</el-form-item>-->
                    <el-form-item label="总价格" label-width="100px">
                        <el-input v-model="selectTable.amount"></el-input>
                    </el-form-item>
                    <el-form-item label="创建时间" label-width="100px">
                        <el-input v-model="selectTable.createTime"></el-input>
                    </el-form-item>
                    <!--<el-form-item label="店铺分类" label-width="100px">-->
                        <!--<el-cascader-->
                            <!--:options="categoryOptions"-->
                            <!--v-model="selectedCategory"-->
                            <!--change-on-select-->
                        <!--&gt;</el-cascader>-->
                    <!--</el-form-item>-->
                    <!--<el-form-item label="商铺图片" label-width="100px">-->
                        <!--<el-upload-->
                            <!--class="avatar-uploader"-->
                            <!--:action="baseUrl + '/v1/addimg/shop'"-->
                            <!--:show-file-list="false"-->
                            <!--:on-success="handleServiceAvatarScucess"-->
                            <!--:before-upload="beforeAvatarUpload">-->
                            <!--<img v-if="selectTable.image_path" :src="baseImgPath + selectTable.image_path" class="avatar">-->
                            <!--<i v-else class="el-icon-plus avatar-uploader-icon"></i>-->
                        <!--</el-upload>-->
                    <!--</el-form-item>-->
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button @click="visible = false">取 消</el-button>
                    <el-button type="primary" @click="updateOrder">确 定</el-button>
                </div>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {orderList,orderSave} from '../api/getData'

    export default {
        data() {
            return {
                tableData: [],
                currentRow: null,
                pageSize: 10,
                count: 0,
                currentPage: 1,
                selectTable: {},
                visible:false,
                orderSearch:'',
            }
        },
        components: {
            headTop,
        },
        created() {
            this.initData();
        },
        mounted() {

        },
        methods: {
            initData() {
                try {
                    const params = {
                        searchParams: "",
                        pageSize: this.pageSize,
                        current: this.currentPage
                    }
                    this.getOrders(params);
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },
            doFilter(){
                try {
                    const param = {
                        searchParams: JSON.stringify({orderNo:this.orderSearch}),
                        pageSize: this.pageSize,
                        current: this.currentPage
                    }
                    this.getOrders(param);
                } catch (err) {
                    console.log('获取数据失败', err);
                }
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                const params = {
                    searchParams: JSON.stringify({orderNo:this.orderSearch}),
                    pageSize: this.pageSize,
                    current: val
                }
                this.getOrders(params)
            },
            getOrders(params = {}) {
                orderList(params).then(result => {
                    if (result.code == 200) {
                        this.count = result.page.total;
                        this.$message({
                            type: 'success',
                            message: result.msg
                        });
                        this.tableData = result.data;
                    } else {
                        this.$message({
                            type: 'error',
                            message: result.msg
                        });
                    }
                })
            },
            handleEdit(index, row) {
                this.selectTable = row;
                this.visible = true;
            },
            updateOrder(){
                this.visible = false;
                try{
                    Object.assign(this.selectTable);
                    let searchParams = {searchParams:JSON.stringify(this.selectTable)}
                    orderSave(searchParams).then(result => {
                        if (result.code == 200) {
                            this.count = result.page.total;
                            this.$message({
                                type: 'success',
                                message: result.msg
                            });
                            this.tableData = result.data;
                        } else {
                            this.$message({
                                type: 'error',
                                message: result.msg
                            });
                        }
                    })
                }catch(err){
                    console.log('更新订单信息失败', err);
                }
            },
        },
    }
</script>

<style lang="less">
    @import '../style/mixin';
    .table_container {
        padding: 20px;
    }

    .demo-table-expand {
        font-size: 0;
    }

    .demo-table-expand label {
        width: 90px;
        color: #99a9bf;
    }

    .demo-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
</style>
