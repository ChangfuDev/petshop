<template>
  <div>
    <div class="div1">
      <div :class="get1fontclass">
        <div :class="get1boderclass">
          <span class="number">1</span>
        </div>
        <span style="margin-left:10px;">验证身份</span>
      </div>
      <div :class="get2fontclass">
        <div :class="get2boderclass">
          <span class="number">2</span>
        </div>
        <span style="margin-left:10px;">设置密码</span>
      </div>
      <div :class="get3fontclass">
        <div :class="get3boderclass">
          <span class="number">3</span>
        </div>
        <span style="margin-left:10px;">改密成功</span>
      </div>
    </div>
    <el-form
      label-position="right"
      class="formclass1"
      v-show="nextpage=='1'"
      :model="form"
      :rules="rules"
      ref="form"
    >
      <el-form-item
        label="原密码："
        label-width="100px"
        class="formclass"
        prop="oldpsd"
        :inline-message="true"
      >
        <el-input show-password size="small" class="formwidth" v-model="form.oldpsd"></el-input>
      </el-form-item>
    </el-form>
    <el-form
      :model="ruleForm"
      :rules="rules2"
      ref="ruleForm"
      v-show="nextpage=='2'"
      label-width="100px"
      class="formclass1"
    >
      <el-form-item
        label="密码："
        label-width="100px"
        class="formclass"
        prop="pass"
        :inline-message="true"
      >
        <el-input
          type="password"
          size="small"
          v-model="ruleForm.pass"
          autocomplete="off"
          class="formwidth"
        ></el-input>
        <el-tooltip class="item" effect="dark" content="密码长度在6-20个字符间" placement="top">
          <i class="el-icon-question icon"></i>
        </el-tooltip>
      </el-form-item>
      <el-form-item
        prop="checkPass"
        label="确认密码："
        label-width="100px"
        class="formclass"
        :inline-message="true"
      >
        <el-input
          size="small"
          type="password"
          v-model="ruleForm.checkPass"
          autocomplete="off"
          class="formwidth"
        ></el-input>
      </el-form-item>
    </el-form>
    <p class="div1" style="margin-top: 30px;margin-bottom: 20px;" v-if="nextpage=='3'">您的密码已经修改成功！</p>
    <div class="div1" style="margin-top: 10px;">
      <el-button type="primary" size="small" @click="goup" v-show="nextpage==2">上一步</el-button>
      <el-button type="primary" size="small" @click="gonext" v-show="nextpage!=3">下一步</el-button>
      <el-button type="primary" size="small" @click="goclose" v-show="nextpage==3">关闭</el-button>
    </div>
  </div>
</template>

<script>
export default {
  name: "psd",
  data() {
    var validatePass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (value === this.old.psd) {
        callback(new Error("该密码与原密码相同！请重新输入！"));
      } else {
        if (this.ruleForm.checkPass !== "") {
          this.$refs.ruleForm.validateField("checkPass");
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleForm.pass) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    var checkold = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("原密码不能为空"));
      }
      if (value != this.old.psd) {
        return callback(new Error("原密码错误，请重新输入"));
      }
      callback();
    };
    return {
      ruleForm: {
        pass: "",
        checkPass: ""
      },
      rules2: {
        pass: [
          { min: 6, max: 20, message: "长度在6到20个字符", trigger: "blur" },
          { validator: validatePass, trigger: "blur" }
        ],
        checkPass: [
          { min: 6, max: 20, message: "长度在6到20个字符", trigger: "blur" },
          { validator: validatePass2, trigger: "blur" }
        ]
      },
      nextpage: "1",
      old: {},
      form: {
        oldpsd: ""
      },
      rules: {
        oldpsd: [{ validator: checkold, trigger: "blur" }]
      }
    };
  },
  mounted() {
    if (this.DialogParams().old) {
      this.old = this.DialogParams().old;
    }
  },
  methods: {
    goup() {
      this.nextpage--;
    },
    gonext() {
      if (this.nextpage == "1") {
        this.$refs["form"].validate(valid => {
          if (valid) {
            this.nextpage++;
          } else {
            return false;
          }
        });
      } else {
        this.$refs["ruleForm"].validate(valid => {
          if (valid) {
            this.$confirm("您确认要修改密码?", "提示", {
              confirmButtonText: "确定",
              cancelButtonText: "取消"
            })
              .then(() => {
                this.axios
                  .post("/api/updateuser/psd", {
                    psd: this.ruleForm.pass,
                    username: this.old.username
                  })
                  .then(res => {
                    if (res.data.success) {
                      this.nextpage++;
                    }
                  });
              })
              .catch(() => {});
          } else {
            return false;
          }
        });
      }
    },
    goclose() {
      this.closeDialog();
    }
  },
  computed: {
    get1boderclass: function() {
      if (this.nextpage == "1") {
        return "boder-pink";
      } else {
        return "boder-grlay";
      }
    },
    get2boderclass: function() {
      if (this.nextpage == "2") {
        return "boder-pink";
      } else {
        return "boder-grlay";
      }
    },
    get3boderclass: function() {
      if (this.nextpage == "3") {
        return "boder-pink";
      } else {
        return "boder-grlay";
      }
    },
    get1fontclass: function() {
      if (this.nextpage == "1") {
        return "font-pink";
      } else {
        return "font-grlay";
      }
    },
    get2fontclass: function() {
      if (this.nextpage == "2") {
        return "font-pink";
      } else {
        return "font-grlay";
      }
    },
    get3fontclass: function() {
      if (this.nextpage == "3") {
        return "font-pink";
      } else {
        return "font-grlay";
      }
    }
  }
};
</script>

<style scoped>
.formwidth {
  width: 250px;
}

.formclass {
  padding-top: 5px;
  padding-bottom: 5px;
  margin-bottom: 0;
}

.formclass1 {
  width: 500px;
  display: block;
  margin-left: auto;
  padding-left: 120px;
  margin-right: auto;
  margin-top: 20px;
}
.number {
  font-size: 20px;
  display: block;
  text-align: center;
  margin-top: 2px;
}

.boder-pink {
  width: 30px;
  height: 30px;
  border: 2px solid #f25d8e;
  display: inline-block;
  vertical-align: middle;
  border-radius: 17px;
}
.font-pink {
  color: #f25d8e;
  display: inline-block;
  vertical-align: bottom;
  margin-left: 40px;
}
.boder-grlay {
  width: 30px;
  height: 30px;
  border: 2px solid #ccd0d7;
  display: inline-block;
  vertical-align: middle;
  border-radius: 17px;
}

.font-grlay {
  color: #ccd0d7;
  display: inline-block;
  vertical-align: bottom;
  margin-left: 40px;
}

.div1 {
  text-align: center;
}
.icon {
  margin-left: 10px;
}
</style>
