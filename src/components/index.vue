<template>
  <div>
    <el-button type="danger"
               style="float:right"
               @click="addlog=true"
               class="add">新增</el-button>
    <el-table :data="tableData"
              stripe
              border
              style="width:100%;"
              max-height="500">
      <el-table-column prop="date"
                       label="日期"
                       align="center"></el-table-column>
      <el-table-column prop="user"
                       label="用户名"
                       align="center"></el-table-column>
      <el-table-column prop="summary"
                       label="摘要"
                       align="center"></el-table-column>
      <el-table-column prop="content"
                       label="日志内容"
                       align="center"></el-table-column>
      <el-table-column label="操作"
                       align="center">
        <template slot-scope="scope">
          <el-button type='text'
                     @click="edit(tableData[scope.$index])">编辑</el-button>
          <el-button type='text'
                     @click="del(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog title="新增日志"
               width="30%"
               :visible.sync="addlog"
               v-model='tableData'>
      <edit @submit="add" @cancel="addlog=false" ref="form"></edit>
    </el-dialog>
    <el-dialog title="修改日志"
               :visible.sync="editlog"
               width="30%">
      <edit :data="editdata"
            @submit="editone" @cancel="editlog=false"> </edit>
    </el-dialog>
  </div>
</template>
<script>
import edit from '@/components/edit'
export default {
  data: function () {
    return {
      tableData: [],
      addlog: false,
      editlog: false,
      editdata: {
        date: '',
        user: '',
        summary: '',
        content: ''
      }
    }
  },
  mounted () {
    this.list()
  },
  components: {
    edit
  },
  methods: {
    list: function () {
      this.$axios.get('http://qianjia.space:8000/logs')
        .then((res) => {
          this.tableData = res.data
        }
        )
    },
    add (model) {
      this.$axios.post('http://qianjia.space:8000/logs', model)
        .then((res) => {
          this.$refs.form.reset()
          this.addlog = false
          this.list()
        })
    },
    edit (row) {
      this.editdata = row
      this.editlog = true
      console.log('将数据绑定到form', this.editdata)
    },
    editone (model) {
      console.log('获取到修改的值', model)
      let json = {
        'user': model.user,
        'summary': model.summary,
        'content': model.content
      }
      this.$axios.put('http://qianjia.space:8000/logs/' + model._id.$oid, json)
        .then((res) => {
          this.editlog = false
          this.$commonUtils.setMessage('success', '修改成功')
        })
        .catch(() => {
          this.$commonUtils.setMessage('warning', '修改失败')
        })
    },
    del (index, row) {
      this.$confirm('此操作将永久删除该数据，是否继续？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() =>
        this.$axios.delete('http://qianjia.space:8000/logs/' + row._id.$oid)
          .then((res) => {
            console.log(res.data)
            // row.splice(index, 1)
            this.$commonUtils.setMessage('success', '删除成功')
            this.list()
          })
          .catch(() => {
            this.$commonUtils.setMessage('warning', '删除失败')
          })
        )
    }
  }
}
</script>
<style>
.add {
  margin: 30px;
}
.el-form-item {
  margin-bottom: 10px !important;
  width: 450px;
}
.el-dialog__header {
  background: #e8edf8;
  height: 15px;
  line-height: 15px;
  text-align: left;
}
.el-dialog__title {
  line-height: 15px;
  font-size: 16px;
}
.el-table th {
  background: #7292e0;
  color: #fff;
}
.el-input__inner {
  height: 28px;
}
</style>