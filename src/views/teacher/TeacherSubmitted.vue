<template>
    <div class="teacher-submitted-wrap">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-fa fa-check"></i> Список заданий
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>

        <div class="container">
            <div class="query-form">
                <el-row :gutter="20">
                    <el-col :offset="9" :span="3">
                        <el-input @keyup.enter.native="query" onkeyup="value=value.replace(/[^\d]/g,'')"
                                  placeholder="ID задания" v-model="queryForm.homeworkId"/>
                    </el-col>
                    <el-col :span="3">
                        <el-input @keyup.enter.native="query" placeholder="Название" v-model="queryForm.homeworkTitle"/>
                    </el-col>
                    <el-col :span="3">
                        <el-input @keyup.enter.native="query" onkeyup="value=value.replace(/[^\d]/g,'')"
                                  placeholder="ID студента" v-model="queryForm.studentId"/>
                    </el-col>
                    <el-col :span="3">
                        <el-input @keyup.enter.native="query" placeholder="Имя студента" v-model="queryForm.studentName"/>
                    </el-col>
                    <el-col :span="3">
                        <el-button @click="query" icon="el-icon-search" type="primary">Найти</el-button>
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
                    <el-table-column label="ID работы учащегося" prop="studentHomeworkId"/>
                    <el-table-column label="ID задания" prop="homeworkId"/>
                    <el-table-column label="Название работы" prop="homeworkTitle"/>
                    <el-table-column label="Содержимое работы" prop="homeworkContent"/>
                    <el-table-column label="ID студента" prop="studentId"/>
                    <el-table-column label="Имя студента" prop="studentName"/>
                    <el-table-column label="Название задания" prop="title"/>
                    <el-table-column label="Содержимое задания" prop="content"/>
                    <el-table-column label="Комментарий преподавателя" prop="teacherComment"/>
                    <el-table-column align="center" label="В процессе" width="200px">
                        <template slot-scope="scope">
                            <el-button @click="editSubmittedStudentHomework(scope.row.studentHomeworkId)" size="mini"
                                       type="success">Оценка
                            </el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </div>

            <el-dialog :visible.sync="editing" title="Редактировать" width="50%">
                <el-form :model="entityForm" label-width="96px" ref="form">
                    <el-form-item label="ID работы учащегося">
                        <el-input disabled type="number" v-model="entityForm.studentHomeworkId"></el-input>
                    </el-form-item>
                    <el-form-item label="ID задания">
                        <el-input disabled type="number" v-model="entityForm.homeworkId"></el-input>
                    </el-form-item>
                    <el-form-item label="Название работы">
                        <el-input disabled type="text" v-model="entityForm.homeworkTitle"></el-input>
                    </el-form-item>
                    <el-form-item label="Содержимое работы">
                        <el-input disabled type="textarea" v-model="entityForm.homeworkContent"></el-input>
                    </el-form-item>
                    <el-form-item label="ID студента">
                        <el-input disabled type="number" v-model="entityForm.studentId"></el-input>
                    </el-form-item>
                    <el-form-item label="Имя студента">
                        <el-input disabled type="text" v-model="entityForm.studentName"></el-input>
                    </el-form-item>
                    <el-form-item label="Название задания">
                        <el-input disabled type="text" v-model="entityForm.title"></el-input>
                    </el-form-item>
                    <el-form-item label="Содержимое задания">
                        <el-input disabled type="textarea" v-model="entityForm.content"></el-input>
                    </el-form-item>
                    <el-form-item label="Комментарий преподавателя">
                        <el-input type="textarea" v-model="entityForm.teacherComment"></el-input>
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
    import * as submittedApi from "@/api/teacher/submitted";

    export default {
        name: "TeacherSubmitted",
        data() {
            return {
                queryForm: {
                    homeworkId: "",
                    homeworkTitle: "",
                    studentId: "",
                    studentName: ""
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
                submittedApi.getPageCount(this.queryForm.homeworkId, this.queryForm.homeworkTitle, this.queryForm.studentId, this.queryForm.studentName).then(res => {
                    this.pageCount = res;
                    this.pageIndex = 1;
                    this.getPage(1);
                });
            },
            getPage(pageIndex) {
                submittedApi.getPage(pageIndex, this.queryForm.homeworkId, this.queryForm.homeworkTitle, this.queryForm.studentId, this.queryForm.studentName).then(res => {
                    this.tableData = res;
                });
            },
            editSubmittedStudentHomework(studentHomeworkId) {
                submittedApi.getStudentHomework(studentHomeworkId).then(res => {
                    this.entityForm = res;
                    this.editing = true;
                });
            },
            save() {
                submittedApi.updateStudentHomework(this.entityForm).then(() => {
                    this.$message.success("点评成功");
                    this.getPage(this.pageIndex);
                    this.editing = false;
                });
            }
        },
        created() {
            this.query();
        }
    }
</script>

<style scoped>

</style>