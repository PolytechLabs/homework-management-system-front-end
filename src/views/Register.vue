<template>
    <div class="register-wrap">
        <div class="register-form">
            <div class="form-title">Регистрация</div>
            <el-form ref="form" :model="formData" :rules="rules" class="form-content" label-width="0px">
                <el-form-item prop="userId">
                    <el-input onkeyup="value=value.replace(/[^\d]/g,'')" placeholder="Id пользователя" v-model="formData.userId">
                        <span slot="prepend"><i class="el-icon-user"></i></span>
                    </el-input>
                </el-form-item>

                <el-form-item prop="username">
                    <el-input placeholder="Имя" v-model="formData.username">
                        <span slot="prepend"><i class="el-icon-user"></i></span>
                    </el-input>
                </el-form-item>

                <el-form-item prop="password">
                    <el-input placeholder="Пароль" type="password" v-model="formData.password">
                        <span slot="prepend"><i class="el-icon-lock"></i></span>
                    </el-input>
                </el-form-item>

                <el-form-item prop="repeatPassword">
                    <el-input placeholder="Подтвердите пароль" type="password" v-model="formData.repeatPassword"
                              @keyup.enter.native="register()">
                        <span slot="prepend"><i class="el-icon-lock"></i></span>
                    </el-input>
                </el-form-item>

                <el-form-item prop="userType">
                    <el-radio-group v-model="formData.userType">
                        <el-radio-button label="1">Ученик</el-radio-button>
                        <el-radio-button label="2">Преподаватель</el-radio-button>
                    </el-radio-group>
                </el-form-item>

                <div class="register-button" v-loading="this.$store.state.loading">
                    <el-button @click="register()" type="success">Зарегестрироваться</el-button>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script>
    import {register} from "@/api/user";
    import md5 from 'js-md5';

    export default {
        name: "Register",
        data() {
            return {
                formData: {
                    userId: "",
                    username: "",
                    password: "",
                    repeatPassword: "",
                    userType: "1"
                },
                rules: {
                    userId: [{required: true, message: "Ввойдите в аккаунт", trigger: "blur"}],
                    username: [{required: true, message: "Введите ваше имя", trigger: "blur"}],
                    password: [{required: true, message: "Введите пароль", trigger: "blur"}],
                    repeatPassword: [{required: true, message: "Подтвердите пароль", trigger: "blur"}],
                    userType: [{required: true, message: "Выберите тип пользователя", trigger: "blur"}]
                }
            };
        },
        methods: {
            register() {
                this.$refs.form.validate(valid => {
                    if (valid) {
                        if (this.formData.repeatPassword !== this.formData.password) {
                            this.$message.error("Пароли не совпадают!");
                        } else {
                            register(this.formData.userId, this.formData.username, md5(this.formData.password + md5(this.formData.userId)), this.formData.userType).then(() => {
                                this.$message.success("Пользователь успешно зарегистрирован");
                                this.$router.go({name: "login"});
                            });
                        }
                    }
                });
            }
        }
    }
</script>

<style scoped>
    .register-wrap {
        position: fixed;
        width: 100%;
        height: 100%;
        background-image: url("../assets/login-background.jpg");
        background-size: 100% 100%;
    }

    .form-title {
        width: 100%;
        line-height: 50px;
        text-align: center;
        font-size: 20px;
        color: #ffffff;
        border-bottom: 1px solid #dddddd;
    }

    .register-form {
        position: absolute;
        left: 50%;
        top: 50%;
        width: 350px;
        margin: -190px 0 0 -175px;
        border-radius: 5px;
        background: rgba(0, 0, 0, 0.5);
        overflow: hidden;
    }

    .form-content {
        padding: 30px 30px;
        text-align: center;
    }

    .register-button {
        text-align: center;
    }

    .register-button button {
        width: 100%;
        height: 38px;
    }
</style>