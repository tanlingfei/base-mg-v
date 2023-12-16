<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbCourseViewDialog
      ref="tbCourseViewDialog"
      :studentId="studentId"
    />
  </div>
</template>
<script>
import TbCourseViewDialog from './tbStudentTbCourseViewDialog'

export default {
  props: {
    studentId: {
      type: String,
      default: ''
    }
  },
  components: {
    TbCourseViewDialog
  },
  methods: {
    // 显示列表
    showUserSelect() {
      this.$refs.tbCourseViewDialog.init()
    }
  }
}
</script>
<style>

</style>
