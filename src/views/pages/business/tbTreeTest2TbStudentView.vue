<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbStudentViewDialog
      ref="tbStudentViewDialog"
      :treesId="treesId"
    />
  </div>
</template>
<script>
import TbStudentViewDialog from './tbTreeTest2TbStudentViewDialog'

export default {
  props: {
    treesId: {
      type: String,
      default: ''
    }
  },
  components: {
    TbStudentViewDialog
  },
  methods: {
    // 显示列表
    showUserSelect() {
      this.$refs.tbStudentViewDialog.init()
    }
  }
}
</script>
<style>

</style>
