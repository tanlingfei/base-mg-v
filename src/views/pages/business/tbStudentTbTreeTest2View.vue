<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbTreeTest2ViewDialog
      ref="tbTreeTest2ViewDialog"
      :studentId="studentId"
    />
  </div>
</template>
<script>
import TbTreeTest2ViewDialog from './tbStudentTbTreeTest2ViewDialog'

export default {
  props: {
    studentId: {
      type: String,
      default: ''
    }
  },
  components: {
    TbTreeTest2ViewDialog
  },
  methods: {
    // 显示列表
    showUserSelect() {
      this.$refs.tbTreeTest2ViewDialog.init()
    }
  }
}
</script>
<style>

</style>
