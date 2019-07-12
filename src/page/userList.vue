<template>
    <div class="fillcontain">
        <head-top></head-top>
        <div class="table_container">
            <input id="testInput" style="display: none;" type="file" @change="getValUpload($event)">
            <el-button @click="submitUpload" style="margin-bottom:10px;background: rgba(0, 204, 255, 1)!important;border: none;" type="primary" size="small">上传文件</el-button>
            <el-table
                stripe
                :data="tableData"
                show-summary
                highlight-current-row
                style="width: 100%">
                <el-table-column
                  type="index"
                  label="序号"
                  width="100">
                </el-table-column>
                <el-table-column
                    width="160"
                    prop="店铺"
                    label="店铺">
                </el-table-column>
                <el-table-column
                    prop="订单类型"
                    label="订单类型">
                </el-table-column>
                <el-table-column
                    prop="买家账号"
                    label="买家账号">
                </el-table-column>
                <el-table-column
                    prop="收件人姓名"
                    label="收件人姓名">
                </el-table-column>
                <el-table-column
                    prop="省"
                    label="省份">
                </el-table-column>
                <el-table-column
                    prop="线上订单号"
                    label="线上订单号">
                </el-table-column>
                <el-table-column
                    prop="快递公司"
                    label="快递公司">
                </el-table-column>
                <el-table-column
                    prop="重量"
                    label="重量">
                </el-table-column>
                <el-table-column
                    prop="运费支出"
                    label="运费支出">
                </el-table-column>
                <!--<el-table-column-->
                    <!--fixed="right"-->
                    <!--label="操作"-->
                    <!--width="100">-->
                    <!--<template slot-scope="scope">-->
                        <!--<el-button type="text" size="small">详情</el-button>-->
                    <!--</template>-->
                <!--</el-table-column>-->
            </el-table>
            <!--<div class="Pagination" style="text-align: left;margin-top: 10px;">-->
                <!--<el-pagination-->
                  <!--@size-change="handleSizeChange"-->
                  <!--@current-change="handleCurrentChange"-->
                  <!--:current-page="currentPage"-->
                  <!--:page-size="20"-->
                  <!--layout="total, prev, pager, next"-->
                  <!--:total="count">-->
                <!--</el-pagination>-->
            <!--</div>-->
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {getUserList, getUserCount} from '@/api/getData'
    const xlsx = require('xlsx')
    console.log(xlsx)
    export default {
        data(){
            return {
                tableData: [],
                currentRow: null,
                offset: 0,
                limit: 20,
                count: 0,
                currentPage: 1,
                lessFivemoney: 0,
                money: 0,
                noSameList: [],
                totalMoney: 0
            }
        },
    	components: {
    		headTop,
    	},
        created(){
            let arr = [
                {
                    no: 123,
                    w:20
                },
                {
                    no: 121231223,
                    w:21
                },
                {
                    no: 123,
                    w:23
                },
                {
                    no: 121231223,
                    w:24
                },
                {
                    no: 123,
                    w:25
                },
                {
                    no: 121212331223,
                    w:26
                },
                {
                    no: 1213,
                    w:27
                },
                {
                    no: 123,
                    w:22
                }
            ]
            let aaaa = []
            aaaa = arr.filter((e, idx) => {
                console.log(e)
                return e.no !== 123
            })
            console.log(aaaa, 12312312)
        },
        methods: {
            calValue(arr) {
                for (let i = 0; i < arr.length; i++) {}
            },
            tableRowClassName({row, rowIndex}) {
                console.log(row, rowIndex)
                if (rowIndex === 1) {
                    return 'warning-row';
                } else if (rowIndex === 3) {
                    return 'success-row';
                }
                return '';
            },
            submitUpload() {
                document.getElementById('testInput').click()
            },
            provincePrice(val, w) {
                this.money = 0
                let weight = Math.ceil(w)
                if (weight === 0) {
                    this.money += 0
                } else {
                    switch (val) {
                        case '广东省':
                            this.money = 5 + (weight - 3) * 1
                            break;
                        case '江苏省':
                        case '浙江省':
                        case '上海':
                        case '广西壮族自治区':
                        case '江西省':
                        case '福建省':
                        case '湖南省':
                        case '湖北省':
                            this.money = 5 + (weight - 3) * 4
                            break;
                        case '河南省':
                        case '河北省':
                        case '海南省':
                        case '山东省':
                        case '北京':
                        case '天津':
                            this.money = 5 + (weight - 3) * 5
                            break;
                        case '四川省':
                        case '重庆':
                        case '贵州省':
                        case '云南省':
                        case '山西省':
                        case '陕西省':
                        case '黑龙江省':
                        case '辽宁省':
                        case '吉林省':
                            this.money = 5 + (weight - 3) * 6
                            break;
                        case '甘肃省':
                        case '青海省':
                        case '宁夏回族自治区':
                            this.money = 5 + (weight - 3) * 10
                            break;
                        case '内蒙古自治区':
                        case '西藏自治区':
                        case '新疆维吾尔自治区':
                            this.money = 5 + (weight - 3) * 12
                            break;
                        default:
                            this.money = 0
                    }
                }
                console.log(this.money, '大于3公斤');
                return this.money
            },
            lessFive(w) {
                this.lessFivemoney = 0
                if (w === 0) {
                    this.lessFivemoney += 0
                } else {
                    this.lessFivemoney += 5
                }
                return this.lessFivemoney
            },
            getValUpload(event) {
                let shangzhiFile = event.target.files[0]  // 上传的文件
                let param = new FormData() // 创建form对象
                param.append('file', shangzhiFile, shangzhiFile.name) // 通过append向form对象添加数据
                this.readWorkbookFromLocalFile(shangzhiFile, (data) => {
                    console.log(data)
                })
            },
            readWorkbookFromLocalFile(file, callback) {
                let _this = this
                var reader = new FileReader()
                reader.onload = function (e) {
                    var data = e.target.result
                    var workbook = xlsx.read(data, {type: 'binary'})
                    let dataList = xlsx.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]])
                    let table = xlsx.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]])
                    table.pop()
                    _this.tableData = table
                    console.log(_this.tableData, 'yinggaishi quanbu de ')
                    dataList.pop()
                    console.log(dataList, 'result')
                    let theSameExpressNoList = []
                    let haveNoSameExpressNoList = []
                    _this.totalMoney = 0
                    let resultSamaList = []
                    // 把相同单号的分离出来
                    for (let i = 0; i < dataList.length; i++) {
                        for (let j = i +1; j < dataList.length; j++) {
                            if (dataList[i]['快递单号'] === dataList[j]['快递单号']) {
                                resultSamaList.push(dataList[i],dataList[j])
                            }
                        }
                    }
                    theSameExpressNoList = [...new Set(resultSamaList)] // 相同的数组
                    for (let w = 0; w < dataList.length; w++) { // dataList里面要去除相同的数组
                        for (let h = 0; h < theSameExpressNoList.length; h++) {
                            if (dataList[w]['快递单号'] === theSameExpressNoList[h]['快递单号']) {
                                dataList.splice(w, 1)
                            }
                        }
                    }
                    haveNoSameExpressNoList = dataList
                    console.log(theSameExpressNoList, 'sfjsldkfjldskjflsdkjfl skdjfl')
                    console.log(haveNoSameExpressNoList, ' skdjfl')
                    // 相同单号的计算重量并计算快递价格
                    if (theSameExpressNoList.length) {
                        let weight = 0
                        for (let i = 0; i < theSameExpressNoList.length; i++) {
                            for (let j = i +1; j < theSameExpressNoList.length; j++) {
                                if (theSameExpressNoList[i]['快递单号'] === theSameExpressNoList[j]['快递单号']) {
                                    weight = Math.ceil(theSameExpressNoList[i]['重量'] + theSameExpressNoList[j]['重量'])
                                    if (weight > 3) {
                                        _this.totalMoney += _this.provincePrice(theSameExpressNoList[i]['省'],weight)
                                    } else {
                                        _this.totalMoney += _this.lessFive(weight)
                                    }
                                }
                            }
                        }
                    }
                    // 去除相同快递单号的数据
                    for (let i = 0; i < haveNoSameExpressNoList.length; i++) {
                        if (Math.ceil(haveNoSameExpressNoList[i]['重量']) <= 3) {
                            _this.totalMoney += _this.lessFive(haveNoSameExpressNoList[i]['重量'])
                        } else {
                            _this.totalMoney += _this.provincePrice(haveNoSameExpressNoList[i]['省'], haveNoSameExpressNoList[i]['重量'])
                        }
                    }
                    // _this.downloadExl(dataList, 'sheet')
                    console.log(_this.totalMoney, '总价钱')
                    console.log(theSameExpressNoList, '相同的')
                    console.log(haveNoSameExpressNoList, '没有相同的')
                    if (callback) callback(workbook)
                }
                reader.readAsBinaryString(file)
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.offset = (val - 1)*this.limit;
            }
        },
    }
</script>

<style lang="less">
	@import '../style/mixin';
    .table_container{
        padding: 20px;
    }
    .el-table .warning-row {
        background: oldlace;
    }

    .el-table .success-row {
        background: #f0f9eb;
    }
</style>
