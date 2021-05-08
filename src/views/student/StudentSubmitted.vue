<template>
    <div class="student-submitted-wrap">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-fa fa-check"></i> Представленные задания
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>

        <div class="container">
            <div class="query-form">
                <el-row :gutter="20">
                    <el-col :offset="15" :span="3">
                        <el-input @keyup.enter.native="query" onkeyup="value=value.replace(/[^\d]/g,'')"
                                  placeholder="№" v-model="queryForm.homeworkId"/>
                    </el-col>
                    <el-col :span="3">
                        <el-input @keyup.enter.native="query" placeholder="Название" v-model="queryForm.homeworkTitle"/>
                    </el-col>
                    <el-col :span="3">
                        <el-button @click="query" icon="el-icon-search" type="primary">Поиск</el-button>
                    </el-col>
                </el-row>
            </div>

            <div>
                <p></p>
            </div>

            <el-row justify="center" type="flex">
                <el-pagination
                        :current-page.sync="pageIndex"
                        :page-size="pageSize"
                        :total="pageSize * pageCount"
                        @current-change="getPage"
                        background
                        layout="prev, pager, next"
                >
                </el-pagination>
            </el-row>

            <div>
                <p></p>
            </div>

            <div class="table">
                <el-table :data="tableData" stripe>
                    <el-table-column label="№" prop="homeworkId"/>
                    <el-table-column label="Название" prop="homeworkTitle"/>
                    <el-table-column label="Содержание" prop="homeworkContent"/>
                    <el-table-column label="Представленное задание" prop="title"/>
                    <el-table-column label="Представленное содержание" prop="content"/>
                    <el-table-column align="center" label="Выполнить" width="200px">
                        <template slot-scope="scope">
                            <el-button @click="editStudentHomework(scope.row.homeworkId)"
                                       size="mini"
                                       type="success">
                                Редактировать
                            </el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </div>

            <el-dialog :visible.sync="editing" title="Редактировать" width="50%">
                <el-form :model="entityForm" label-width="82px" ref="form">
                    <el-form-item label="№">
                        <el-input disabled type="number" v-model="entityForm.homeworkId"></el-input>
                    </el-form-item>
                    <el-form-item label="Название">
                        <el-input disabled type="text" v-model="entityForm.homeworkTitle"></el-input>
                    </el-form-item>
                    <el-form-item label="Содержание">
                        <el-input disabled type="textarea" v-model="entityForm.homeworkContent"></el-input>
                    </el-form-item>
                    <el-form-item label="Представленное задание">
                        <el-input type="text" v-model="entityForm.title"></el-input>
                    </el-form-item>
                    <el-form-item label="Представленное содержание">
                        <el-input type="textarea" v-model="entityForm.content"></el-input>
                    </el-form-item>
                </el-form>
                <span class="dialog-footer" slot="footer">
                <el-button @click="save" type="primary">Подтвердить</el-button>
                <el-button @click="editing = false">Отменить</el-button>
            </span>
            </el-dialog>
        </div>
    </div>
</template>

<script>
    import * as submittedApi from "@/api/student/submitted";

    export default {
        name: "StudentSubmitted",
        data() {
            return {
                queryForm: {
                    homeworkId: "",
                    homeworkTitle: ""
                },
                entityForm: {},
                tableData: [],
                pageSize: submittedApi.pageSize,
                pageCount: 1,
                pageIndex: 1,
                editing: false
            };
        },
        methods: {
            query() {
                submittedApi.getPageCount(this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.pageCount = res;
                    this.pageIndex = 1;
                    this.getPage(1);
                });
            },
            getPage(pageIndex) {
                submittedApi.getPage(pageIndex, this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.tableData = res;
                });
            },
            editStudentHomework(homeworkId) {
                submittedApi.getStudentHomework(homeworkId).then(res => {
                    this.entityForm = res;
                    this.editing = true;
                })
            },
            save() {
                submittedApi.updateStudentHomework(this.entityForm).then(() => {
                    this.$message.success("Успех!");
                    this.getPage(this.pageIndex);
                    this.editing = false;
                });
            }
        },
        created() {
            this.query();
        }
    };
</script>

<style scoped>

</style>