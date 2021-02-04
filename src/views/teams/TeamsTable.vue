<template>
    <div>
        <div>
            <el-table
                    :data="teamsTableData"
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
                        prop="tester"
                        label="tester"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="programmer"
                        label="programmer"
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
                emps: [],
            }
        },


        methods: {
            findById(id) {
                this.$router.push({path:'/teamsUpdate',query:{id:id}})
            },

            initEmps(type) {
                this.loading = true;
                let url = '/employee/basic/?page=' + this.page + '&size=' + this.size;
                if (type && type == 'advanced') {
                    if (this.searchValue.politicId) {
                        url += '&politicId=' + this.searchValue.politicId;
                    }
                    if (this.searchValue.nationId) {
                        url += '&nationId=' + this.searchValue.nationId;
                    }
                    if (this.searchValue.jobLevelId) {
                        url += '&jobLevelId=' + this.searchValue.jobLevelId;
                    }
                    if (this.searchValue.posId) {
                        url += '&posId=' + this.searchValue.posId;
                    }
                    if (this.searchValue.engageForm) {
                        url += '&engageForm=' + this.searchValue.engageForm;
                    }
                    if (this.searchValue.departmentId) {
                        url += '&departmentId=' + this.searchValue.departmentId;
                    }
                    if (this.searchValue.beginDateScope) {
                        url += '&beginDateScope=' + this.searchValue.beginDateScope;
                    }
                } else {
                    url += "&name=" + this.keyword;
                }
                this.getRequest(url).then(resp => {
                    this.loading = false;
                    if (resp) {
                        this.emps = resp.data;
                        this.total = resp.total;
                    }
                });
            },

            change(currentPage) {
                this.currentPage = currentPage
                // const _this = this
                this.getRequest('/teams/basic/findByPage/'+currentPage).then(resp => {
                    // this.loading = false;
                    this.teamsTableData = resp.data.data
                 });   
                // axios.get('http://localhost:8081/teams/basic/findByPage/'+currentPage).then(function (resp) {
                //     _this.teamsTableData = resp.data.data
                // })
            },
            deleteById(row) {
                this.$confirm('Delete《'+row.name+'》?', '提示', {
                    confirmButtonText: 'Delete',
                    cancelButtonText: 'Cancel',
                    type: 'warning'
                }).then(() => {
                    const _this = this
                    axios.delete('http://localhost:8081/teams/basic/deleteById/'+row.id).then(function (resp) {
                        if(resp.data == 1){
                            axios.get('http://localhost:8081/teams/basic/findByPage/'+_this.currentPage).then(function (resp) {
                                // _this.$message({
                                //     type: 'success',
                                //     message: '删除成功!'
                                // });
                                //_this.teamsTableData = resp.data.data

                                _this.$alert('Deleted！', '', {
                                    confirmButtonText: 'Done',
                                    callback: action => {
                                        _this.teamsTableData = resp.data.data
                                    }
                                });

                            })
                        }
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: 'Delete cancelled'
                    });
                });
            }
        },

        data() {
            return {
                total:0,
                pageSize:5,
                teamsTableData: [],
                currentPage:0
            }
        },

        created() {
            const _this = this
            axios.get('http://localhost:8081/teams/basic/findByPage/1').then(function (resp) {
                console.log(resp.data)
                _this.pageSize = resp.data.pageSize
                _this.total = resp.data.total
                _this.teamsTableData = resp.data.data
            })
        }
    }
</script>

<style scoped>

</style>