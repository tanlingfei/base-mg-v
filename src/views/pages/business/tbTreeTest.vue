<template>
  <div class="app-container">
    <!-- 工具条 -->
    <div class="tools-div">
      <el-button type="success" icon="el-icon-plus" size="mini" @click="add()"
                 :disabled="$hasBP('bnt.tbTreeTest.add')  === false"
      >添 加
      </el-button>
    </div>
    <!-- 列表 -->
    <el-table
      v-loading="listLoading"
      :data="list"
      style="width: 100%;margin-bottom: 20px;margin-top: 10px;"
      row-key="id"
      border
      :default-expand-all="false"
      :tree-props="{children: 'children'}"
    >
      <el-table-column sortable prop="name" label="名称"/>
      <el-table-column sortable prop="treeRemark" label="备注"/>
      <el-table-column sortable prop="sortValue" label="排序"/>
      <el-table-column sortable prop="createTime" label="创建时间"/>
      <el-table-column sortable prop="updateTime" label="修改时间"/>
      <el-table-column type="expand" label="...">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="名称">
              <span>{{ props.row.name }}</span>
            </el-form-item>
            <el-form-item label="备注">
              <span>{{ props.row.treeRemark }}</span>
            </el-form-item>
            <el-form-item label="排序">
              <span>{{ props.row.sortValue }}</span>
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
      <el-table-column label="操作" align="center" fixed="right">
        <template slot-scope="scope">
          <el-button type="success" v-if="scope.row.type !== 2" icon="el-icon-plus" size="mini" @click="add(scope.row)"
                     title="添加下级"
          />
          <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row.id)"
                     :disabled="$hasBP('bnt.tbTreeTest.update')  === false" title="修改"
          />
          <el-button type="danger" icon="el-icon-delete" size="mini" @click="removeDataById(scope.row.id)"
                     :disabled="$hasBP('bnt.tbTreeTest.remove')  === false" title="删除"
          />
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="添加/修改" :visible.sync="dialogVisible" width="40%" :close-on-click-modal="false">
      <el-form ref="dataForm" :model="tbTreeTest" :rules="rules" label-width="100px" size="small"
               style="padding-right: 40px;"
      >
        <el-form-item label="上级">
          <select-parent-tree @saveTreeId="saveParentId" treeTitle="选择上级" :dataTree="dataParentTree"
                              :treeVal="tbTreeTest.parentName" :treeId="tbTreeTest.parentId"
          ></select-parent-tree>
        </el-form-item>
        <el-form-item label="名称" prop="name">
          <el-input v-model="tbTreeTest.name" clearable/>
        </el-form-item>
        <el-form-item label="备注" prop="treeRemark">
          <el-input v-model="tbTreeTest.treeRemark" clearable/>
        </el-form-item>
        <el-form-item label="排序" prop="sortValue">
          <el-input-number :min="0" v-model="tbTreeTest.sortValue" controls-position="right" clearable/>
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
import api from '@/api/business/tbTreeTest'

const defaultForm = {}
export default {
  components: {
    //导入树形选择组件
    SelectParentTree: () => import('@/components/Custom/TreeSelect/TreeSelect')
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
      tbTreeTest: defaultForm,
      saveBtnDisabled: false,
      selectValue: [] //复选框选择内容封装数组
      , rules: {

        name:
          [
            { required: true, message: '请输入名称', trigger: 'blur' }
          ]
        ,
        treeRemark:
          [
            { required: true, message: '请输入备注', trigger: 'blur' }
          ]
        ,
        parentId:
          [
            { required: true, message: '请输入上级', trigger: 'blur' }
          ]
        ,
        sortValue:
          [
            { required: true, message: '请输入排序', trigger: 'blur' }
          ]
      }
      , dataParentTree: null
    }
  },
  // 生命周期函数：内存准备完毕，页面尚未渲染
  created() {
    console.log('list created......')
    this.fetchData()
    this.fetchPopParent()
  },
  // 生命周期函数：内存准备完毕，页面渲染成功
  mounted() {
    console.log('list mounted......')
  },
  methods: {
    getRowKeys(row) {
      return row.id
    },
    //回调保存选择的部门id
    saveParentId(treeId) {
      if (treeId == this.tbTreeTest.id) {
        this.$alert('上级不能和当前相同', '提示', {
          confirmButtonText: '确定',
          callback: action => {
            this.tbTreeTest.parentId = ''
            this.tbTreeTest.parentName = ''
            api.getById(this.tbTreeTest.id).then(response => {
              this.tbTreeTest = response.data
              //this.tbTreeTest.parentName = row.name
            })
          }
        })
      } else {
        this.tbTreeTest.parentId = treeId
        this.tbTreeTest.parentName = treeName
      }
    },

    fetchPopParent() {
      //加载弹出选择部门数据
      api.getAllTbTreeTest().then(response => {
        this.dataParentTree = response.data
      })

    },

    //导出数据
    exportData() {
      window.open(process.env.VUE_APP_BASE_API + '/business/tbTreeTest/export?token=' + getToken())
    },
    // 每页数
    sizeChangeHandle(val) {
      this.limit = val
      this.page = 1
      this.fetchData(this.page)
    },
    // 加载banner列表数据
    fetchData(page = 1) {
      debugger
      this.page = page
      api.getAllTbTreeTest(this.searchObj).then(
        response => {
          //this.list = response.data.list
          this.list = response.data
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
      this.tbTreeTest = {}
      this.dialogTitle = '添加下级'
      if (row) {
        this.tbTreeTest.parentId = row.id
        this.tbTreeTest.parentName = row.name
      } else {
        this.dialogTitle = '添加顶级'
        this.tbTreeTest.parentId = '0'
      }
      this.rules.name = [{ required: true, message: '请输入名称', trigger: 'blur' }]
      this.rules.treeRemark = [{ required: true, message: '请输入备注', trigger: 'blur' }]
      this.rules.parentId = [{ required: true, message: '请输入上级', trigger: 'blur' }]
      this.rules.sortValue = [{ required: true, message: '请输入排序', trigger: 'blur' }]
    },
    //编辑
    edit(id) {
      this.dialogVisible = true
      this.rules.name = [{ required: true, message: '请输入名称', trigger: 'blur' }]
      this.rules.treeRemark = [{ required: true, message: '请输入备注', trigger: 'blur' }]
      this.rules.parentId = [{ required: true, message: '请输入上级', trigger: 'blur' }]
      this.rules.sortValue = [{ required: true, message: '请输入排序', trigger: 'blur' }]
      api.getById(id).then(response => {
        this.$nextTick(function() {
          this.tbTreeTest = response.data
          if (this.$refs.zdept) {
            this.$refs.zdept.reset_data(this.tbTreeTest.deptName)
          }
          if (this.$refs.eltreeDept) {
            this.$refs.eltreeDept.setCheckedKeys(this.tbTreeTest.deptIdList)
          }
        })
      })
    },
    //添加或更新
    saveOrUpdate() {
      this.$refs.dataForm.validate(valid => {
        if (valid) {
          if (!this.tbTreeTest.id) {
            this.save()
          } else {
            this.update()
          }
        }
      })
    },
    //添加
    save() {
      api.save(this.tbTreeTest).then(response => {
        this.$message.success('操作成功')
        this.dialogVisible = false
        this.fetchData(this.page)
      })
    },
    //更新
    update() {
      api.updateById(this.tbTreeTest).then(response => {
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
