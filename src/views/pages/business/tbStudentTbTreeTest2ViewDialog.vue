<template>
  <el-dialog
    title="多选树"
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
            <el-form-item prop="treePath">
              <el-input
                size="small"
                v-model="searchForm.treePath"
                placeholder=""
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
      ref="tbTreeTest2Table"
      style="width: 100%;margin-top: 10px;"
    >

      <el-table-column sortable prop="name" label="名称"/>
      <el-table-column sortable prop="parentName" label="上级名称"/>
      <el-table-column sortable prop="level" label=""/>
      <el-table-column sortable prop="treePath" label=""/>
      <el-table-column sortable prop="sortValue" label="排序"/>
      <el-table-column sortable prop="createTime" label="创建时间"/>
      <el-table-column sortable prop="updateTime" label="修改时间"/>
    </el-table>
  </el-dialog>
</template>
<script>
import tbTreeTest2Api from '@/api/business/tbTreeTest2'

export default {
  data() {
    return {
      searchForm: {},
      idKey: 'id', // 标识列表数据中每一行的唯一键的名称(需要按自己的数据改一下)
      dataList: [],
      pageNo: 1,
      pageSize: 5,
      total: 0,
      loading: false,
      visible: false
    }
  },
  props: {
    studentId: {
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
        this.resetSearch()
      })
    },
    // 获取数据列表
    refreshList() {
      this.loading = true
      this.searchForm.studentId = this.studentId
      tbTreeTest2Api.getPageList(this.pageNo, this.pageSize, this.searchForm).then(
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
    }
  }
}
</script>
<style lang="scss">
</style>
