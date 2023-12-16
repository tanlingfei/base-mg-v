<template>
        <div class="app-container">
        <div class="search-div">
            <el-form label-width="70px" size="small">
                <el-row>
                            <el-col :span="8">
                                <el-form-item label="模块标题">
                                        <el-input v-model="searchObj.title" placeholder="模块标题" clearable/>
                                </el-form-item>
                            </el-col>
                </el-row>
                <el-row style="display:flex">
                    <el-button type="primary" icon="el-icon-search" size="mini" @click="fetchData()">搜索</el-button>
                    <el-button icon="el-icon-refresh" size="mini" @click="resetData">重置</el-button>
                </el-row>
            </el-form>
        </div>
        <!-- 列表 -->
        <el-table
                v-loading="listLoading"
                :data="list"
                :row-key="getRowKeys"
                stripe
                border
                style="width: 100%;margin-top: 10px;"
                @selection-change="handleSelectionChange">
            <el-table-column
                    label="序号"
                    width="70"
                    align="center">
                <template slot-scope="scope">
                    {{ (page - 1) * limit + scope.$index + 1 }}
                </template>
            </el-table-column>
            <el-table-column type="selection"  :reserve-selection="true"/>
                        <el-table-column sortable prop="title" label="模块标题"/>
                        <el-table-column sortable prop="businessType" label="业务类型"/>
                        <el-table-column sortable prop="method" label="方法名称"/>
                        <el-table-column sortable prop="requestMethod" label="请求方式"/>
                        <el-table-column sortable prop="operatorType" label="操作类别"/>
                        <el-table-column sortable prop="operName" label="操作人员"/>
                        <el-table-column sortable prop="deptName" label="部门名称"/>
                        <el-table-column sortable prop="operUrl" label="请求URL"/>
                                <el-table-column type="expand" label="...">
                                    <template slot-scope="props">
                                        <el-form label-position="left" inline class="demo-table-expand">
                                                    <el-form-item label="模块标题">
                                                        <span>{{ props.row.title }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="业务类型">
                                                        <span>{{ props.row.businessType }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="方法名称">
                                                        <span>{{ props.row.method }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="请求方式">
                                                        <span>{{ props.row.requestMethod }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作类别">
                                                        <span>{{ props.row.operatorType }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作人员">
                                                        <span>{{ props.row.operName }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="部门名称">
                                                        <span>{{ props.row.deptName }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="请求URL">
                                                        <span>{{ props.row.operUrl }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="主机地址">
                                                        <span>{{ props.row.operIp }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="请求参数">
                                                        <span>{{ props.row.operParam }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="返回参数">
                                                        <span>{{ props.row.jsonResult }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作状态（0正常 1异常）">
                                                        <span>{{ props.row.status }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="错误消息">
                                                        <span>{{ props.row.errorMsg }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="操作时间">
                                                        <span>{{ props.row.operTime }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="创建时间">
                                                        <span>{{ props.row.createTime }}</span>
                                                    </el-form-item>
                                                    <el-form-item label="修改时间">
                                                        <span>{{ props.row.updateTime }}</span>
                                                    </el-form-item>
                                        </el-form>
                                    </template>
                                </el-table-column>
        </el-table>
        <!-- 分页组件 -->
        <el-pagination
                @size-change="sizeChangeHandle"
                :current-page="page"
                :total="total"
                :page-size="limit"
                style="padding: 30px 0; text-align: center;"
                :page-sizes="[5, 10, 50, 100]"
                layout="total,sizes, prev, pager, next, jumper"
                @current-change="fetchData"
        />
        <el-dialog title="添加/修改" :visible.sync="dialogVisible" width="40%">
            <el-form ref="dataForm" :model="sysOperLog" :rules="rules" label-width="100px" size="small"
                     style="padding-right: 40px;">
                    <el-form-item label="模块标题" prop="title">
                                <el-input v-model="sysOperLog.title" clearable/>
                    </el-form-item>
                    <el-form-item label="业务类型" prop="businessType">
                                <el-input v-model="sysOperLog.businessType" clearable/>
                    </el-form-item>
                    <el-form-item label="方法名称" prop="method">
                                <el-input v-model="sysOperLog.method" clearable/>
                    </el-form-item>
                    <el-form-item label="请求方式" prop="requestMethod">
                                <el-input v-model="sysOperLog.requestMethod" clearable/>
                    </el-form-item>
                    <el-form-item label="操作类别" prop="operatorType">
                                <el-input v-model="sysOperLog.operatorType" clearable/>
                    </el-form-item>
                    <el-form-item label="操作人员" prop="operName">
                                <el-input v-model="sysOperLog.operName" clearable/>
                    </el-form-item>
                    <el-form-item label="部门名称" prop="deptName">
                                <el-input v-model="sysOperLog.deptName" clearable/>
                    </el-form-item>
                    <el-form-item label="请求URL" prop="operUrl">
                                <el-input v-model="sysOperLog.operUrl" clearable/>
                    </el-form-item>
                    <el-form-item label="主机地址" prop="operIp">
                                <el-input v-model="sysOperLog.operIp" clearable/>
                    </el-form-item>
                    <el-form-item label="请求参数" prop="operParam">
                                <el-input v-model="sysOperLog.operParam" clearable/>
                    </el-form-item>
                    <el-form-item label="返回参数" prop="jsonResult">
                                <el-input v-model="sysOperLog.jsonResult" clearable/>
                    </el-form-item>
                    <el-form-item label="操作状态（0正常 1异常）" prop="status">
                                <el-input-number :min="0" v-model="sysOperLog.status" controls-position="right"  clearable/>
                    </el-form-item>
                    <el-form-item label="错误消息" prop="errorMsg">
                                <el-input v-model="sysOperLog.errorMsg" clearable/>
                    </el-form-item>
                    <el-form-item label="操作时间" prop="operTime">
                                <el-date-picker
                                        v-model="sysOperLog.operTime"
                                        type="date"
                                        placeholder="选择日期">
                                </el-date-picker>
                    </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false" size="small" icon="el-icon-refresh-right">取 消</el-button>
        <el-button type="primary" icon="el-icon-check" @click="saveOrUpdate()" size="small">确 定</el-button>
      </span>
        </el-dialog>
    </div>
</template>
<script>
    import api from '@/api/log/sysOperLog'
    const defaultForm = {
    }
    export default {
        components: {
        },
        data() {
            return {
                listLoading: true, // 数据是否正在加载
                list: [], // banner列表
                total: 0, // 数据库中的总记录数
                page: 1, // 默认页码
                limit: 5, // 每页记录数
                searchObj: {}, // 查询表单对象
                dialogVisible: false,
                sysOperLog:defaultForm,
                saveBtnDisabled:false,
                selectValue:[] //复选框选择内容封装数组
                , rules:{
            }
        }
        },
        // 生命周期函数：内存准备完毕，页面尚未渲染
        created() {
            console.log('list created......')
            this.fetchData()
        },
        // 生命周期函数：内存准备完毕，页面渲染成功
        mounted() {
            console.log('list mounted......')
        },
        methods: {
            getRowKeys(row) {
                return row.id
            },
            // 每页数
            sizeChangeHandle(val) {
                this.limit = val;
                this.page = 1;
                this.fetchData(this.page);
            },
            // 加载banner列表数据
            fetchData(page = 1) {
                debugger
                this.page = page
                api.getPageList(this.page, this.limit, this.searchObj).then(
                    response => {
                        //this.list = response.data.list
                        this.list = response.data.records
                        this.total = response.data.total
                        // 数据加载并绑定成功
                        this.listLoading = false
                    }
                )
            },
            // 重置查询表单
            resetData() {
                console.log('重置查询表单')
                this.searchObj = {}
                this.fetchData()
            },
            // 根据id删除数据
            removeDataById(id) {
                // debugger
                this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => { // promise
                    // 点击确定，远程调用ajax
                    return api.removeById(id)
                }).then((response) => {
                    this.fetchData(this.page)
                    this.$message.success(response.message || '删除成功')
                }).catch(() => {
                    this.$message.info('取消删除')
                })
            },
            //复选框发生变化执行方法
            handleSelectionChange(selection) {
                this.selectValue = selection
                //console.log(this.selectValue)
            },
            //批量删除
            batchRemove() {
                //判断
                if (this.selectValue.length == 0) {
                    this.$message.warning('请选择要删除的记录！')
                    return
                }
                this.$confirm('此操作将永久删除, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    //数组
                    var idList = []
                    //获取多个复选框对应id，封装到数组里面
                    // [1,2,3]
                    for (var i = 0; i < this.selectValue.length; i++) {
                        var obj = this.selectValue[i]
                        //id值
                        var id = obj.id
                        //放到数组里面
                        idList.push(id)
                    }
                    api.batchRemove(idList).then(response => {
                        //提示
                        this.$message({
                            type: 'success',
                            message: '删除成功!'
                        });
                        //刷新
                        this.fetchData()
                    })
                })
            },
            //弹出添加表单
            add() {
                this.dialogVisible = true
                this.sysOperLog = {
                }
            },
            //编辑
            edit(id) {
                this.dialogVisible = true
                api.getById(id).then(response => {
                    this.$nextTick(function () {
                        this.sysOperLog = response.data
                        if(this.$refs.eltreeDept){
                            this.$refs.eltreeDept.setCheckedKeys(this.sysOperLog.deptIdList);
                        }
                    })
                })
            },
            //添加或更新
            saveOrUpdate() {
                this.$refs.dataForm.validate(valid => {
                    if (valid) {
                        if (!this.sysOperLog.id) {
                            this.save()
                        } else {
                            this.update()
                        }
                    }
                })
            },
            //添加
            save() {
                api.save(this.sysOperLog).then(response => {
                    this.$message.success('操作成功')
                    this.dialogVisible = false
                    this.fetchData(this.page)
                })
            },
            //更新
            update() {
                api.updateById(this.sysOperLog).then(response => {
                    this.$message.success(response.message || '操作成功')
                    this.dialogVisible = false
                    this.fetchData(this.page)
                })
            }
        }
    }
</script>
<style>
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
