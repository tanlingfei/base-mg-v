<template>
    <div>
        <el-button type="primary"  size="small" @click="showUserSelect">查看</el-button>
        <SysFormItemDailog
                ref="sysFormItemDailog"
                :formId="formId"
        />
    </div>
</template>
<script>
    import SysFormItemDailog from './SysFormItemDailog.vue'
    export default {
        props: {
            formId: {
                type: String,
                default: ''
            }
        },
        components: {
          SysFormItemDailog
        },
        methods: {
            // 显示列表
            showUserSelect () {
                this.$refs.sysFormItemDailog.init()
            }
        }
    }
</script>
<style>

</style>
