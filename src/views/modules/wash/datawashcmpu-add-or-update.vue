<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据清洗运算代码" prop="dataWashCmpuCd">
      <el-input v-model="dataForm.dataWashCmpuCd" placeholder="数据清洗运算代码"></el-input>
    </el-form-item>
    <el-form-item label="数据清洗运算名称" prop="dataWashCmpuNm">
      <el-input v-model="dataForm.dataWashCmpuNm" placeholder="数据清洗运算名称"></el-input>
    </el-form-item>
    <el-form-item label="数据清洗运算描述" prop="dataWashCmpuDesc">
      <el-input v-model="dataForm.dataWashCmpuDesc" placeholder="数据清洗运算描述"></el-input>
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
          dataWashCmpuid: 0,
          dataWashCmpuCd: '',
          dataWashCmpuNm: '',
          dataWashCmpuDesc: ''
        },
        dataRule: {
          dataWashCmpuCd: [
            { required: true, message: '数据清洗运算代码不能为空', trigger: 'blur' }
          ],
          dataWashCmpuNm: [
            { required: true, message: '数据清洗运算名称不能为空', trigger: 'blur' }
          ],
          dataWashCmpuDesc: [
            { required: true, message: '数据清洗运算描述不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dataWashCmpuid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dataWashCmpuid) {
            this.$http({
              url: this.$http.adornUrl(`/wash/datawashcmpu/info/${this.dataForm.dataWashCmpuid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dataWashCmpuCd = data.datawashcmpu.dataWashCmpuCd
                this.dataForm.dataWashCmpuNm = data.datawashcmpu.dataWashCmpuNm
                this.dataForm.dataWashCmpuDesc = data.datawashcmpu.dataWashCmpuDesc
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
              url: this.$http.adornUrl(`/wash/datawashcmpu/${!this.dataForm.dataWashCmpuid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dataWashCmpuid': this.dataForm.dataWashCmpuid || undefined,
                'dataWashCmpuCd': this.dataForm.dataWashCmpuCd,
                'dataWashCmpuNm': this.dataForm.dataWashCmpuNm,
                'dataWashCmpuDesc': this.dataForm.dataWashCmpuDesc
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
