<template>
    <div class="teacher-homework-wrap">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-fa fa-book"></i> Список заданий
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>

        <div class="container">
            <div class="query-form">
                <el-row :gutter="20">
                    <el-col :span="2">
                        <el-button @click="create" icon="el-icon-plus">Опубикловать задание</el-button>
                    </el-col>
                    <el-col :offset="13" :span="3">
                        <el-input @keyup.enter.native="query" onkeyup="value=value.replace(/[^\d]/g,'')"
                                  placeholder="ID работы" v-model="queryForm.homeworkId"/>
                    </el-col>
                    <el-col :span="3">
                        <el-input @keyup.enter.native="query" placeholder="Название" v-model="queryForm.homeworkTitle"/>
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
                    <el-table-column label="ID задания" prop="homeworkId"/>
                    <el-table-column label="Название" prop="homeworkTitle"/>
                    <el-table-column label="Содержимое" prop="homeworkContent"/>
                    <el-table-column align="center" label="В процессе" width="200px">
                        <template slot-scope="scope">
                            <el-button @click="editHomework(scope.row.homeworkId)" size="mini" type="success">Редактировать
                            </el-button>
                            <el-button @click="deleteHomework(scope.row.homeworkId)" size="mini" type="danger">Удалить
                            </el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </div>

            <el-dialog :visible.sync="editing" title="Редактировать" width="50%">
                <el-form :model="entityForm" label-width="68px" ref="form">
                    <el-form-item label="ID задания">
                        <el-input disabled v-model="entityForm.homeworkId"></el-input>
                    </el-form-item>
                    <el-form-item label="Название">
                        <el-input v-model="entityForm.homeworkTitle"></el-input>
                    </el-form-item>
                    <el-form-item label="Содержимое">
                        <el-input type="textarea" v-model="entityForm.homeworkContent"></el-input>
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
    import * as homeworkApi from "@/api/teacher/homework";

    export default {
        name: "TeacherHomework",
        data() {
            return {
                queryForm: {
                    homeworkId: "",
                    homeworkTitle: ""
                },
                entityForm: {},
                tableData: [],
                pageSize: homeworkApi.pageSize,
                pageCount: 1,
                pageIndex: 1,
                editing: false
            };
        },
        methods: {
            query() {
                homeworkApi.getPageCount(this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.pageCount = res;
                    this.pageIndex = 1;
                    this.getPage(1);
                });
            },
            getPage(pageIndex) {
                homeworkApi.getPage(pageIndex, this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.tableData = res;
                });
            },
            create() {
                this.entityForm = {
                    homeworkId: "",
                    homeworkTitle: "",
                    homeworkContent: ""
                };
                this.editing = true;
            },
            editHomework(homeworkId) {
                homeworkApi.getHomework(homeworkId).then(res => {
                    this.entityForm = res;
                    this.editing = true;
                });
            },
            save() {
                if (this.entityForm.homeworkId === "") {
                    homeworkApi.addHomework(this.entityForm).then(() => {
                        this.finishSave();
                    });
                } else {
                    homeworkApi.updateHomework(this.entityForm).then(() => {
                        this.finishSave();
                    });
                }
            },
            finishSave() {
                this.$message.success("Успешно!");
                this.getPage(this.pageIndex);
                this.editing = false;
            },
            deleteHomework(homeworkId) {
                homeworkApi.deleteHomework(homeworkId).then(() => {
                    this.$message.success("Успешно удалено!");
                    this.getPage(this.pageIndex);
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