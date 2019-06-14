<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="数据库ID" prop="dbid">
      <el-input v-model="dataForm.dbid" placeholder="数据库ID"></el-input>
    </el-form-item>
    <el-form-item label="数据表物理名称" prop="dataTblPhysNm">
      <el-input v-model="dataForm.dataTblPhysNm" placeholder="数据表物理名称"></el-input>
    </el-form-item>
    <el-form-item label="数据表中文名称" prop="dataTblCnNm">
      <el-input v-model="dataForm.dataTblCnNm" placeholder="数据表中文名称"></el-input>
    </el-form-item>
    <el-form-item label="数据表描述" prop="dataTblDesc">
      <el-input v-model="dataForm.dataTblDesc" placeholder="数据表描述"></el-input>
    </el-form-item>
    <el-form-item label="创建日期" prop="createDt">
      <el-input v-model="dataForm.createDt" placeholder="创建日期"></el-input>
    </el-form-item>
    <el-form-item label="更新日期" prop="updDt">
      <el-input v-model="dataForm.updDt" placeholder="更新日期"></el-input>
    </el-form-item>
    <el-form-item label="删除日期" prop="delDt">
      <el-input v-model="dataForm.delDt" placeholder="删除日期"></el-input>
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
          dataTblid: 0,
          dbid: '',
          dataTblPhysNm: '',
          dataTblCnNm: '',
          dataTblDesc: '',
          createDt: '',
          updDt: '',
          delDt: ''
        },
        dataRule: {
          dbid: [
            { required: true, message: '数据库ID不能为空', trigger: 'blur' }
          ],
          dataTblPhysNm: [
            { required: true, message: '数据表物理名称不能为空', trigger: 'blur' }
          ],
          dataTblCnNm: [
            { required: true, message: '数据表中文名称不能为空', trigger: 'blur' }
          ],
          dataTblDesc: [
            { required: true, message: '数据表描述不能为空', trigger: 'blur' }
          ],
          createDt: [
            { required: true, message: '创建日期不能为空', trigger: 'blur' }
          ],
          updDt: [
            { required: true, message: '更新日期不能为空', trigger: 'blur' }
          ],
          delDt: [
            { required: true, message: '删除日期不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.dataTblid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.dataTblid) {
            this.$http({
              url: this.$http.adornUrl(`/meta/datatbl/info/${this.dataForm.dataTblid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.dbid = data.datatbl.dbid
                this.dataForm.dataTblPhysNm = data.datatbl.dataTblPhysNm
                this.dataForm.dataTblCnNm = data.datatbl.dataTblCnNm
                this.dataForm.dataTblDesc = data.datatbl.dataTblDesc
                this.dataForm.createDt = data.datatbl.createDt
                this.dataForm.updDt = data.datatbl.updDt
                this.dataForm.delDt = data.datatbl.delDt
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
              url: this.$http.adornUrl(`/meta/datatbl/${!this.dataForm.dataTblid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'dataTblid': this.dataForm.dataTblid || undefined,
                'dbid': this.dataForm.dbid,
                'dataTblPhysNm': this.dataForm.dataTblPhysNm,
                'dataTblCnNm': this.dataForm.dataTblCnNm,
                'dataTblDesc': this.dataForm.dataTblDesc,
                'createDt': this.dataForm.createDt,
                'updDt': this.dataForm.updDt,
                'delDt': this.dataForm.delDt
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
