<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <el-table :data="tableData" style="width: 2000px">
                <el-table-column
                    label="日期"
                    fixed
                    width="120"
                    prop="date">
                    <template slot-scope="scope">
                        {{ moment(scope.date).format('YYYY-MM-DD') }}
                    </template>
                </el-table-column>
                <el-table-column
                    label="产品型号"
                    width="120"
                    prop="model">
                </el-table-column>
                <el-table-column
                    label="订单号"
                    width="120"
                    prop="orderNo">
                </el-table-column>
                <el-table-column
                    label="销售额"
                    width="80"
                    prop="salesVolume">
                </el-table-column>
                <el-table-column
                    label="SLS买家运费"
                    width="120"
                    prop="buyerFreight">
                </el-table-column>
                <el-table-column
                    label="运费补贴"
                    width="120"
                    prop="freightSubsidy">
                </el-table-column>
                <el-table-column
                    label="SLS卖家"
                    width="120"
                    prop="sellerFreight">
                </el-table-column>
                <el-table-column
                    label="优惠券"
                    width="120"
                    prop="couponVolume">
                </el-table-column>
                <el-table-column
                    label="佣金"
                    width="120"
                    prop="commission">
                </el-table-column>
                <el-table-column
                    label="活动费率"
                    width="120"
                    prop="activityCommission">
                </el-table-column>
                <el-table-column
                    label="交易手续费"
                    width="120"
                    prop="transactionFee">
                </el-table-column>
                <el-table-column
                    label="实际收入"
                    width="120"
                    prop="income">
                </el-table-column>
                <el-table-column
                    label="汇率"
                    width="120"
                    prop="exchangeRate">
                </el-table-column>
                <el-table-column
                    label="实际订单收入（RMB）"
                    width="220"
                    prop="incomeRmb">
                </el-table-column>
                <el-table-column
                    label="购买成本（RMB）"
                    width="180"
                    prop="purchaseCost">
                </el-table-column>
                <el-table-column
                    label="售后反币/返券"
                    width="180"
                    prop="praiseRebate">
                </el-table-column>
                <el-table-column
                    label="货代费用（RMB）"
                    width="160"
                    prop="agencyFee">
                </el-table-column>
                <el-table-column
                    label="利润额（RMB）"
                    width="160"
                    prop="profit">
                </el-table-column>
                <el-table-column
                    label="销售额（RMB）"
                    width="180"
                    prop="salesVolumeRmb">
                </el-table-column>
                <el-table-column
                    label="利润率"
                    width="120"
                    prop="profitMargin">
                </el-table-column>
                <el-table-column
                    label="采购账号"
                    width="120"
                    prop="purchaseAccount">
                </el-table-column>
                <el-table-column
                    label="采购时间"
                    width="120"
                    prop="purchaseTime">
                </el-table-column>
                <el-table-column
                    label="快递单号"
                    fixed="right"
                    width="120"
                    prop="orderNo">
                </el-table-column>
            </el-table>
            <div class="Pagination" style="text-align: left;margin-top: 10px;">
                <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-size="20"
                    layout="total, prev, pager, next"
                    :total="count">
                </el-pagination>
            </div>
        </div>
    </div>
</template>

<script>
import headTop from '../components/headTop'
import moment from 'moment';
import {getOrderList, getOrderCount, getResturantDetail, getUserInfo, getAddressById} from '@/api/getData'

export default {
    data() {
        return {
            tableData: [],
            currentRow: null,
            offset: 0,
            limit: 20,
            count: 0,
            currentPage: 1,
            restaurant_id: null,
            expendRow: [],
            moment: moment
        }
    },
    components: {
        headTop,
    },
    created() {
        this.restaurant_id = this.$route.query.restaurant_id;
        this.initData();
    },
    mounted() {

    },
    methods: {
        async initData() {
            this.getOrders();
        },
        handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
        },
        handleCurrentChange(val) {
            this.currentPage = val;
            this.offset = (val - 1) * this.limit;
            this.getOrders()
        },
        async getOrders() {
            const Orders = await getOrderList({pageSize: 10, pageNum: 1});
            this.tableData = Orders.records;
        },
        async expand(row, status) {
            if (status) {
                const restaurant = await getResturantDetail(row.restaurant_id);
                const userInfo = await getUserInfo(row.user_id);
                const addressInfo = await getAddressById(row.address_id);

                this.tableData.splice(row.index, 1, {
                    ...row, ...{
                        restaurant_name: restaurant.name,
                        restaurant_address: restaurant.address,
                        address: addressInfo.address,
                        user_name: userInfo.username
                    }
                });
                this.$nextTick(() => {
                    this.expendRow.push(row.index);
                })
            } else {
                const index = this.expendRow.indexOf(row.index);
                this.expendRow.splice(index, 1)
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
