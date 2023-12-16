<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbStudentViewDialog
      ref="tbStudentViewDialog"
      :courseId="courseId"
    />
  </div>
</template>
<script>
import TbStudentViewDialog from './tbCourseTbStudentViewDialog'

export default {
  props: {
    courseId: {
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
