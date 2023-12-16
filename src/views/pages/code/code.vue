<template>
  <el-form ref="form" label-width="150px" size="small" :model="form" style="margin-left:5px;margin-right:500px">
    <el-form-item label="选择生成文件">
      <el-checkbox-group v-model="form.genList">
        <el-checkbox label="api" checked>前端api</el-checkbox>
        <el-checkbox label="vue" checked>前端页面</el-checkbox>
        <el-checkbox label="po" checked>后端Po实体类</el-checkbox>
        <el-checkbox label="vo" checked>后端Vo实体类</el-checkbox>
        <el-checkbox label="mapper" checked>后端Mapper生成</el-checkbox>
        <el-checkbox label="xml" checked>后端Mapper XML生成</el-checkbox>
        <el-checkbox label="service" checked>后端Service生成</el-checkbox>
        <el-checkbox label="impl" checked>后端Service实现生成</el-checkbox>
        <el-checkbox label="controller" checked>后端Controller生成</el-checkbox>
        <el-checkbox label="main" :checked="false">启动类</el-checkbox>
      </el-checkbox-group>
    </el-form-item>
    <!--  <el-form-item label="数据库连接">
        <el-input v-model="form.connectionURL" ></el-input>
      </el-form-item>
      <el-form-item label="数据库用户名">
        <el-input v-model="form.username"></el-input>
      </el-form-item>
      <el-form-item label="数据库密码">
        <el-input v-model="form.password" type="password"></el-input>
      </el-form-item>-->
    <el-form-item label="激活码">
      <el-input v-model="form.authCode" placeholder="联系作者免费获取,激活后,后续不用再输入"></el-input>
    </el-form-item>
    <el-form-item label="选择表单">
      <!--      <el-input v-model="form.tabName"></el-input>-->
      <el-select
        v-model="form.tabName"
        placeholder="选择主表" @change="getInputItem">
        <el-option
          v-for="item in form.inputList"
          :key="item.name"
          :label="item.description"
          :value="item.name"/>
      </el-select>
    </el-form-item>
<!--    <el-form-item label="显示方式">
      <el-radio-group v-model="form.genType">
        <el-radio label="0">常规</el-radio>
        <el-radio label="1">树形</el-radio>
      </el-radio-group>
    </el-form-item>-->
    <el-form-item label="生成代码包路径">
      <el-input v-model="form.packageName"></el-input>
    </el-form-item>
    <el-form-item label="生成代码后端目录">
      <el-input v-model="form.filePath"></el-input>
    </el-form-item>
    <el-form-item label="生成代码前端目录">
      <el-input v-model="form.frontPath"></el-input>
    </el-form-item>
    <!--  <el-form-item label="模板文件目录">-->
    <!--    <el-input v-model="form.ftlPath"></el-input>-->
    <!--  </el-form-item>-->
    <el-form-item label="作者">
      <el-input v-model="form.author"></el-input>
    </el-form-item>
    <el-form-item
      v-for="(item, index) in form.mulList"
      :label="'关联表信息' + (index+1)"
    >
      <div style="border:1px solid #dcdfe6;padding: 5px">
        <!--      <el-input v-model="item.target" placeholder="设置字段"></el-input>-->

        <el-form-item label="设置字段">
          <el-select
            v-model="item.target"
            placeholder="设置字段">
            <el-option
              v-for="item in form.itemList"
              :key="item.name"
              :label="item.description"
              :value="item.name"/>
          </el-select>
        </el-form-item>
        <el-form-item label="说明">
          <el-input v-model="item.modRemark" placeholder="说明"></el-input>
        </el-form-item>
        <el-form-item label="输入方式">
          <el-select v-model="item.mod" placeholder="输入方式" @change="changeSelectStyle(item)" >
            <el-option label="手动输入" value="default"></el-option>
            <el-option label="下拉选择" value="select"></el-option>
            <el-option label="单选按钮" value="radio"></el-option>
            <el-option label="多选框" value="checkbox"></el-option>
            <el-option label="弹窗选择" value="pop"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="查询数据源表">
          <el-select v-model="item.data"
                     placeholder="查询数据源表" @change="getDuiyingItem($event,index)">
            <el-option
              v-for="it in form.inputList"
              :key="it.name"
              :label="it.description"
              :value="it.name"/>
          </el-select>
        </el-form-item>

        <el-form-item label="对应值字段">
          <!--          <el-input v-model="item.key" placeholder="对应值字段"></el-input>-->
          <el-select
            v-model="item.key"
            placeholder="对应值字段">
            <el-option
              v-for="it in item.duiyingList"
              :label="it.description"
              :value="it.name"/>
          </el-select>
        </el-form-item>
        <el-form-item label="显示值字段">
          <!--          <el-input v-model="item.value" placeholder="显示值字段"></el-input>-->
          <el-select
            v-model="item.value"
            placeholder="显示值字段">
            <el-option
              v-for="it in item.duiyingList"
              :label="it.description"
              :value="it.name"/>
          </el-select>
        </el-form-item>
        <el-form-item label="查询条件">
          <el-input v-model="item.where" placeholder="查询条件"></el-input>
        </el-form-item>
        <el-form-item label="引入模块目录">
          <el-input v-model="item.pageDir" placeholder="引入模块目录"></el-input>
        </el-form-item>
        <el-form-item label="选择方式">
          <el-radio-group v-model="item.isMul" @change="showOrHidMiddleInfo($event,'middleInfo'+index)">
            <el-radio label="false" v-if="item.mod !== 'checkbox'" >单选</el-radio>
            <el-radio label="true" v-if="item.mod !== 'radio'" >多选</el-radio>
          </el-radio-group>
        </el-form-item>
        <div :id="'middleInfo'+index" style="border:1px solid #dcdfe6;padding: 5px;display: none">
          <el-form-item label="选择中间表">
            <el-select v-model="item.mulTable"
                       placeholder="选择中间表" @change="getXianShiItem($event,index)">
              <el-option
                v-for="item in form.inputList"
                :key="item.name"
                :label="item.description"
                :value="item.name"/>
            </el-select>
          </el-form-item>
          <el-form-item label="主表对应字段名称">
            <!--            <el-input v-model="item.mulMainColum" placeholder="多选主表对应字段名称"></el-input>-->
            <el-select
              v-model="item.mulMainColum"
              placeholder="主表对应字段名称">
              <el-option
                v-for="it in item.xianshiList"
                :label="it.description"
                :value="it.name"/>
            </el-select>
          </el-form-item>
          <el-form-item label="次表对应字段名称">
            <!--            <el-input v-model="item.mulSecColum" placeholder="多选次表对应字段名称"></el-input>-->
            <el-select
              v-model="item.mulSecColum"
              placeholder="次表对应字段名称">
              <el-option
                v-for="it in item.xianshiList"
                :label="it.description"
                :value="it.name"/>
            </el-select>
          </el-form-item>
        </div>
        <el-button style="margin-top: 3px" @click="deleteItem(index)" type="primary" icon="el-icon-delete"></el-button>
      </div>
    </el-form-item>
    <el-form-item label="是否生成左侧部门导航">
      <el-radio-group v-model="form.isGenLeftDept">
        <el-radio label="false">否</el-radio>
        <el-radio label="true">是</el-radio>
      </el-radio-group>
      <span style="margin-left: 10px;color: red">(主表中需要存在dept_id字段关联部门(sys_dept))表 或者中间表关联</span>
    </el-form-item>
    <el-form-item label="部门数据加载方式">
      <el-radio-group v-model="form.isLazyDept">
        <el-radio label="false">立即加载</el-radio>
        <el-radio label="true">懒加载</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="onSubmit" v-loading.fullscreen.lock="loading">生成代码</el-button>
      <el-button @click="addItem">新增关联表信息</el-button>
    </el-form-item>
  </el-form>
</template>
<script>
import request from '@/utils/request'
import formApi from '@/api/generator/form/sysForm'
import itemApi from '@/api/generator/form/sysFormItem'

export default {
  data() {
    return {
      loading: false,
      form: {
        genType: "0",
        genList: [],
        id: '',
        /* connectionURL:'jdbc:mysql://localhost:3306/base-lf?characterEncoding=utf-8&useSSL=false&nullCatalogMeansCurrent=true',
         username:'root',
         password:'root',*/
        /* driverName:'com.mysql.cj.jdbc.Driver',*/
        tabName: '',
        packageName: 'com.lanf.business',
        filePath: 'D:\\codeTmp\\base_mf_sig_v\\src\\main',
        frontPath: 'D:\\codeTmp\\base-front-v\\src',
        /* ftlPath:'F:/basemf-sig/src/main/resources/template',*/
        mulList: [],
        searchObj: {}, // 查询表单对象
        inputList: [], //表列表
        itemList: [],  //字段列表
        isGenLeftDept: 'false',
        isLazyDept: 'false'
      },
      searchForm: {tabName: ""}
    }
  },
  methods: {
    save(data) {
      return request({
        url: '/code/generator/save',
        method: 'post',
        data: data
      })
    },

    showOrHidMiddleInfo(value,id){
      if(value == 'false'){
        document.getElementById(id).style.display='none'
      }else{
        document.getElementById(id).style.display='block'
      }
    },

    changeSelectStyle(item){
      if(item.mod == 'checkbox'){
        item.isMul = 'true'
      }else if(item.mod == 'radio'){
        item.isMul = 'false'
      }
    },

    getInputType() {
      formApi.getAllSysForm().then(
        response => {
          //this.list = response.data.list
          this.form.inputList = response.data;
        }
      )
    },

    getInputItem(tabName, type) {
      this.searchForm.tabName = tabName;
      itemApi.getAllSysFormItem(this.searchForm).then(
        response => {
          //this.list = response.data.list
          this.form.itemList = response.data;
        }
      )
    },

    getDuiyingItem(tabName, index) {
      this.searchForm.tabName = tabName;
      itemApi.getAllSysFormItem(this.searchForm).then(
        response => {
          //this.list = response.data.list
          this.$nextTick(function (){
            this.form.mulList[index].duiyingList= response.data;
          })
        }
      )
    },

    getXianShiItem(tabName, index) {
      this.searchForm.tabName = tabName;
      itemApi.getAllSysFormItem(this.searchForm).then(
        response => {
          //this.list = response.data.list
          this.form.mulList[index].xianshiList = response.data;
        }
      )
    },

    onSubmit() {
      let tabName = this.form.tabName;
      if (tabName == '') {
        this.$message.warning('请填写表名')
        return;
      }
      this.loading = true
      this.save(this.form).then(response => {
        this.loading = false
        if (response.code == '200') {
          this.$message.success('代码生成成功，如果生成路径不在所属项目路径，请将生成的代码拷贝至项目中，重启系统生效')
        }
      }).catch(() => {
        this.loading = false
      })
      console.log('submit!');
    },
    deleteItem(i) {
      this.form.mulList.splice(i, 1)
    },
    addItem() {
      this.form.mulList.push({
        target: "",
        mod: "",
        data: "",
        key: "",
        value: "",
        where: "",
        pageDir: "",
        isMul: "false",
        mulTable: "",
        mulMainColum: "",
        mulSecColum: "",
        duiyingList:[],
        xianshiList:[]
      });
    }
  },
  created() {
    this.getInputType()
  }
}
</script>
