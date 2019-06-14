<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据库物理名称" prop="dbPhysNm">
      <el-input v-model="dataForm.dbPhysNm" placeholder="数据库物理名称"></el-input>
    </el-form-item>
    <el-form-item label="分区ID" prop="partid">
      <el-input v-model="dataForm.partid" placeholder="分区ID"></el-input>
    </el-form-item>
    <el-form-item label="数据源ID" prop="dataSrcid">
      <el-input v-model="dataForm.dataSrcid" placeholder="数据源ID"></el-input>
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
          dbid: 0,
          dbPhysNm: '',
          partid: '',
          dataSrcid: ''
        },
        dataRule: {
          dbPhysNm: [
            { required: true, message: '数据库物理名称不能为空', trigger: 'blur' }
          ],
          partid: [
            { required: true, message: '分区ID不能为空', trigger: 'blur' }
          ],
          dataSrcid: [
            { required: true, message: '数据源ID不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dbid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dbid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/db/info/${this.dataForm.dbid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dbPhysNm = data.db.dbPhysNm
                this.dataForm.partid = data.db.partid
                this.dataForm.dataSrcid = data.db.dataSrcid
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
              url: this.$http.adornUrl(`/meta/db/${!this.dataForm.dbid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dbid': this.dataForm.dbid || undefined,
                'dbPhysNm': this.dataForm.dbPhysNm,
                'partid': this.dataForm.partid,
                'dataSrcid': this.dataForm.dataSrcid
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
