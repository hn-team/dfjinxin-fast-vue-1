<style lang="scss">
    .view-global{
        .el-popup-parent--hidden{
            padding-right: 0 !important;
        }
        .el-input--medium .el-input__inner{
            height: 30px;
        }
        .el-form-item__content{
            margin-left: 0!important;
        }
        .el-form-item{
            margin-bottom: 16px;
        }
        .tbl-result-border{
            padding-left: 10px;
            padding-right: 4px;
            border:1px solid #eeeeee;
        }
        .div-grid-bg{
            background-color: #eceff2;
            padding: 10px;
        }
        .tab-name-font{
            font-size: 16px;
            font-weight:bold;
            height: 38px;
        }
        .del-icon-pointer{
            cursor: pointer;
            margin-left: 10px;
            font-size: 16px;
            color: #b4b5b8;
        }
        .title-text-bg{
            padding: 5px;
            font-weight:bold;
            line-height: 28px
        }
        .filed-el-select-width{
            width: 100%;
        }
        .col-list{
            margin: 5px 0;
        }
        .col-list.el-select el-select--medium{
            width: 100%;
        }
        .division-dashed{
            border:1px dashed #f2f2f2;
            height: 0.8px;
            margin: 10px 0;
        }
        .el-table .warning-row {
            background: #f6f6f7;
        }
    }
</style>

<template>
    <div class="view-global">
        <el-row >
            <el-col :span="23">
                <div class="grid-content bg-purple-dark div-grid-bg">
                    <span style="margin-right: 5px">查询名称</span>
                    <el-select v-model="curQuery" placeholder="--请查询--" @change="queryChange">
                        <el-option
                                v-for="item,key in sqlOptions"
                                :label="item.name"
                                :value="key">
                        </el-option>
                    </el-select>
                    <i class="el-icon-delete del-icon-pointer icon-color" @click="delDialog = true" v-if="sqlOptions.length > 0">删除</i>
                </div>
            </el-col>
        </el-row>
        <el-row :gutter="8" style="margin-top: 10px">
            <el-col :span="7">
                <viewCard ref="viewCard" v-on:childMethod="clickTblItem"></viewCard>
            </el-col>
            <el-col :span="16">
                <div class="grid-content bg-purple-light">
                    <div class="div-grid-bg tab-name-font">{{curTable.cnName}}</div>
                    <div class="tbl-result-border">
                        <div class="title-text-bg">查询条件</div>
                        <el-row :gutter="10">
                            <el-col :span="24" v-if="queryMap.length == 0" >
                                <div class="grid-content bg-purple-light" style="text-align: center">
                                    <i class="el-icon-circle-plus-outline del-icon-pointer" @click="onAddFiled()">添加</i>
                                </div>
                            </el-col>
                            <el-col :span="24" v-for="queryItem,index in queryMap" class= "col-list" v-else="">
                                <div class="grid-content bg-purple-dark">
                                    <el-col :span="6">
                                        <div class="grid-content bg-purple-dark">
                                            <el-select v-model="queryItem.filed" placeholder="--请选择--" class="filed-el-select-width">
                                                <el-option
                                                        v-for="item in filedOption"
                                                        :key="item.key"
                                                        :label="item.label"
                                                        :value="item.key">
                                                </el-option>
                                            </el-select>
                                        </div>
                                    </el-col>
                                    <el-col :span="6">
                                        <div class="grid-content bg-purple-dark">
                                            <el-select v-model="queryItem.ship" placeholder="--请选择--" class="filed-el-select-width">
                                                <el-option
                                                        v-for="item in shipOptions"
                                                        :key="item.value"
                                                        :label="item.label"
                                                        :value="item.value">
                                                </el-option>
                                            </el-select>
                                        </div>
                                    </el-col>
                                    <el-col :span="6">
                                        <div class="grid-content bg-purple-dark">
                                            <el-input v-model="queryItem.value" placeholder="请输入内容"></el-input>
                                        </div>
                                    </el-col>
                                    <el-col  :span="6">
                                        <div style="line-height: 28px">
                                            <i class="el-icon-delete del-icon-pointer" @click="onDelFiled(index)">删除</i>
                                            <i class="el-icon-circle-plus-outline del-icon-pointer" @click="onAddFiled()">添加</i>
                                        </div>
                                    </el-col>
                                </div>
                            </el-col>
                        </el-row>
                        <div class="division-dashed"></div>
                        <el-row style="margin: 10px 0">
                            <el-col :span="12">
                                <div class="grid-content bg-purple">
                                    <span class="title-text-bg" style="float: bottom">查询结果</span>
                                </div>
                            </el-col>
                            <el-col :span="12">
                                <div class="grid-content bg-purple" style="text-align: right">
                                    <el-button type="primary" size="mini" @click="querySql">查询</el-button>
                                    <el-button type="success" size="mini" @click="saveSql">保存</el-button>
                                </div>
                            </el-col>
                        </el-row>
                            <el-table
                                    :data="resultData"
                                    height="282"
                                    border
                                    :row-class-name="tableRowClassName"
                                    style="width: 100%">
                                <el-table-column
                                        type="index"
                                        label="序号"
                                        width="50"
                                        align="center">
                                </el-table-column>
                                <el-table-column
                                        prop="name"
                                        label="姓名"
                                        width="100">
                                </el-table-column>
                                <el-table-column
                                        prop="age"
                                        label="年龄"
                                        width="50">
                                </el-table-column>
                                <el-table-column
                                        prop="card"
                                        label="身份证"
                                        width="170">
                                </el-table-column>
                                <el-table-column
                                        prop="address"
                                        label="住址"
                                        width="260">
                                </el-table-column>
                                <el-table-column
                                        prop="sex"
                                        label="性别"
                                        width="50">
                                </el-table-column>
                                <el-table-column
                                        prop="home"
                                        label="户籍"
                                        width="50">
                                </el-table-column>
                                <el-table-column
                                        prop="job"
                                        label="职业"
                                        width="60">
                                </el-table-column>
                            </el-table>
                            <div class="block">
                                <el-pagination
                                        @size-change="handleSizeChange"
                                        @current-change="handleCurrentChange"
                                        :current-page.sync="currentPage"
                                        :page-size="100"
                                        layout="total, prev, pager, next, jumper"
                                        :total="1000">
                                </el-pagination>
                            </div>
                    </div>
                </div>
            </el-col>
        </el-row>
        <el-dialog
                title="提示" :visible.sync="delDialog"  width="30%">
                    <div>
                        <el-table :data="sqlOptions" :show-header="false" max-height="182">
                            <el-table-column prop="name"  label="名称"></el-table-column>
                            <el-table-column
                                    fixed="right"
                                    label="操作"
                                    width="80">
                                <template slot-scope="scope">
                                    <el-button
                                            @click.native.prevent="confirmDel(scope.$index)"
                                            type="text"
                                            size="small">
                                        <i class="el-icon-delete"></i>删除
                                    </el-button>
                                </template>
                            </el-table-column>
                        </el-table>
<!--                        <el-pagination-->
<!--                                :hide-on-single-page="true"-->
<!--                                small-->
<!--                                layout="prev, pager, next"-->
<!--                                :total="50">-->
<!--                        </el-pagination>-->
                    </div>
                    <span slot="footer" class="dialog-footer">
                        <el-button @click="delDialog = false">取 消</el-button>
                        <el-button type="primary" @click="delDialog = false">确 定</el-button>
                    </span>
        </el-dialog>
        <el-dialog
                title="提示" :visible.sync="saveDialog"  width="30%">
            <div>
                <el-input v-model="saveName" placeholder="请输入名称"></el-input>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="saveDialog = false">取 消</el-button>
                <el-button type="primary" @click="doSave">确 定</el-button>
              </span>
        </el-dialog>
    </div>
</template>

<script>
    import viewCard from  './view-card'
    export default {
        components: { viewCard },
        data() {
            return {
                curTable:{
                    cnName:''
                },
                saveName:null,
                currentPage: 5,
                delDialog:false,
                saveDialog:false,
                curQuery:null,
                queryMap:[],
                sqlOptions: [
                    {
                        name:'000123',
                        sql:[
                            {
                                filed:1,
                                ship:'选项2',
                                value:'1234'
                            }
                        ]
                    },
                    {
                        name:'000123',
                        sql:[
                            {
                                filed:1,
                                ship:'选项2',
                                value:'1234'
                            }
                        ]
                    }
                ],
                filedOption:[],
                shipOptions: [{
                    value: '选项1',
                    label: '大于'
                }, {
                    value: '选项2',
                    label: '小于'
                }, {
                    value: '选项3',
                    label: '等于'
                }, {
                    value: '选项4',
                    label: '包含'
                }],
                rules: {
                    filed: [
                        { required: true, message: '请选择字段名称', trigger: 'blur' },
                    ],
                    ship: [
                        { required: true, message: '请选择查询条件', trigger: 'blur' }
                    ],
                    value: [
                        { required: true, message: '请输入字段值', trigger: 'blur' }
                    ]
                },
                value: null,
                resultData: [{
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '医生',
                    home: '海南',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '医生',
                    home: '海南',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '四季',
                    home: '上海',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '医生',
                    home: '海南',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '教师',
                    home: '北京',
                    address: '上海市普陀区金沙江路 1518 弄'
                },{
                    card: '460004200010204321',
                    name: '王小虎',
                    age: '19',
                    sex: '男',
                    job: '教师',
                    home: '北京',
                    address: '上海市普陀区金沙江路 1518 弄'
                }],
            }
        },
        methods:{
            onAddFiled(){
                this.queryMap.push(
                    {
                        filed:null,
                        ship:null,
                        value:null
                    }
                 );
            },
            onDelFiled(index){
                console.log('this.queryMap',this.queryMap)
                this.queryMap.splice(index, 1);
                console.log(' this.sqlOptions', this.sqlOptions)

            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                console.log(`当前页: ${val}`);
            },
            confirmDel(index) {
                let _this = this;
                _this.$confirm('您确定要删除['+  _this.sqlOptions[index].name+']吗？', '提示', {type: 'warning'})
                    .then(_ => {
                        _this.sqlOptions.splice(index, 1);
                    })
                    .catch(_ => {});
            },
            clickTblItem(table){
                this.curTable = table
                this.filedOption = table.filed;
            },
            checkSql(){
                let _this = this;
                if (_this.queryMap.length == 0) {
                    _this.$alert('请输入查询条件', '温馨提示', {
                        confirmButtonText: '确定',
                        callback: action => {
                        }
                    });
                    return false;
                }else{
                    try{
                        for(var index = 0; index < _this.queryMap.length; index ++){
                            if (_this.queryMap[index].filed == null || _this.queryMap[index].ship == null || _this.queryMap[index].value == null | _this.queryMap[index].value == '') {
                                _this.$alert('请输入查询条件', '温馨提示', {
                                    confirmButtonText: '确定',
                                    callback: action => {
                                    }
                                });
                                return false;
                            }
                        }
                        // _this.queryMap.forEach(function (value) {
                        //     if (value.filed == null || value.ship == null || value.value == null | value.value == '') {
                        //         _this.$alert('请输入查询条件', '温馨提示', {
                        //             confirmButtonText: '确定',
                        //             callback: action => {
                        //             }
                        //         });
                        //         throw Error('');
                        //     }
                        // })

                    }catch (e) {

                    }
                }
            },
            querySql(){
                if (this.checkSql()) {

                }
            },
            saveSql(){
                if (this.checkSql()) {
                    this.saveDialog = true;
                }
            },
            doSave(){
                this.sqlOptions.push({
                    name:this.saveName,
                    sql:Array.from(this.queryMap)
                });
                console.log(' this.sqlOptions', this.sqlOptions)
                this.saveName = null;
                this.saveDialog = false;
            },
            queryChange(value){
                console.log('value',value)
                console.log(this.sqlOptions[value])
                this.queryMap = this.sqlOptions[value].sql;
            },
            tableRowClassName({row, rowIndex}) {
                if (rowIndex % 2 == 1) {
                    return 'warning-row';
                }
                return '';
            }
        }
    }
</script>
