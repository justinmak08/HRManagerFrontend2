<template>
    <div>
        <div>
            <el-table
                    :data="tableData"
                    border
                    style="width: 80%;margin-left: 100px;margin-top: 30px;">
                <el-table-column
                        fixed
                        prop="id"
                        label="id"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="name"
                        label="name"
                        width="150">
                </el-table-column>
                <el-table-column
                        prop="manager"
                        label="manager"
                        width="150">
                </el-table-column>
                <el-table-column
                        prop="analyst"
                        label="analyst"
                        width="180">
                </el-table-column>
                <el-table-column
                        prop="designer"
                        label="designer"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="programmer"
                        label="programmer"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="tester"
                        label="tester"
                        width="120">
                </el-table-column>
                <el-table-column
                        fixed="right"
                        label=""
                        width="100">
                    <template slot-scope="scope">
                        <el-button @click="findById(scope.row.id)" type="text" size="small">Edit</el-button>
                        <el-button @click="deleteById(scope.row)" type="text" size="small">Delete</el-button>
                    </template>
                </el-table-column>

            </el-table>
            <el-pagination
                    background
                    layout="prev, pager, next"
                    :total="total"
                    :page-size="pageSize"
                    @current-change="change"
            >
            </el-pagination>
        </div>
    </div>
</template>

<script>
    export default {
        name: "TeamsTable",
        data() {
            return {
                total:0,
                pageSize:5,
                tableData: [],
                currentPage:0
            }
        },
        methods: {
            findById(id) {
                this.$router.push({path:'/teams/basicUpdate',query:{id:id}})
            },
            change(currentPage) {
               this.currentPage = currentPage
               this.getRequest('/teams/basic/findByPage/' + currentPage).then(resp => {
                if (resp) {
                    console.log(resp.data)
                    this.pageSize = resp.pageSize
                    this.total = resp.total
                    this.tableData = resp.data;
                }
             })
            },
            deleteById(row) {
                this.$confirm('此操作将永久删除【' + row.name + '】, 是否继续?', '提示', {
                    confirmButtonText: 'Delete',
                    cancelButtonText: 'Cancel',
                    type: 'warning'
                }).then(() => {
                    this.deleteRequest("/teams/basic/deleteById/" + row.id).then(resp => {
               this.getRequest('/teams/basic/findByPage/1').then(resp => {
                if (resp) {
                    console.log(resp.data)
                    this.pageSize = resp.pageSize
                    this.total = resp.total
                    this.tableData = resp.data;
                }
             })
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: 'Delete cancelled'
                    });
                });
            }
        },

        initProject() {
            this.getRequest('/teams/basic/findByPage/'+ data.page).then(resp => {
                if (resp) {
                    this.tableData = resp;
                }
            })
        },


        created() {
             this.getRequest('teams/basic/findByPage/1').then(resp => {

                console.log(resp.data)
                this.pageSize = resp.pageSize
                this.total = resp.total
                this.tableData = resp.data
             })
        }
    }
</script>

<style scoped>

</style>