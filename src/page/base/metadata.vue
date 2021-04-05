<template>
    <div>
        <el-card>
            <div class="topButton">
                <el-button size="mini" type="primary" @click="setAddDialog({})" icon="el-icon-plus">新建编码</el-button>
                <!--      <el-button size="mini" type="warning" @click="openGet()">修改编码</el-button>-->
            </div>
        </el-card>
        <!-- 表格 -->
        <div class="list">
            <el-card>
            <el-table
                    :data="metadataList"
                    style="width: 100%"
                    highlight-current-row
                    border
            >
                <el-table-column label="编码" prop="code" width="150"></el-table-column>
                <el-table-column label="名称" prop="name" width="150"></el-table-column>
                <el-table-column label="创建人" prop="creator" width="120"></el-table-column>
                <el-table-column label="创建时间" prop="createTime" style="width: 15%;">
                    <template slot-scope="scope">
                        <div>{{scope.row.createTime | formatDate}}</div>
                    </template>
                </el-table-column>
                <el-table-column label="更新人" prop="modifier" width="120"></el-table-column>
                <el-table-column label="更新时间" style="width: 15%;">
                    <template slot-scope="scope">
                        <div>{{scope.row.updateTime | formatDate}}</div>
                    </template>
                </el-table-column>
                <el-table-column label="操作" style="width: 36%;">
                    <template slot-scope="scope">
                        <el-button type="primary" size="mini" @click="setItemDialog(scope.row)">查看选项</el-button>
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
        <add-dialog v-if="showAddDialog" :rowData="rowData" @hideDialog="showAddDialog=false" @query="query()"></add-dialog>
        <item-dialog v-if="showItemDialog" :rowData="rowData" @hideDialog="showItemDialog=false"></item-dialog>
    </div>
</template>

<script>
    import addDialog from './components/addMetadata'
    import itemDialog from './components/metadataItem'

    export default {
        data() {
            return {
                metadataList: [],
                currentPage: 1, //初始页
                pagesize: 10,
                total: 0,
                showAddDialog: false,
                showItemDialog: false,
                rowData: null,
            };
        },
        components: {
            addDialog,
            itemDialog
        },
        created() {
            this.query()
        },
        methods: {
            query() {
                this.$axios.post("/metadata/query", {
                    pageNo: this.currentPage,
                    pageSize: this.pagesize
                }).then(res => {
                    if (res.data) {
                        this.metadataList = res.data.data;
                        this.total = res.data.total;
                    }
                }).catch(err=> {
                    this.$message.error("查询失败！稍后重试")
                });
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
            setItemDialog(row) {
                this.rowData = row,
                this.showItemDialog = true
            },
            delRecords(row) {
              this.$confirm('确定删除所选编码？','',{
                  confirmButtonText:'确定',
                  cancelButtonText:'取消',
                  type:'warning'
              }).then(()=>{
                  this.$axios.post("/metadata/delete",{
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