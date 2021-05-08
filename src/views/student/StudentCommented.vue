<template>
    <div class="student-commented-wrap">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-fa fa-thumbs-up"></i> Комментарий учителя
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
                    <el-table-column label="ФИО учителя" prop="teacherName"/>
                    <el-table-column label="Комментарий учителя" prop="teacherComment"/>
                </el-table>
            </div>
        </div>
    </div>
</template>

<script>
    import * as commentedApi from "@/api/student/commented";

    export default {
        name: "StudentCommented",
        data() {
            return {
                queryForm: {
                    homeworkId: "",
                    homeworkTitle: ""
                },
                tableData: [],
                pageSize: commentedApi.pageSize,
                pageCount: 1,
                pageIndex: 1,
            };
        },
        methods: {
            query() {
                commentedApi.getPageCount(this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.pageCount = res;
                    this.pageIndex = 1;
                    this.getPage(1);
                });
            },
            getPage(pageIndex) {
                commentedApi.getPage(pageIndex, this.queryForm.homeworkId, this.queryForm.homeworkTitle).then(res => {
                    this.tableData = res;
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