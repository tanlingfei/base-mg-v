<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbTeacherViewDialog
      ref="tbTeacherViewDialog"
      :studentId="studentId"
    />
  </div>
</template>
<script>
import TbTeacherViewDialog from './tbStudentTbTeacherViewDialog'

export default {
  props: {
    studentId: {
      type: String,
      default: ''
    }
  },
  components: {
    TbTeacherViewDialog
  },
  methods: {
    // 显示列表
    showUserSelect() {
      this.$refs.tbTeacherViewDialog.init()
    }
  }
}
</script>
<style>

</style>
