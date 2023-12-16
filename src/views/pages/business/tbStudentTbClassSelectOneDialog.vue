<template>
  <el-dialog
    title="所属班级"
    width="1000px"
    :close-on-click-modal="false"
    :append-to-body="true"
    v-dialogDrag
    :visible.sync="visible"
  >
           <span slot="title" class="dialog-footer">
          <el-button
            size="small"
            @click="visible = false"
            icon="el-icon-circle-close"
          >关闭</el-button>
          <el-button
            size="small"
            type="primary"
            icon="el-icon-circle-check"
            @click="doSubmit()"
          >确定</el-button>
        </span>
    <div class="search-div" style="height: 55px;margin-top: -25px">
      <el-form
        size="small"
        ref="searchForm"
        :model="searchForm"
      >
        <el-row>
          <el-col :span="5" style="margin-left: 5px">
            <el-form-item prop="name">
              <el-input
                size="small"
                v-model="searchForm.name"
                placeholder="名称"
                @input="changeInput($event)"
                clearable
              >
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="5" style="margin-left: 5px">
            <el-form-item prop="remark">
              <el-input
                size="small"
                v-model="searchForm.remark"
                placeholder="备注"
                @input="changeInput($event)"
                clearable
              >
              </el-input>
            </el-form-item>
          </el-col>
          <el-col :span="5" style="margin-left: 5px">
            <el-form-item>
              <el-button
                type="primary"
                @click="refreshList()"
                size="small"
                icon="el-icon-search"
              >搜索
              </el-button>
              <el-button
                @click="resetSearch()"
                size="small"
                icon="el-icon-refresh-right"
              >重置
              </el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <el-table
      :data="dataList"
      v-loading="loading"
      size="small"
      border
      ref="tbClassTable"
      highlight-current-row
      @current-change="propertySelectionChange"
      style="width: 100%;margin-top: 10px;"
    >
      <el-table-column sortable prop="name" label="名称"/>
      <el-table-column sortable prop="remark" label="备注"/>
      <el-table-column sortable prop="createTime" label="创建时间"/>
      <el-table-column sortable prop="updateTime" label="修改时间"/>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageNo"
      :page-sizes="[5, 10, 50, 100]"
      :page-size="pageSize"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, sizes, prev, pager, next, jumper"
    >
    </el-pagination>
  </el-dialog>
</template>
<script>
import tbClassApi from '@/api/business/tbClass'

export default {
  data() {
    return {
      searchForm: {},
      dataListAllSelections: {}, // 所有选中的数据包含跨页数据
      dataList: [],
      pageNo: 1,
      pageSize: 5,
      total: 0,
      loading: false,
      visible: false
    }
  },
  props: {
    selectData: {
      type: Object,
      default: () => {
        return {}
      }
    },

    where: {
      type: String,
      default: ''
    }
  },
  methods: {
    changeInput() {
      this.$forceUpdate()
    },

    init() {
      this.visible = true
      this.$nextTick(() => {
        this.dataListAllSelections = this.selectData
        this.$refs.tbClassTable.clearSelection()
        this.resetSearch()
      })
    },
    // 获取数据列表
    refreshList() {
      if (this.where != null && this.where != '') {
        let queryObj = JSON.parse(this.where)
        this.searchForm = Object.assign(queryObj, this.searchForm)
      }
      tbClassApi.getPageList(this.pageNo, this.pageSize, this.searchForm).then(
        response => {
          //this.list = response.data.list
          this.dataList = response.data.records
          this.total = response.data.total
          // 数据加载并绑定成功
          this.loading = false
        }
      )
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val
      this.pageNo = 1
      this.refreshList()
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageNo = val
      this.refreshList()
    },
    // 排序
    resetSearch() {
      this.$refs.searchForm.resetFields()
      this.refreshList()
    },
    //监听选中
    propertySelectionChange(newVal, oldVal) {
      this.dataListAllSelections = newVal
    },
    doSubmit() {
      this.visible = false
      this.$emit('doSubmit', this.dataListAllSelections)
    }
  }
}
</script>
<style lang="scss">
</style>
