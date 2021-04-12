<template>
  <div>
    <el-card>
      <div class="topButton">
        <el-button size="mini" type="primary" @click="setAddDialog({})" icon="el-icon-plus">添加人员</el-button>
      </div>
    </el-card>
    <!-- 表格 -->
    <div class="list">
      <el-card>
        <el-table
                :data="userList"
                style="width: 100%"
                highlight-current-row
                border
        >
          <el-table-column label="ID" prop="id"></el-table-column>
          <el-table-column label="姓名" prop="userName"></el-table-column>
          <el-table-column label="登录名" prop="loginName"></el-table-column>
          <el-table-column label="联系电话" prop="userMobile"></el-table-column>
          <el-table-column label="性别" prop="userSex">
            <template slot-scope="scope">
              {{sexEnum[scope.row.userSex]}}
            </template>
          </el-table-column>
          <el-table-column label="门店" prop="deptNo">
            <template slot-scope="scope">
              {{deptOption[scope.row.deptNo]}}
            </template>
          </el-table-column>
          <el-table-column label="操作" style="width: 36%;">
            <template slot-scope="scope">
              <el-button-group>
                <el-button type="warning" size="mini" @click="setAddDialog(scope.row)" icon="el-icon-edit"></el-button>
                <el-button type="danger" size="mini" @click="delRecords(scope.row)" icon="el-icon-delete"></el-button>
              </el-button-group>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <div class="page">
          <el-pagination
                  @size-change="handleSizeChange"
                  @current-change="handleCurrentChange"
                  :current-page="currentPage"
                  :page-sizes="[5, 10, 20, 40]"
                  :page-size="pagesize"
                  layout="total, sizes, prev, pager, next, jumper"
                  :total="this.total"
          ></el-pagination>
        </div>
      </el-card>
    </div>
    <add-dialog v-if="showAddDialog" :rowData="rowData" :deptOption="deptOption" @hideDialog="showAddDialog=false" @query="query()"></add-dialog>
  </div>
</template>

<script>
  import addDialog from './components/addUser'

  export default {
    data() {
      return {
        userList: [],
        currentPage: 1, //初始页
        pagesize: 10,
        total: 0,
        showAddDialog: false,
        rowData: null,
        sexEnum:{
          1:'男',
          2:'女'
        },
        deptOption:{}
      };
    },
    components: {
      addDialog
    },
    computed: {
    },
    created() {
      this.query()
    },
    methods: {
      async query() {
        this.$axios.post("/user/query", {
            pageNo: this.currentPage,
            pageSize: this.pagesize
          }).then(res => {
            if (res.data) {
              this.userList = res.data.data;
              this.total = res.data.total;
            }
          }).catch(err=> {
            this.$message.error("查询失败！稍后重试")
          })
        await this.queryMetadataItem('MD')
      },
      handleSizeChange: function (size) {
        this.pagesize = size;
        this.query();
      },
      handleCurrentChange: function (currentPage) {
        this.currentPage = currentPage;
        this.query();
      },
      setAddDialog(row) {
        this.rowData = row,
        this.showAddDialog = true
      },
      delRecords(row) {
        this.$confirm('确定删除所选人员？','',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          this.$axios.post("/user/delete",{
            idList:[row.id]
          }).then(res => {
            if (res.data.code==200) {
              this.$message.success("删除成功");
              this.query()
            }
          }).catch(err=> {
            this.$message.error("删除失败！稍后重试")
          });
        })
      },
      queryMetadataItem(code) {
        this.$axios.post("/metadata/item/get", {
            code: code
          }).then(res => {
          if (res.data.code === 200) {
            this.deptOption=res.data.data
          } else {
            this.$message.error(res.data.message);
          }
        }).catch(err => {
          this.$message.error("查询基础信息失败！请稍后重试")
        });
      }
    }
  };
</script>

<style>
  .topButton {
    width: 100%;
    height: 10px;
    line-height: 20px;
    padding-bottom: 20px;
  }

  .list {
    position: relative;
  }

  .page {
    /* margin-top: 20px;
      margin-left: 800px; */
    position: absolute;
    right: 0;
    bottom: -50px;
  }

</style>