<template>
    <el-dialog :title="title" :visible.sync="dialogVisible" width="50%" :before-close="hideDialog">
        <el-card>
            <div class="topButton">
                <el-button size="mini" type="primary" @click="setAddDialog({})" icon="el-icon-plus">新建选项</el-button>
            </div>
        </el-card>
        <el-card>
        <!-- 表格 -->
        <div class="item-list">
            <el-table
                    :data="gridData"
                    style="width: 100%"
                    highlight-current-row
                    border
            >
                <el-table-column label="编码" prop="code" style="width: 30%;"></el-table-column>
                <el-table-column label="名称" prop="name" style="width: 30%;"></el-table-column>
                <el-table-column label="操作" style="width: 40%;">
                    <template slot-scope="scope">
                        <el-button-group>
                            <el-button type="warning" size="mini" @click="setAddDialog(scope.row)" icon="el-icon-edit"></el-button>
                            <el-button type="danger" size="mini" @click="delRecords(scope.row)" icon="el-icon-delete"></el-button>
                        </el-button-group>
                    </template>
                </el-table-column>
            </el-table>
        </div>
        </el-card>
        <!-- 分页 -->
        <div class="item-page">
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
        <add-metadata-item v-if="showAddDialog" :itemData="itemData" :metadataId="metadataId" @hideDialog="showAddDialog=false" @queryItem="queryItem()"></add-metadata-item>
    </el-dialog>

</template>

<script>
    import addMetadataItem from "./addMetadataItem";
    export default {
        name: "metadataItem",
        components:{
            addMetadataItem
        },
        props: {
            rowData: {
                type: Object
            }
        },
        data() {
            return {
                dialogVisible: true,
                showAddDialog:false,
                gridData: null,
                itemData:{},
                currentPage: 1, //初始页
                pagesize: 10,
                total: 0,
                metadataId: null,
                title:'编码选项',
                rules: {}
            }
        },
        created() {
            this.queryItem();
            if(this.rowData.name){
                this.title=this.rowData.name+'选项';
                this.metadataId=this.rowData.id
            }
        },
        methods: {
            hideDialog() {
                this.$emit('hideDialog')
            },
            handleSizeChange: function (size) {
                this.pagesize = size;
                this.queryItem();
            },
            handleCurrentChange: function (currentPage) {
                this.currentPage = currentPage;
                this.queryItem();
            },
            setAddDialog(row) {
                this.itemData = row,
                this.showAddDialog = true
            },
            queryItem() {
                this.$axios.post("/metadata/item/query", {
                    pageNo: this.currentPage,
                    pageSize: this.pagesize,
                    metadataId:this.rowData.id
                }).then(res => {
                    if (res.data) {
                        this.gridData = res.data.data;
                        this.total = res.data.total;
                    }
                }).catch(err=> {
                    this.$message.error("查询失败！稍后重试")
                });
            },
            delRecords(row) {
                this.$confirm('确定删除所选选项？','',{
                    confirmButtonText:'确定',
                    cancelButtonText:'取消',
                    type:'warning'
                }).then(()=>{
                    this.$axios.post("/metadata/item/delete",{
                        idList:[row.id]
                    }).then(res => {
                        if (res.data.code==200) {
                            this.$message.success("删除成功");
                            this.queryItem()
                        }
                    }).catch(err=> {
                        this.$message.error("删除失败！稍后重试")
                    });
                })
            }
        }
    }
</script>

<style scoped>
    .topButton {
        width: 100%;
        height: 8px;
        line-height: 16px;
        /*padding-bottom: 20px;*/
    }

    .item-list {
        position: relative;
    }

    .item-page {
        /* margin-top: 20px;
          margin-left: 800px; */
        position: relative;
        left: 45%;
        bottom: -10px;
    }
</style>