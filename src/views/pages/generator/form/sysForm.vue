<template>
  <div class="app-container">
    <div class="search-div">
      <el-form label-width="70px" size="small">
        <el-row>
          <el-col :span="8">
            <el-form-item label="名称">
              <el-input v-model="searchObj.name" placeholder="名称" clearable/>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="表单描述">
              <el-input v-model="searchObj.description" placeholder="表单描述" clearable/>
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
                 :disabled="$hasBP('bnt.sysForm.add')  === false">添 加
      </el-button>
      <el-button class="btn-add" size="mini" @click="batchRemove()" :disabled="$hasBP('bnt.sysForm.remove')  === false">
        批量删除
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
      @selection-change="handleSelectionChange">
      <el-table-column
        label="序号"
        width="70"
        align="center">
        <template slot-scope="scope">
          {{ (page - 1) * limit + scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column type="selection" :reserve-selection="true"/>
      <el-table-column sortable prop="name" label="名称"/>
      <el-table-column sortable prop="description" label="表单描述"/>
      <el-table-column sortable label="查看字段">
        <template slot-scope="scope">
          <SysFormItemView :form-id="scope.row.id"></SysFormItemView>
        </template>
      </el-table-column>
      <el-table-column sortable prop="createTime" label="创建时间"/>
      <el-table-column sortable prop="updateTime" label="更新时间"/>
      <el-table-column type="expand" label="...">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="名称">
              <span>{{ props.row.name }}</span>
            </el-form-item>
            <el-form-item label="表单描述">
              <span>{{ props.row.description }}</span>
            </el-form-item>
            <el-form-item label="查看字段">
              <span><SysFormItemView :form-id="props.row.id"></SysFormItemView></span>
            </el-form-item>
            <el-form-item label="创建时间">
              <span>{{ props.row.createTime }}</span>
            </el-form-item>
            <el-form-item label="更新时间">
              <span>{{ props.row.updateTime }}</span>
            </el-form-item>
          </el-form>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" fixed="right">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row.id)"
                     :disabled="$hasBP('bnt.sysForm.update')  === false" title="修改"/>
          <el-button type="danger" icon="el-icon-delete" size="mini" @click="removeDataById(scope.row.id)"
                     :disabled="$hasBP('bnt.sysForm.remove')  === false" title="删除"/>
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
    <el-dialog title="添加/修改" :close-on-click-modal="false" :visible.sync="dialogVisible" width="35%">
      <el-form ref="dataForm" :model="sysForm" :rules="rules" label-width="100px" size="small"
               style="padding-right: 40px;">
        <el-form-item label="表名称" prop="name">
          <el-input v-model="sysForm.name" clearable/>
        </el-form-item>
        <el-form-item label="表单描述" prop="description">
          <el-input v-model="sysForm.description" clearable/>
        </el-form-item>

        <el-form-item label="表结构" prop="tabType">
          <el-radio-group v-model="sysForm.tabType">
            <el-radio label="0">常规</el-radio>
            <el-radio label="1">树形</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item v-if="sysForm.id == null || sysForm.id == ''" label="是否强制生成">
          <el-radio-group v-model="sysForm.isFause">
            <el-radio label="false">否</el-radio>
            <el-radio label="true">是</el-radio>
          </el-radio-group>
          <div style="color: red;font-size: 12px">选择"是"将会删除已存在同名的表，请谨慎选择</div>
        </el-form-item>
        <el-form-item v-for="(item, index) in sysForm.itemList"
                      :label="'字段' + (index+1)">
          <el-input v-model="item.name" placeholder="名称"></el-input>
          <el-input style="margin-top: 5px" v-model="item.description" placeholder="描述"></el-input>
          <!--          字段类型1:int 2:varchar 3:timestamp 4:text 6:BigDecimal-->
          <el-select style="margin-top: 5px" v-model="item.itemType" placeholder="类型">
            <el-option label="字符" value="varchar(500)"></el-option>
            <el-option label="日期" value="timestamp"></el-option>
            <el-option label="文本(text)" value="text"></el-option>
            <el-option label="整数(int)" value="int"></el-option>
            <el-option label="整数(bigint)" value="bigint"></el-option>
            <el-option label="整数(tinyint)" value="tinyint"></el-option>
            <el-option label="小数" value="decimal(20,10)"></el-option>
          </el-select>
          <el-radio-group v-model="item.isAllowNull">
            <el-radio label="0">可为空</el-radio>
            <el-radio label="1">不可为空</el-radio>
          </el-radio-group>
          <div></div>
          <el-button style="margin-left: 10px" @click="deleteItem(index)" type="primary"
                     icon="el-icon-delete"></el-button>
        </el-form-item>

        <el-button style="margin-left: 100px;margin-top: -30px;margin-bottom: 5px" @click="addItem" type="primary"
                   size="mini">新增字段
        </el-button>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false" size="small" icon="el-icon-refresh-right">取 消</el-button>
        <el-button type="primary" icon="el-icon-check" @click="saveOrUpdate()" size="small">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import {getToken} from '@/utils/auth'
import api from '@/api/generator/form/sysForm'
import SysFormItemView from './SysFormItemView.vue'

const defaultForm = {itemList: []}
export default {
  components: {SysFormItemView},
  data() {
    return {
      listLoading: true, // 数据是否正在加载
      list: [], // banner列表
      total: 0, // 数据库中的总记录数
      page: 1, // 默认页码
      limit: 5, // 每页记录数
      searchObj: {}, // 查询表单对象
      dialogVisible: false,
      sysForm: defaultForm,
      saveBtnDisabled: false,
      selectValue: [] //复选框选择内容封装数组
      , rules: {

        name:
          [
            {required: true, message: '请输入名称', trigger: 'blur'}
          ]
        ,
        description:
          [
            {required: true, message: '请输入表单描述', trigger: 'blur'}
          ]
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
    deleteItem(i) {
      this.sysForm.itemList.splice(i, 1)
    },
    addItem() {
      this.sysForm.itemList.push({
        name: "",
        description: "",
        itemType: "",
        tabType: "1",
        isFause: "false",
        isAllowNull:"0"
      });
    },
    //导出数据
    exportData() {
      window.open(process.env.VUE_APP_BASE_API + '/generator/sysForm/export?token=' + getToken());
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
    add(row) {
      this.dialogVisible = true
      this.sysForm = {itemList: [], tabType: "0", isFause: "false"}
      this.rules.name = [{required: true, message: '请输入名称', trigger: 'blur'}]
      this.rules.description = [{required: true, message: '请输入表单描述', trigger: 'blur'}]
    },
    //编辑
    edit(id) {
      this.dialogVisible = true
      this.rules.name = [{required: true, message: '请输入名称', trigger: 'blur'}]
      this.rules.description = [{required: true, message: '请输入表单描述', trigger: 'blur'}]
      api.getById(id).then(response => {
        this.$nextTick(function () {
          this.sysForm = response.data
        })
      })
    },
    //添加或更新
    saveOrUpdate() {
      this.$refs.dataForm.validate(valid => {
        if (valid) {
          if (!this.sysForm.id) {
            this.save()
          } else {
            this.update()
          }
        }
      })
    },
    //添加
    save() {
      api.save(this.sysForm).then(response => {
        this.$message.success('操作成功')
        this.dialogVisible = false
        this.fetchData(this.page)
      })
    },
    //更新
    update() {
      this.$confirm(`修改表结构会清空已有数据，是否继续?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        api.updateById(this.sysForm).then(response => {
          this.$message.success(response.message || '操作成功')
          this.dialogVisible = false
          this.fetchData(this.page)
        })
      }).catch(() => {
        // 取消操作
        this.$message({
          type: 'info',
          message: '已取消'
        })
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
