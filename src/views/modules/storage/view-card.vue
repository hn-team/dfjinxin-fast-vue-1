<style lang="scss">

    .card-global{
        .el-input--medium .el-input__inner{
            height: 30px;
        }
        .vue-treeselect__control{
            height: 31px;
            border-radius: 4px;
        }
        .vue-treeselect__placeholder, .vue-treeselect__single-value{
            line-height: 26px;
        }
        .el-tabs--border-card>.el-tabs__header{
            background-color: #eceff2;
        }
        .el-tabs__nav{
            width: 100%;
        }
        .el-tabs__item{
            width: 51%;
            text-align: center;
        }
        .el-tabs--border-card{
            border:1px solid #eeeeee;
            box-shadow: 0 0 0 0;
        }
        .el-tabs--border-card>.el-tabs__content{
            padding: 5px;
        }
        .tab-tree-height{
            height: 458px;
            overflow:auto;
        }
        .tab-height{
            height: 458px;
        }
        .space{
            width: 100%;
            margin-bottom: 10px
        }
        .el-table .warning-row {
            background: #f6f6f7;
        }
    }

</style>

<template>
    <div class="card-global">
        <el-tabs type="border-card" >
            <el-tab-pane label="元数据视图"  >
                <el-row>
                    <el-select v-model="value" placeholder="请选择数据分区" style="width:100%;margin-bottom: 10px">
                        <el-option-group
                                v-for="group in areaOptions"
                                :key="group.label"
                                :label="group.label">
                            <el-option
                                    v-for="item in group.options"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-option-group>
                    </el-select>
                </el-row>
                <el-row  class="space" :gutter="10">
                    <el-col :span="19"><el-input v-model="tabName" placeholder="请输入表名称"></el-input></el-col>
                    <el-col :span="5">
                        <div class="grid-content bg-purple" style="text-align: right">
                            <el-button type="primary" size="mini" @click="queryTable">查询</el-button>
                        </div>
                    </el-col>
                </el-row>
                <el-row  class="tab-height">
                    <el-table
                            :data="tableData"
                            border
                            style="width:100%"
                            height="396"
                            :row-class-name="tableRowClassName"
                            @row-click="onRowClick"
                    >
                        <el-table-column
                                type="index"
                                label="序号"
                                width="50"
                                align="center">
                        </el-table-column>
                        <el-table-column
                                prop="cnName"
                                label="表中文名"
                                width="200"
                                align="center">
                        </el-table-column>
                        <el-table-column
                                prop="enName"
                                label="表英文名"
                                width="200"
                                align="center">
                        </el-table-column>
                    </el-table>
                    <el-pagination
                            small
                            @size-change="handleSizeChange"
                            @current-change="handleCurrentChange"
                            layout="prev, pager, next"
                            :total="50">
                    </el-pagination>
                </el-row>
            </el-tab-pane>
            <el-tab-pane label="标签视图">
                <el-row>
                    <el-col >
                        <el-select v-model="value" placeholder="请选择数据分区" class="space">
                            <el-option-group
                                    v-for="group in areaOptions"
                                    :key="group.label"
                                    :label="group.label">
                                <el-option
                                        v-for="item in group.options"
                                        :key="item.value"
                                        :label="item.label"
                                        :value="item.value">
                                </el-option>
                            </el-option-group>
                        </el-select>
                    </el-col>
                    <el-col >
                        <treeselect v-model="labelValue" :multiple="true" :options="labelOptions" placeholder="--请选择--" @input="treeSelectChange"/>
                    </el-col>
                </el-row>
                <el-row >
                    <el-col class="tab-tree-height">
                        <el-tree :data="resultTree" :props="defaultProps" @node-click="handleNodeClick" style="margin-top: 5px"></el-tree>
                    </el-col>
                </el-row>
            </el-tab-pane>
        </el-tabs>
    </div>
</template>

<script>
    import Treeselect from '@riophae/vue-treeselect'
    import '@riophae/vue-treeselect/dist/vue-treeselect.css'
    export default {
        components: {
            Treeselect
        },
        data() {
            return {
                tabName:'',
                tableData: [{
                    cnName: '市教委-中学',
                    enName: 'shijiaowei-zhongxue',
                    filed:{
                        name:{
                            type:1,
                            key:1,
                            label:'姓名'
                        },
                        age:{
                            type:2,
                            key:2,
                            label:'年龄'
                        },
                        card:{
                            type:1,
                            key:3,
                            label:'身份证'
                        },
                        birth:{
                            type:3,
                            key:4,
                            label:'出生日期'
                        },
                        address:{
                            type:1,
                            key:5,
                            label:'地址'
                        },
                    }
                }, {
                    cnName: '教育局-中学',
                    enName: 'jiaoyuju-zhongxue',
                    filed:{
                        name:{
                            type:1,
                            key:1,
                            label:'姓名'
                        },
                        age:{
                            type:2,
                            key:2,
                            label:'年龄'
                        },
                        card:{
                            type:1,
                            key:3,
                            label:'身份证'
                        },
                        birth:{
                            type:3,
                            key:4,
                            label:'出生日期'
                        },
                        address:{
                            type:1,
                            key:5,
                            label:'地址'
                        },
                    }
                },{
                    cnName: '北京市-中学',
                    enName: 'beijingshi-zhongxue',
                    filed:{
                        name:{
                            type:1,
                            key:1,
                            label:'姓名'
                        },
                        age:{
                            type:2,
                            key:2,
                            label:'年龄'
                        },
                        card:{
                            type:1,
                            key:3,
                            label:'身份证'
                        },
                        birth:{
                            type:3,
                            key:4,
                            label:'出生日期'
                        },
                        address:{
                            type:1,
                            key:5,
                            label:'地址'
                        },
                    }
                },{
                    cnName: '省教委-中学',
                    enName: 'shengjiaowei-zhongxue',
                    filed:{
                        name:{
                            type:1,
                            key:1,
                            label:'姓名'
                        },
                        age:{
                            type:2,
                            key:2,
                            label:'年龄'
                        },
                        card:{
                            type:1,
                            key:3,
                            label:'身份证'
                        },
                        birth:{
                            type:3,
                            key:4,
                            label:'出生日期'
                        },
                        address:{
                            type:1,
                            key:5,
                            label:'地址'
                        },
                    }
                },{
                    cnName: '市教委-小学',
                    enName: 'shijiaowei-小学',
                    filed:{
                        name:{
                            type:1,
                            key:1,
                            label:'姓名'
                        },
                        age:{
                            type:2,
                            key:2,
                            label:'年龄'
                        },
                        card:{
                            type:1,
                            key:3,
                            label:'身份证'
                        },
                        birth:{
                            type:3,
                            key:4,
                            label:'出生日期'
                        },
                        address:{
                            type:1,
                            key:5,
                            label:'地址'
                        },
                    }
                }],
                areaOptions: [{
                    options: [{
                        value: 'shenzhen',
                        label: '原始区'
                    },{
                        value: 'Shanghai',
                        label: '清洗区'
                    }, {
                        value: 'Beijing',
                        label: '融合区'
                    }]
                }],
                resultTree: [{
                    label: '一级 1',
                    children: [{
                        label: '二级 1-1',
                        children: [{
                            label: '三级 1-1-1'
                        }]
                    }]
                }, {
                    label: '一级 2',
                    children: [{
                        label: '二级 2-1',
                        children: [{
                            label: '三级 2-1-1'
                        }]
                    }, {
                        label: '二级 2-2',
                        children: [{
                            label: '三级 2-2-1'
                        }]
                    }]
                }, {
                    label: '一级 2',
                    children: [{
                        label: '二级 2-1',
                        children: [{
                            label: '三级 2-1-1'
                        }]
                    }, {
                        label: '二级 2-2',
                        children: [{
                            label: '三级 2-2-1'
                        }]
                    }]
                }, {
                    label: '一级 2',
                    children: [{
                        label: '二级 2-1',
                        children: [{
                            label: '三级 2-1-1'
                        }]
                    }, {
                        label: '二级 2-2',
                        children: [{
                            label: '三级 2-2-1'
                        }]
                    }]
                }, {
                    label: '一级 2',
                    children: [{
                        label: '二级 2-1',
                        children: [{
                            label: '三级 2-1-1'
                        }]
                    }, {
                        label: '二级 2-2',
                        children: [{
                            label: '三级 2-2-1'
                        }]
                    }]
                }, {
                    label: '一级 3',
                    children: [{
                        label: '二级 3-1',
                        children: [{
                            label: '三级 3-1-1'
                        }]
                    }, {
                        label: '二级 3-2',
                        children: [{
                            label: '三级 3-2-1'
                        }]
                    }]
                }],
                defaultProps: {
                    children: 'children',
                    label: 'label'
                },
                labelOptions: [ {
                    id: '1',
                    label: '标签类目',
                    children: [ {
                        id: '2-1',
                        label: '表分类',
                        children: [ {
                            id: '3-1',
                            label: '数据分区',
                            children: [ {
                                id: '4-1',
                                label: '原始取',
                            }, {
                                id: '4-2',
                                label: '清洗区',
                            }, {
                                id: '4-3',
                                label: '融合区',
                            }],
                        }, {
                            id: '3-2',
                            label: '数据分类',
                            children: [ {
                                id: '4-4',
                                label: '人口',
                            }, {
                                id: '4-5',
                                label: '法人',
                            }, {
                                id: '4-6',
                                label: '信用',
                            }],
                        }, {
                            id: '3-3',
                            label: '共享类型',
                            children: [ {
                                id: '4-7',
                                label: 'other',
                            }],
                        }, {
                            id: '3-4',
                            label: '安全等级',
                        }, {
                            id: '3-5',
                            label: '质量标签',
                        }],
                    }, {
                        id: '2-2',
                        label: '属性分类',
                        children: [ {
                            id: '3-6',
                            label: '命名',
                            children: [ {
                                id: '4-8',
                                label: '人名',
                            }, {
                                id: '4-9',
                                label: '企业名',
                            }],
                        }, {
                            id: '3-7',
                            label: '地址',
                            children: [ {
                                id: '4-10',
                                label: '中文地址',
                            }, {
                                id: '4-11',
                                label: '英文地址',
                            }],
                        }, {
                            id: '3-8',
                            label: '联系方式',
                        }, {
                            id: '3-9',
                            label: '金额',
                        }],
                    } ],
                }],
                value: '',
                labelValue:null
            }
        },
        methods:{
            queryTable(){
                console.log(this.tabName.replace(/\s*/g,""))
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                console.log(`当前页: ${val}`);
            },
            treeSelectChange(value, instanceId){
                console.log(value);
                console.log(instanceId);
            },
            handleNodeClick(data, node, obj) {
                if (node.childNodes.length == 0) {
                    this.$emit('childMethod',{
                        cnName: '市教委-小学',
                        enName: 'shijiaowei-小学',
                        filed:{
                            name:{
                                type:1,
                                key:1,
                                label:'姓名'
                            },
                            age:{
                                type:2,
                                key:2,
                                label:'年龄'
                            },
                            card:{
                                type:1,
                                key:3,
                                label:'身份证'
                            },
                            birth:{
                                type:3,
                                key:4,
                                label:'出生日期'
                            },
                            address:{
                                type:1,
                                key:5,
                                label:'地址'
                            },
                        }
                    })
                }
            },
            onRowClick(row, column, event){
                this.$emit('childMethod',row)
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