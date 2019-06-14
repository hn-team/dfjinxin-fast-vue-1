<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据探查模板名称" prop="dataExplTmplNm">
      <el-input v-model="dataForm.dataExplTmplNm" placeholder="数据探查模板名称"></el-input>
    </el-form-item>
    <el-form-item label="数据探查模板描述" prop="dataExplTmplDesc">
      <el-input v-model="dataForm.dataExplTmplDesc" placeholder="数据探查模板描述"></el-input>
    </el-form-item>
    <el-form-item label="模板类型" prop="tmplType">
      <el-input v-model="dataForm.tmplType" placeholder="模板类型"></el-input>
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
          dataExplTmplid: 0,
          dataExplTmplNm: '',
          dataExplTmplDesc: '',
          tmplType: '',
          tnmtid: ''
        },
        dataRule: {
          dataExplTmplNm: [
            { required: true, message: '数据探查模板名称不能为空', trigger: 'blur' }
          ],
          dataExplTmplDesc: [
            { required: true, message: '数据探查模板描述不能为空', trigger: 'blur' }
          ],
          tmplType: [
            { required: true, message: '模板类型不能为空', trigger: 'blur' }
          ],
          tnmtid: [
            { required: true, message: '租户ID不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dataExplTmplid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dataExplTmplid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/dataexpltmpl/info/${this.dataForm.dataExplTmplid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataExplTmplNm = data.dataexpltmpl.dataExplTmplNm
                this.dataForm.dataExplTmplDesc = data.dataexpltmpl.dataExplTmplDesc
                this.dataForm.tmplType = data.dataexpltmpl.tmplType
                this.dataForm.tnmtid = data.dataexpltmpl.tnmtid
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
              url: this.$http.adornUrl(`/wash/dataexpltmpl/${!this.dataForm.dataExplTmplid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dataExplTmplid': this.dataForm.dataExplTmplid || undefined,
                'dataExplTmplNm': this.dataForm.dataExplTmplNm,
                'dataExplTmplDesc': this.dataForm.dataExplTmplDesc,
                'tmplType': this.dataForm.tmplType,
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
