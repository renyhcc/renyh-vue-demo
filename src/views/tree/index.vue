
<template>
  <div slot="body">
    <el-row :gutter="30" class="body-position">
      <el-col :span="6" :xs="24" :sm="24" :md="6" :lg="6" style="margin-left: 20px;">

        <!--节点数据-->
        <el-tree
          highlight-current
          :props="defaultProps"
          :load="loadNode1s"
          :expand-on-click-node="false"
          lazy>
          <span class="custom-tree-node" slot-scope="{ node, data }">
          <span @click="search(data)">{{ node.label }}</span>


          <span class="render-contents">
          <el-button class="fa"
                     type="text"
                     size="mini"
                     icon="el-icon-plus"
                     @click=newTree(data)>
          </el-button>
          <el-button class="fa"
                     type="text"
                     size="mini"
                     icon="el-icon-edit"
                     @click=editTree(data)>
          </el-button>
          <el-button class="fa"
                     type="text"
                     size="mini"
                     icon="el-icon-delete"
                     @click=deleteTree(data)>
          </el-button>
        </span>
        </span>);
        </el-tree>
      </el-col>
      <el-card class="box-card">
        <user-index :oid="checkId"></user-index>
      </el-card>
      <group-form :visible.sync="dialogVisible" :editData="editData" :dialogStatus="dialogStatus" :title="title"
                  @success="loadData"></group-form>
    </el-row>
  </div>
</template>
<script>
  import groupApi from '../../api/group'
  import UserIndex from '../table/index'
  import GroupForm from './form.vue'

  export default {
    components: {
      UserIndex,
      GroupForm
    },

    props: {},
    data () {
      return {
        checkId: null,
        total: 0,
        query: {
          pageIndex: 1
        },
        defaultProps: {
          label: 'department',
          children: 'children',
          id: 'oid'
        },
        dialogStatus: '',
        title: {
          true: '机构编辑',
          false: '机构新增'
        },
        dialogVisible: false,
        editData: {
          oid: null,
          department: '',
          pid: null
        },
        node: {
          level: 0
        }
      }
    },
    methods: {
      async loadNode1s (node, resolve) {
        this.node = node
        if (node.level === 0) {
          console.log("========================================")
          console.log(node.level)
          console.log("========================================")
          let resp = await groupApi.roleList(node.level)
          return resolve(resp.data)
        }
        if (node.level > 0) {
          console.log("========================================")
          console.log(node.data.oid)
          console.log("========================================")
          let resp = await groupApi.roleList(node.data.oid)
          return resolve(resp.data)
        }
      },
      // 新增树节点
      newTree () {
        this.dialogVisible = true
        this.dialogStatus = false
        // 对象赋值
        this.editData = {
          oid: null,
          department: this.editData.department,
          pid: this.editData.pid,
        }
        console.log("=============================");
        console.log(oid);
      },
      // 编辑树节点
      editTree (data) {
        this.dialogVisible = true
        this.dialogStatus = true
        // 数据隔离
        this.editData.oid = data.oid
        this.editData.department = data.department
        this.editData.pid = data.pid
      },
      // 删除树
      deleteTree (data) {
        this.$confirm('确定删除?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          let id = data.oid
          let resp = await groupApi.remove(id)
          if (resp.code === 200) {
            this.$message({
              message: '操作成功',
              type: 'success'
            })
            this.loadData()
          } else if (resp.status === 2011 || resp.status === 2014) {
            this.$message({
              message: resp.msg,
              type: 'failed'
            })
          }
        }).catch(() => {

        })
      },
      // 查询
      async search (data) {
        this.checkId = data.oid
      },
      loadData () {
        this.$router.push({
            name: '组织机构',
            query: {
              _t: Date.now()
            }
          }
        )
      }
    },
    mounted () {
    }
  }
</script>



<style>
  .body-position {
    margin-bottom: 20px;
  }
  .render-contents {
    float: right;
    margin-left: 10px;
  }
  .fa {
    margin-left: 10px;
  }
  .fa:hover {
    color: #e6000f;
  }
  .custom-tree-node {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    padding-right: 8px;
  }
</style>
