<template>
  <div>
    <el-button type="primary" size="small" @click="showUserSelect">查看</el-button>
    <TbStudentViewDialog
      ref="tbStudentViewDialog"
      :teacherId="teacherId"
    />
  </div>
</template>
<script>
import TbStudentViewDialog from './tbTeacherTbStudentViewDialog'

export default {
  props: {
    teacherId: {
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
