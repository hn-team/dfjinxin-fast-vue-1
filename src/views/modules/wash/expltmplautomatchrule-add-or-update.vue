<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="规则描述" prop="ruleDesc">
      <el-input v-model="dataForm.ruleDesc" placeholder="规则描述"></el-input>
    </el-form-item>
    <el-form-item label="标引对象类型" prop="markIdxObjType">
      <el-input v-model="dataForm.markIdxObjType" placeholder="标引对象类型"></el-input>
    </el-form-item>
    <el-form-item label="对象匹配策略" prop="objMatchStgy">
      <el-input v-model="dataForm.objMatchStgy" placeholder="对象匹配策略"></el-input>
    </el-form-item>
    <el-form-item label="规则表达式" prop="ruleExprs">
      <el-input v-model="dataForm.ruleExprs" placeholder="规则表达式"></el-input>
    </el-form-item>
    <el-form-item label="数据探查模板ID" prop="dataExplTmplid">
      <el-input v-model="dataForm.dataExplTmplid" placeholder="数据探查模板ID"></el-input>
    </el-form-item>
    <el-form-item label="租户ID" prop="tnmtid">
      <el-input v-model="dataForm.tnmtid" placeholder="租户ID"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          ruleid: 0,
          ruleDesc: '',
          markIdxObjType: '',
          objMatchStgy: '',
          ruleExprs: '',
          dataExplTmplid: '',
          tnmtid: ''
        },
        dataRule: {
          ruleDesc: [
            { required: true, message: '规则描述不能为空', trigger: 'blur' }
          ],
          markIdxObjType: [
            { required: true, message: '标引对象类型不能为空', trigger: 'blur' }
          ],
          objMatchStgy: [
            { required: true, message: '对象匹配策略不能为空', trigger: 'blur' }
          ],
          ruleExprs: [
            { required: true, message: '规则表达式不能为空', trigger: 'blur' }
          ],
          dataExplTmplid: [
            { required: true, message: '数据探查模板ID不能为空', trigger: 'blur' }
          ],
          tnmtid: [
            { required: true, message: '租户ID不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.ruleid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.ruleid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/expltmplautomatchrule/info/${this.dataForm.ruleid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.ruleDesc = data.expltmplautomatchrule.ruleDesc
                this.dataForm.markIdxObjType = data.expltmplautomatchrule.markIdxObjType
                this.dataForm.objMatchStgy = data.expltmplautomatchrule.objMatchStgy
                this.dataForm.ruleExprs = data.expltmplautomatchrule.ruleExprs
                this.dataForm.dataExplTmplid = data.expltmplautomatchrule.dataExplTmplid
                this.dataForm.tnmtid = data.expltmplautomatchrule.tnmtid
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/expltmplautomatchrule/${!this.dataForm.ruleid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'ruleid': this.dataForm.ruleid || undefined,
                'ruleDesc': this.dataForm.ruleDesc,
                'markIdxObjType': this.dataForm.markIdxObjType,
                'objMatchStgy': this.dataForm.objMatchStgy,
                'ruleExprs': this.dataForm.ruleExprs,
                'dataExplTmplid': this.dataForm.dataExplTmplid,
                'tnmtid': this.dataForm.tnmtid
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
