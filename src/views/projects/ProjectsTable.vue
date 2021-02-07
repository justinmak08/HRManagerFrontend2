<template>
  <div>
    <div>
      <div style="display: flex; justify-content: space-between">
        <div>
          <el-button
            type="primary"
            icon="el-icon-plus"
            @click="showAddProjectView"
          >
            Add Project
          </el-button>
        </div>
      </div>
    </div>
    <div style="margin-top: 10px">
      <el-table
        :data="tableData"
        stripe
        border
        v-loading="loading"
        element-loading-text="正在加载..."
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.8)"
        style="width: 100%"
      >
        <el-table-column fixed prop="id" label="id" width="100">
        </el-table-column>
        <el-table-column prop="name" label="name" width="150">
        </el-table-column>
        <el-table-column prop="language" label="language" width="150">
        </el-table-column>
        <el-table-column prop="startDate" label="start date" width="180">
        </el-table-column>
        <el-table-column prop="dueDate" label="due date" width="100">
        </el-table-column>
        <el-table-column prop="description" label="description" width="100">
        </el-table-column>
        <el-table-column fixed="right" label="" width="100">
          <template slot-scope="scope">
            <el-button @click="findById(scope.row)" type="text" size="small"
              >Edit</el-button
            >
            <el-button @click="deleteById(scope.row)" type="text" size="small"
              >Delete</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <div style="display: flex; justify-content: flex-end">
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
    <el-dialog :title="title" :visible.sync="dialogVisible" width="80%">
      <div>
        <el-form
          :model="ruleForm"
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="form-container"
        >
          <el-row>
            <el-col :span="6">
              <el-form-item label="Name" prop="name">
                <el-input v-model="ruleForm.name"></el-input>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="Language" prop="language">
                <el-select v-model="ruleForm.language" placeholder="Select">
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  >
                  </el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="Start Date" prop="startDate">
                <el-date-picker v-model="ruleForm.startDate"></el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="Due Date" prop="dueDate">
                <el-date-picker v-model="ruleForm.dueDate"></el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :span="6">
              <el-form-item label="Description" prop="description">
                <el-input v-model="ruleForm.description"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
        </el-form>
      </div>
      <span slot="footer" class="dialog-footer">
        <!-- <el-button @click="resetForm('ruleForm')">Clear</el-button> -->
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="doAddProjects">Confirm</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "ProjectsTable",
  data() {
    return {
      total: 0,
      pageSize: 5,
      tableData: [],
      currentPage: 0,
      loading: false,
      title: "",
      dialogVisible: false,
      ruleForm: {
        name: "",
        language: "",
        startDate: "",
        dueDate: "",
        description: "",
      },
      rules: {
        name: [{ required: true, message: "Enter name", trigger: "blur" }],
        language: [
          { required: true, message: "Enter language", trigger: "blur" },
        ],
        startDate: [
          { required: true, message: "Enter start date", trigger: "change" },
        ],
        dueDate: [
          { required: true, message: "Enter due date", trigger: "blur" },
        ],
        description: [
          { required: true, message: "Enter description", trigger: "blur" },
        ],
      },
      options: [
        {
          value: "Java",
          label: "Java",
        },
        {
          value: "JavaScript",
          label: "JavaScript",
        },
        {
          value: "Python",
          label: "Python",
        },
        {
          value: "C++",
          label: "C++",
        },
        {
          value: "HTML",
          label: "HTML",
        },
        {
          value: "C#",
          label: "C#",
        },
        {
          value: "C",
          label: "C",
        },
        {
          value: "Ruby",
          label: "Ruby",
        },
      ],
      value: "",
    };
  },

  methods: {
    findById(data) {
      this.title = "Edit Project";
      this.ruleForm = data;
      this.dialogVisible = true;
    },
    change(currentPage) {
      this.currentPage = currentPage;
      this.getRequest("/projects/basic/findByPage/" + currentPage).then(
        (resp) => {
          if (resp) {
            console.log(resp.data);
            this.pageSize = resp.pageSize;
            this.total = resp.total;
            this.tableData = resp.data;
          }
        }
      );
    },
    emptyProject() {
      this.ruleForm = {
        name: "",
        language: "",
        startDate: "",
        dueDate: "",
        description: "",
      };
    },
    showAddProjectView() {
      this.emptyProject();
      this.title = "Add Team";
      this.dialogVisible = true;
    },
    doAddProjects() {
      if (this.ruleForm.id) {
        this.$refs["ruleForm"].validate((valid) => {
          if (valid) {
            this.putRequest(
              "/projects/basic/projectsUpdate",
              this.ruleForm
            ).then((resp) => {
              if (resp == 1) {
                this.dialogVisible = false;
                this.getRequest("/projects/basic/findByPage/1").then((resp) => {
                  if (resp) {
                    console.log(resp.data);
                    this.pageSize = resp.pageSize;
                    this.total = resp.total;
                    this.tableData = resp.data;
                  }
                });
              }
            });
          }
        });
      } else {
        this.$refs["ruleForm"].validate((valid) => {
          if (valid) {
            this.postRequest("/projects/basic/save", this.ruleForm).then(
              (resp) => {
                if (resp == 1) {
                  this.dialogVisible = false;
                  this.getRequest("/projects/basic/findByPage/1").then(
                    (resp) => {
                      if (resp) {
                        console.log(resp.data);
                        this.pageSize = resp.pageSize;
                        this.total = resp.total;
                        this.tableData = resp.data;
                      }
                    }
                  );
                }
              }
            );
          }
        });
      }
    },
    deleteById(row) {
      this.$confirm("此操作将永久删除【" + row.name + "】, 是否继续?", "提示", {
        confirmButtonText: "Delete",
        cancelButtonText: "Cancel",
        type: "warning",
      })
        .then(() => {
          this.deleteRequest("/projects/basic/deleteById/" + row.id).then(
            (resp) => {
              this.getRequest("/projects/basic/findByPage/1").then((resp) => {
                if (resp) {
                  console.log(resp.data);
                  this.pageSize = resp.pageSize;
                  this.total = resp.total;
                  this.tableData = resp.data;
                }
              });
            }
          );
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "Delete cancelled",
          });
        });
    },
  },

  initProject() {
    this.getRequest("/projects/basic/findByPage/" + data.page).then((resp) => {
      if (resp) {
        this.tableData = resp;
      }
    });
  },

  created() {
    this.getRequest("projects/basic/findByPage/1").then((resp) => {
      console.log(resp.data);
      this.pageSize = resp.pageSize;
      this.total = resp.total;
      this.tableData = resp.data;
    });
  },
};
</script>

<style scoped>
</style>