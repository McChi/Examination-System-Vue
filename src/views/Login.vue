<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="../assets/title.png"
          width="250"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/SanguineWang/Examination-System-Vue"
        target="_blank"
        text
      >
        <span class="mr-2">our Github</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="5">
            <v-card>
              <v-card-title>
                <span class="headline">输入您的账户</span>
                <v-spacer></v-spacer>
              </v-card-title>
              <!-- <v-divider></v-divider> -->
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12">
                      <v-text-field
                        label="*账号"
                        required
                        :rules="notNullRules"
                        v-model="user.number"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12">
                      <v-text-field
                        label="*密码"
                        type="password"
                        :rules="notNullRules"
                        required
                        v-model="user.password"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
                <small>*请确认您使用自己的帐户</small>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="black darken-1" text @click="login">登入</v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from "@/axios/myAxios.js";
import * as types from "@/store/type.js";
import { updateRouters } from "../router";
export default {
  name: "Login",
  data() {
    return {
      notNullRules: [
        v => /^[0-9]*$/.test(v) || "必须是数字",
        v => v != "" || "不能为空"
      ],
      user: {
        number: "",
        password: ""
      }
    };
  },
  components: {},
  created() {},
  mounted() {},
  methods: {
    login() {
      console.log("click login");
      // console.log(this.user);

      this.userLogin(this.user).then(() => {
        updateRouters();
        let role = sessionStorage.getItem(types.role);
        console.log(role);
        if (role == types.teacherRole) {
          this.$router.push("/teacher/exam");
        }
        if (role == types.studentRole) {
          this.$router.push("/student");
        }
        if (role == types.adminRole) {
          this.$router.push("/home");
        }
      });
    },
    //登录
    async userLogin(user) {
      let resp = await axios.post("login", user);
      if (resp != null) {
        //  置于本地session仓库
        let token = resp.headers["authorization"];
        let role = resp.data.data.role;
        sessionStorage.setItem(types.author, token);
        sessionStorage.setItem(types.role, role);
        // console.log(token);
        // console.log(role);
      } else {
        console.log("响应为空");
      }
    },
    // 登录使用token测试
    async userLoginTest() {
      let resp = await axios.get("loginTest");
      if (resp != null) {
        console.log(resp.data.data.uid);
      } else {
        console.log("响应为空");
      }
    }
  }
};
</script>
<style scoped></style>
