<template>
  <div>
    <p class="title">寄养申请</p>
    <el-form
      label-position="right"
      style="margin-left:100px;"
      :model="form"
      :rules="rules"
      ref="form"
    >
      <el-form-item label="宠物信息：" label-width="100px" prop="name">
        <el-button size="small" @click="goadd" v-if="pet.pet">添加宠物</el-button>
        <el-button size="small" @click="goadd" v-else>编辑宠物</el-button>
        <el-popover placement="right" width="400" trigger="click">
          <el-form label-position="right">
            <el-form-item label="名字：" label-width="100px" prop="name">
              <el-input
                type="text"
                size="small"
                class="formlist"
                v-model="pet.name"
                :disabled="true"
              ></el-input>
            </el-form-item>
            <el-form-item label="生日：" label-width="100px" prop="birthday">
              <el-date-picker
                v-model="pet.birthday"
                type="date"
                class="formlist"
                size="small"
                :disabled="true"
              ></el-date-picker>
            </el-form-item>
            <el-form-item label="价格：" label-width="100px" prop="price">
              <el-input
                type="text"
                size="small"
                class="formlist"
                v-model.number="pet.price"
                oninput="if(value.length>10)value=value.slice(0,10)"
                :disabled="true"
              ></el-input>
            </el-form-item>
            <el-form-item label="种类：" label-width="100px" prop="type">
              <el-input
                type="text"
                size="small"
                class="formlist"
                v-model="pet.type"
                :disabled="true"
              ></el-input>
            </el-form-item>
            <el-form-item label="品种：" label-width="100px" prop="variety">
              <el-input
                type="text"
                size="small"
                class="formlist"
                v-model="pet.variety"
                :disabled="true"
              ></el-input>
            </el-form-item>
            <el-form-item label="性别：" label-width="100px" prop="sex">
              <el-input
                type="text"
                size="small"
                class="formlist"
                v-model="pet.sex"
                :disabled="true"
              ></el-input>
            </el-form-item>
            <el-form-item label="备注：" label-width="100px" prop="note">
              <el-input
                type="textarea"
                :rows="3"
                class="formlist"
                v-model="pet.note"
                :disabled="true"
              ></el-input>
            </el-form-item>
          </el-form>
          <i class="header-icon el-icon-view icon" slot="reference"></i>
        </el-popover>
      </el-form-item>
      <el-form-item label="开始时间：" label-width="100px" prop="starttime">
        <el-date-picker v-model="form.starttime" type="date" class="formlist" size="small"></el-date-picker>
      </el-form-item>
      <el-form-item label="寄养时长：" label-width="100px" prop="timerang">
        <el-input
          type="text"
          size="small"
          class="formlist"
          v-model.number="form.timerang"
          oninput="if(value.length>10)value=value.slice(0,10)"
        ></el-input>
        <span style="margin-left:10px;">天</span>
      </el-form-item>
    </el-form>
    <div class="button">
      <el-button type="primary" size="small" @click="gosave">提交</el-button>
      <el-button size="small" @click="goclose">取消</el-button>
    </div>
    <Dialog></Dialog>
  </div>
</template>

<script>
import Dialog from "@common/dialog.vue";
import add_update from "../manage/dialog/petadd_update.vue";
import { checkinput } from "@assets/validate.js";
export default {
  components: {
    Dialog
  },
  name: "caretable",
  data() {
    return {
      form: {
        starttime: "",
        timerang: ""
      },
      pet: { pet: true },
      rules: {
        starttime: [
          {
            validator: checkinput,
            trigger: "blur",
            message: "开始日期不能为空"
          }
        ],
        timerang: [
          {
            validator: checkinput,
            trigger: "blur",
            message: "寄养时长不能为空"
          },
          { type: "number", message: "寄养时长必须为数字值" }
        ]
      }
    };
  },
  methods: {
    gosave() {
      this.$refs["form"].validate(valid => {
        if (valid) {
          this.form.starttime = this.moment(this.form.starttime).format(
            "YYYY-MM-DD"
          );
          this.form.username = this.$store.state.username;
          this.axios
            .post("/api/addpet", this.pet)
            .then(res => {
              if (res.data.success) {
                this.form.petid = res.data.petid;
                return this.axios.post("/api/addcaretable", this.form);
              }
            })
            .then(res => {
              if (res.data.success) {
                this.$message.success("成功提交寄养申请！");
                this.closeDialog();
              }
            });
        } else {
          return false;
        }
      });
    },
    goadd() {
      var pet = this.pet;
      this.Dialog.title("我要寄养")
        .width("800px")
        .currentView(add_update, { pet })
        .then(data => {
          if (data) {
            this.pet = data;
          }
        })
        .show();
    },
    goclose() {
      this.closeDialog();
    }
  }
};
</script>

<style scoped>
.seepicture {
  width: 150px;
  height: 150px;
}
.title {
  text-align: center;
  font-size: 25px;
  margin-top: 0px;
}
.formlist {
  width: 250px;
}
.button {
  text-align: center;
}
.icon {
  font-size: 20px;
  margin-left: 10px;
  cursor: pointer;
}
</style>
