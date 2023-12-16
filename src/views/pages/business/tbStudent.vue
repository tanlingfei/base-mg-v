<template>
  <div class="app-container">
    <div class="search-div">
      <el-form label-width="70px" size="small">
        <el-row>
          <el-col :span="8">
            <el-form-item label="学生姓名">
              <el-input v-model="searchObj.name" placeholder="学生姓名" clearable/>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="所属班级">
              <el-input v-model="searchObj.className"
                        placeholder="名称" clearable
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="所属地区">
              <el-select
                v-model="searchObj.areaId"
                placeholder="所属地区"
              >
                <el-option
                  v-for="tbArea in tbAreaX117d58f5List"
                  :key="tbArea.id"
                  :label="tbArea.name"
                  :value="tbArea.id"
                />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="树id">
              <el-cascader
                v-model="searchObj.treeId"
                :props="{ label: 'name', value: 'id', checkStrictly: true, emitPath: false }"
                :options="tbTreeTestXfbb2cba6List"
                clearable
                filterable
              ></el-cascader>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="部门id">
              <el-cascader
                v-model="searchObj.deptId"
                :props="{ label: 'name', value: 'id', checkStrictly: true, emitPath: false }"
                :options="sysDeptX0ffc7576List"
                clearable
                filterable
              ></el-cascader>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row style="display:flex">
          <el-button type="primary" icon="el-icon-search" size="mini" @click="fetchData()">搜索</el-button>
          <el-button icon="el-icon-refresh" size="mini" @click="resetData">重置</el-button>
        </el-row>
      </el-form>
    </div>
    <!-- 工具条 -->
    <div class="tools-div">
      <el-button type="success" icon="el-icon-plus" size="mini" @click="add"
                 :disabled="$hasBP('bnt.tbStudent.add')  === false"
      >添 加
      </el-button>
      <el-button class="btn-add" size="mini" @click="batchRemove()"
                 :disabled="$hasBP('bnt.tbStudent.remove')  === false"
      >批量删除
      </el-button>
      <el-button style="margin-left:8px" size="mini" :disabled="$hasBP('bnt.tbStudent.list')  === false" type="primary"
                 @click="exportData"
      >导出数据
      </el-button>
    </div>
    <!-- 列表 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      :row-key="getRowKeys"
      stripe
      border
      style="width: 100%;margin-top: 10px;"
      @selection-change="handleSelectionChange"
    >
      <el-table-column
        label="序号"
        width="70"
        align="center"
      >
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column type="selection" :reserve-selection="true"/>
      <el-table-column sortable prop="name" label="学生姓名"/>
      <el-table-column sortable prop="className" label="所属班级"/>
      <el-table-column sortable prop="areaName" label="所属地区"/>
      <el-table-column sortable prop="treeName" label="树id"/>
      <el-table-column sortable prop="deptName" label="部门名称"/>
      <el-table-column sortable prop="createTime" label="创建时间"/>
      <el-table-column sortable prop="updateTime" label="修改时间"/>
      <el-table-column sortable label="多选树">
        <template slot-scope="scope">
          <TbTreeTest2View :studentId="scope.row.id"></TbTreeTest2View>
        </template>
      </el-table-column>
      <el-table-column type="expand" label="...">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="学生姓名">
              <span>{{ props.row.name }}</span>
            </el-form-item>
            <el-form-item label="所属班级">
              <span>{{ props.row.className }}</span>
            </el-form-item>
            <el-form-item label="所属地区">
              <span>{{ props.row.areaName }}</span>
            </el-form-item>
            <el-form-item label="树id">
              <span>{{ props.row.treeName }}</span>
            </el-form-item>
            <el-form-item label="部门名称">
              <span>{{ props.row.deptName }}</span>
            </el-form-item>
            <el-form-item label="创建时间">
              <span>{{ props.row.createTime }}</span>
            </el-form-item>
            <el-form-item label="修改时间">
              <span>{{ props.row.updateTime }}</span>
            </el-form-item>
            <el-form-item label="多选树">
              <span><TbTreeTest2View :studentId="props.row.id"></TbTreeTest2View></span>
            </el-form-item>
            <el-form-item label="课程名称">
              <span><TbCourseView :studentId="props.row.id"></TbCourseView></span>
            </el-form-item>
            <el-form-item label="讲师名称">
              <span><TbTeacherView :studentId="props.row.id"></TbTeacherView></span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="120" fixed="right">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row.id)"
                     :disabled="$hasBP('bnt.tbStudent.update')  === false" title="修改"
          />
          <el-button type="danger" icon="el-icon-delete" size="mini" @click="removeDataById(scope.row.id)"
                     :disabled="$hasBP('bnt.tbStudent.remove')  === false" title="删除"
          />
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
    <el-dialog title="添加/修改" :visible.sync="dialogVisible" width="40%" :close-on-click-modal="false">
      <el-form ref="dataForm" :model="tbStudent" :rules="rules" label-width="100px" size="small"
               style="padding-right: 40px;"
      >
        <el-form-item label="学生姓名" prop="name">
          <el-input v-model="tbStudent.name" clearable/>
        </el-form-item>
        <el-form-item label="所属班级" prop="classId">
          <TbClassSelectOne
            :myvals="tbStudent.classId"
            @getInfo="getTbClassX7de1b815" where=""
          />
        </el-form-item>
        <el-form-item label="所属地区" prop="areaId">
          <el-select
            v-model="tbStudent.areaId"
            placeholder="所属地区"
          >
            <el-option
              v-for="tbArea in tbAreaX117d58f5List"
              :key="tbArea.id"
              :label="tbArea.name"
              :value="tbArea.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="树id" prop="treeId">
          <el-cascader
            v-model="tbStudent.treeId"
            :props="{ label: 'name', value: 'id', checkStrictly: true, emitPath: false }"
            :options="tbTreeTestXfbb2cba6List"
            clearable
            filterable
          ></el-cascader>
        </el-form-item>
        <el-form-item label="部门id" prop="deptId">
          <el-cascader
            v-model="tbStudent.deptId"
            :props="{ label: 'name', value: 'id', checkStrictly: true, emitPath: false }"
            :options="sysDeptX0ffc7576List"
            clearable
            filterable
          ></el-cascader>
        </el-form-item>
        <el-form-item label="多选树" prop="treesId">
          <el-cascader
            v-model="tbStudent.treesIdList"
            :props="{ label: 'name', multiple:'true', value: 'id', checkStrictly: true, emitPath: false }"
            :options="tbTreeTest2X88750e5aList"
            clearable
            filterable
          ></el-cascader>
        </el-form-item>
        <el-form-item label="选择课程" prop="courseId">
          <el-select multiple
                     v-model="tbStudent.courseIdList"
                     placeholder=""
          >
            <el-option
              v-for="tbCourse in tbCourseXa5e7ce87List"
              :key="tbCourse.id"
              :label="tbCourse.courseName"
              :value="tbCourse.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="选择讲师" prop="teacherId">
          <TbTeacherSelect
            :myvals="tbStudent.teacherIdList"
            @getInfo="getTbTeacherX45d262b1" :single="false" where=""
          />
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
import { getToken } from '@/utils/auth'
import api from '@/api/business/tbStudent'
import TbClassSelectOne from './tbStudentTbClassSelectOne'
import tbAreaApi from '@/api/business/tbArea'
import tbTreeTestApi from '@/api/business/tbTreeTest'
import sysDeptApi from '@/api/system/sysDept'
import tbTreeTest2Api from '@/api/business/tbTreeTest2'
import TbTreeTest2View from './tbStudentTbTreeTest2View'

import tbCourseApi from '@/api/business/tbCourse'
import TbCourseView from './tbStudentTbCourseView'

import TbTeacherSelect from './tbStudentTbTeacherSelect'
import TbTeacherView from './tbStudentTbTeacherView'

const defaultForm = {}
export default {
  components: {
    TbClassSelectOne,
    TbTreeTest2View,

    TbCourseView,

    TbTeacherSelect,
    TbTeacherView,

    SelectDeptTree: () => import('@/components/Custom/TreeSelect/TreeSelect')
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
      tbStudent: defaultForm,
      saveBtnDisabled: false,
      selectValue: [] //复选框选择内容封装数组
      , tbAreaX117d58f5List: []
      , tbTreeTestXfbb2cba6List: []
      , sysDeptX0ffc7576List: []
      , tbTreeTest2X88750e5aList: []
      , tbCourseXa5e7ce87List: []
      , dataDeptTree: []//树形选择数据
      , rules: {

        name:
          [
            { required: true, message: '请输入学生姓名', trigger: 'blur' }
          ]
        ,
        classId:
          [
            { required: true, message: '请输入所属班级', trigger: 'blur' }
          ]
        ,
        areaId:
          [
            { required: true, message: '请输入所属地区', trigger: 'blur' }
          ]
        ,
        deptId:
          [
            { required: true, message: '请输入部门id', trigger: 'blur' }
          ]
      }
    }
  },
  // 生命周期函数：内存准备完毕，页面尚未渲染
  created() {
    console.log('list created......')
    this.fetchData()
    this.getAllTbAreaX117d58f5()
    this.getAllTbTreeTestXfbb2cba6()
    this.getAllSysDeptX0ffc7576()
    this.getAllTbTreeTest2X88750e5a()
    this.getAllTbCourseXa5e7ce87()
    this.fetchPopDept()
  },
  // 生命周期函数：内存准备完毕，页面渲染成功
  mounted() {
    console.log('list mounted......')
  },
  methods: {
    getRowKeys(row) {
      return row.id
    },
    //导出数据
    exportData() {
      window.open(process.env.VUE_APP_BASE_API + '/business/tbStudent/export?token=' + getToken())
    },
    // 每页数
    sizeChangeHandle(val) {
      this.limit = val
      this.page = 1
      this.fetchData(this.page)
    },
    getTbClassX7de1b815(selections) {
      this.tbStudent.classId = selections
      this.$refs['dataForm'].clearValidate('classId')
      this.rules.classId = []
    },
    getAllTbAreaX117d58f5() {
      let queryObj = {}
      tbAreaApi.getAllTbArea(queryObj)
        .then(response => {
          this.tbAreaX117d58f5List = response.data
        })
    },
    getAllTbTreeTestXfbb2cba6() {
      let queryObj = {}
      tbTreeTestApi.getAllTbTreeTest(queryObj)
        .then(response => {
          this.tbTreeTestXfbb2cba6List = response.data
        })
    },
    getAllSysDeptX0ffc7576() {
      let queryObj = {}
      sysDeptApi.getAllSysDept(queryObj)
        .then(response => {
          this.sysDeptX0ffc7576List = response.data
        })
    },
    getAllTbTreeTest2X88750e5a() {
      let queryObj = {}
      tbTreeTest2Api.getAllTbTreeTest2(queryObj)
        .then(response => {
          this.tbTreeTest2X88750e5aList = response.data
        })
    },
    getAllTbCourseXa5e7ce87() {
      let queryObj = {}
      tbCourseApi.getAllTbCourse(queryObj)
        .then(response => {
          this.tbCourseXa5e7ce87List = response.data
        })
    },
    getTbTeacherX45d262b1(selections) {
      this.tbStudent.teacherIdList = selections
    },
    //回调保存选择的部门id
    saveDeptId(treeId, treeName) {
      this.tbStudent.deptId = treeId
      this.$refs['dataForm'].clearValidate('deptId')
      this.rules.deptId = []
    },
    fetchPopDept() {
      //加载弹出选择部门数据
      sysDeptApi.findSelectNodes().then(response => {
        this.dataDeptTree = response.data
      })
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
          })
          //刷新
          this.fetchData()
        })
      })
    },
    //弹出添加表单
    add(row) {
      this.dialogVisible = true
      if (this.$refs.zdept) {
        this.$refs.zdept.reset_data()
      }
      this.tbStudent = {}
      this.rules.name = [{ required: true, message: '请输入学生姓名', trigger: 'blur' }]
      this.rules.classId = [{ required: true, message: '请输入所属班级', trigger: 'blur' }]
      this.rules.areaId = [{ required: true, message: '请输入所属地区', trigger: 'blur' }]
      this.rules.deptId = [{ required: true, message: '请输入部门id', trigger: 'blur' }]
    },
    //编辑
    edit(id) {
      this.dialogVisible = true
      this.rules.name = [{ required: true, message: '请输入学生姓名', trigger: 'blur' }]
      this.rules.classId = [{ required: true, message: '请输入所属班级', trigger: 'blur' }]
      this.rules.areaId = [{ required: true, message: '请输入所属地区', trigger: 'blur' }]
      this.rules.deptId = [{ required: true, message: '请输入部门id', trigger: 'blur' }]
      api.getById(id).then(response => {
        this.$nextTick(function() {
          this.tbStudent = response.data
          if (this.$refs.zdept) {
            this.$refs.zdept.reset_data(this.tbStudent.deptName)
          }
          if (this.$refs.eltreeDept) {
            this.$refs.eltreeDept.setCheckedKeys(this.tbStudent.deptIdList)
          }
        })
      })
    },
    //添加或更新
    saveOrUpdate() {
      this.$refs.dataForm.validate(valid => {
        if (valid) {
          if (!this.tbStudent.id) {
            this.save()
          } else {
            this.update()
          }
        }
      })
    },
    //添加
    save() {
      api.save(this.tbStudent).then(response => {
        this.$message.success('操作成功')
        this.dialogVisible = false
        this.fetchData(this.page)
      })
    },
    //更新
    update() {
      api.updateById(this.tbStudent).then(response => {
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
