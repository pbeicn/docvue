<template>
  <div id="main">
    <DoctorHeader :department="department" :name="name"></DoctorHeader>
    <div class="main_content">
      <div class="left">
        <div class="header">
          <div class="title">问诊人员列表</div>
          <div class="count">
            当前挂号人数
            <span style="color: #FF5B5B;font-size: 18px;" v-html="count"></span>人
          </div>
        </div>
        <div class="divider"></div>
        <div class="patients">
          <div
            v-for="(patient, index) in patients"
            :key="index"
            @click="onScanPatientCardQRCode"
            :class="index===0?'patient':'patient1'"
          >
            <div class="name">{{ patient.pname }}</div>
            <div class="sex-and-age">{{ patient.sex }} {{ patient.age }}</div>
            <div class="date">挂号时间：{{ patient.startTime }}</div>
          </div>
        </div>
        <div class="botom">
          <div class="wenzi">历史就诊人员</div>
        </div>
      </div>
      <div class="right">
        <div class="top">
          <div class="header">
            <div class="title">患者信息</div>
            <div class="info" v-if="currentPatient">
              {{ currentPatient.name }}({{ currentPatient.idcard }})
              {{ currentPatient.sex }}
              {{ currentPatient.age }}
            </div>
          </div>
          <div class="divider"></div>
          <div class="diagnose">
            <div class="diagnose-info">
              <div class="title">诊断信息:</div>
              <a-textarea placeholder="请输入诊断信息" :rows="6" v-model="diagnoseInfo" />
            </div>
            <div class="diagnose-result">
              <div class="title">诊断结果:</div>
              <a-textarea placeholder="请输入诊断结果" :rows="6" v-model="diagnoseResult" />
            </div>
          </div>
          <div class="btns">
            <button class="button1" style="margin-right: 15px;" @click="saveDiagnoseInfo">保存诊断</button>
            <button
              class="button2"
              style="border:1px solid #0090FF;color:#0090FF"
              @click="finishDiagnoseInfo"
            >结束诊断</button>
            <button class="button3" style="margin-left: 15px;" @click="toSaoMa">开新诊断</button>
          </div>
        </div>
        <div class="bottom">
          <div class="btab">
            <a-tabs defaultActiveKey="1" @change="callback">
              <a-tab-pane tab="开处方" key="1">
                <RecipeTable ref="recipe" @onCompleted="postRecipe"></RecipeTable>
              </a-tab-pane>
              <a-tab-pane tab="开检查" key="2" forceRender>
                <CheckTable ref="check"></CheckTable>
              </a-tab-pane>
            </a-tabs>
          </div>
          <div class="bb1">
            <a-button type="primary" @click="showDrawer">+新建</a-button>
            <a-button @click="postCheck1">保存</a-button>
            <a-button @click="delrecipe">删除</a-button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import DoctorHeader from "@/components/header/doc-header";
import RecipeTable from "@/components/table/recipe-table-2";
import CheckTable from "@/components/table/check-table";
export default {
  components: { DoctorHeader, RecipeTable, CheckTable },
  data() {
    return {
      form: this.$form.createForm(this),
      name: "",
      department: "",
      count: "",
      diagnoseInfo: "",
      diagnoseResult: "",
      patients: [],
      currentPatient: {},
      visible: false,
      iswhich: "1",
      addText: "新增处方"
    };
  },
  created() {
    this.getDoctorAndPatients();
    setInterval(() => {
      this.getDoctorAndPatients();
    }, 10000);
  },
  computed: {
    // whichcolor: function() {
    //   alert(this.patients.name);
    //   if (this.patients.length == 1) {
    //     return "1";
    //   }
    //   return "patient";
    // }
  },
  watch: {
    // patients: {
    //   handler(newValue, oldValue) {
    //     for (let i = 0; i < newValue.length; i++) {
    //       if (oldValue[i] != newValue[i]) {
    //         window.console.log(newValue);
    //       }
    //     }
    //   },
    //   deep: true
    // }
  },
  methods: {
    postRecipe(val) {
      alert("postRecipe");
      window.console.log(val);
      // const config = {
      //   url: `api/v1/diagnose/${this.diagnoseId}/recipe`,
      //   headers: {
      //     "X-CARD-CODE": this.patientCardQRCode
      //   },
      //   data: {
      //     medicine: val
      //   }
      // };
      // postWithAuth(config)
      //   .then(res => {
      //     debug.log("医生提交处方信息成功", res);
      //     this.$message.success("提交处方信息成功");
      //   })
      //   .catch(err => {
      //     debug.error("医生提交处方信息失败", err);
      //     this.$message.error("提交处方信息失败");
      //   });
    },
    delrecipe() {
      if (this.iswhich === "1") {
        this.$refs.recipe.onDeleteTableData();
      }
    },
    postCheck1() {
      if (this.iswhich === "1") {
        window.console.log(this.$refs.recipe.tableData);
      } else {
        window.console.log(this.$refs.check.tableData);
      }
      // const config = {
      //   url: `api/v1/diagnose/${this.diagnoseId}/check`,
      //   headers: {
      //     "X-CARD-CODE": this.patientCardQRCode
      //   },
      //   data: val
      // };
      // postWithAuth(config)
      //   .then(res => {
      //     debug.log("医生提交检查信息成功", res);
      //     this.$message.success("提交检查信息成功");
      //   })
      //   .catch(err => {
      //     debug.error("医生提交检查信息失败", err);
      //     this.$message.error("提交检查信息失败");
      //   });
    },
    callback(key) {
      window.console.log(key);
      this.iswhich = key;
      // if (key === "1") {
      //   this.addText = "新增处方";
      // } else {
      //   this.addText = "新增检查";
      // }
    },
    getDoctorAndPatients() {
      this.$ajax
        .get("api/v1/doctor1")
        .then(res => {
          window.console.log(res);
          this.department = res.data.department;
          this.name = res.data.doctorName;
          this.count = res.data.count;
          this.patients = res.data.registerPatientDtoList;
        })
        .catch(res => {
          window.console.log(res);
        });
    },
    showDrawer() {
      // this.visible = true;
      if (this.iswhich === "1") {
        this.$refs.recipe.onDrawerVisible();
      } else {
        this.$refs.check.onDrawerVisible();
      }
    },
    onClose() {
      this.visible = false;
    },
    onScanPatientCardQRCode() {
      window.console.log("1");
    },
    toSaoMa() {
      this.$router.push("/saoma");
    },
    saveDiagnoseInfo() {
      this.$ajax
        .post(
          "/api/v1/diagnose/" +
            sessionStorage.getItem("pid") +
            "/info?information=" +
            this.diagnoseInfo
        )
        .then(res => {
          window.console.log(res);
        })
        .catch(res => {
          window.console.log(res);
        });
    },
    finishDiagnoseInfo() {
      alert(1);
      this.$ajax
        .post("/api/v1/diagnose/" + sessionStorage.getItem("pid"), null, {
          headers: {
            "X-CARD-CODE": sessionStorage.getItem("xcode")
          }
        })
        .then(res => {
          window.console.log(res);
        })
        .catch(res => {
          window.console.log(res);
        });
    }
  },
  destroyed() {
    clearInterval();
  }
};
</script>
<style lang="less" scoped>
.bg {
  background-color: aqua;
}
.bg2 {
  background-color: rgb(214, 90, 177);
}
#main {
  width: 100%;
  min-height: 100%;
  background: rgba(240, 242, 245, 1);
  background: #f0f2f5;
  flex-direction: column;
}
.main_p1 {
  width: 10px;
}
.a1 {
  width: 100px;
}
.main_content {
  margin: 18px;
  display: flex;
  flex-direction: row;
  .left {
    width: 342px;
    margin-bottom: 18px;
    height: 667px;
    background: rgba(255, 255, 255, 1);
    box-shadow: 0px 1px 7px 0px rgba(153, 153, 153, 0.2);
    border-radius: 3px;
    display: flex;
    position: relative;
    flex-direction: column;
    .header {
      height: 41px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 14px;

      .title {
        font-size: 14px;
        font-weight: 600;
        color: rgba(51, 51, 51, 1);
        line-height: 20px;
      }
      .count {
        font-size: 11px;
        font-weight: 600;
        color: rgba(51, 51, 51, 1);
        line-height: 16px;
      }
    }
    .divider {
      height: 1px;
      background: rgba(216, 216, 216, 1);
    }
    .patients {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      .patient {
        margin: 20px;
        width: 263px;
        height: 103px;
        background: rgba(135, 182, 242, 1);
        box-shadow: 0px 1px 6px 0px rgba(0, 0, 0, 0.2);
        border-radius: 1px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding: 14px 28px;
        text-align: left;

        .name,
        .sex-and-age,
        .date {
          font-size: 14px;
          font-weight: 400;
          color: rgba(255, 255, 255, 1);
          line-height: 20px;
        }
        .name1,
        .sex-and-age1,
        .date1 {
          font-size: 14px;
          font-weight: 400;
          color: black;
          line-height: 20px;
        }
      }
      .patient1 {
        margin: 20px;
        width: 263px;
        height: 103px;
        background: white;
        box-shadow: 0px 1px 6px 0px rgba(0, 0, 0, 0.2);
        border-radius: 1px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding: 14px 28px;
        text-align: left;

        .name,
        .sex-and-age,
        .date {
          font-size: 14px;
          font-weight: 400;
          color: black;
          line-height: 20px;
        }
      }
    }
    .botom {
      position: absolute;
      left: 21px;
      right: 21px;
      bottom: 10px;
      width: 80%;
      height: 43px;
      background: rgba(0, 144, 255, 1);
      border-radius: 4px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      .wenzi {
        height: 25px;
        font-size: 14px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: rgba(255, 255, 255, 1);
        line-height: 25px;
      }
    }
  }
  .right {
    margin-left: 20px;
    flex-grow: 1;
    .top {
      height: 273px;
      background: rgba(255, 255, 255, 1);
      margin-bottom: 20px;
      .header {
        height: 41px;
        display: flex;
        align-items: center;
        padding: 0 14px;
        .title {
          font-size: 14px;
          font-weight: 600;
          color: rgba(51, 51, 51, 1);
          line-height: 20px;
        }
        .info {
          font-size: 11px;
          font-weight: 600;
          color: rgba(51, 51, 51, 1);
          line-height: 16px;
          margin-left: 20px;
        }
      }
      .divider {
        height: 1px;
        background: rgba(216, 216, 216, 1);
        margin-left: 5px;
        margin-right: 5px;
      }
      .diagnose {
        padding-top: 34px;
        /*padding-left: 25px;*/
        /*padding-right: 25px;*/
        display: flex;
        justify-content: space-between;
        .diagnose-info,
        .diagnose-result {
          width: 50%;
          height: 100%;
          margin: 0 50px 0 25px;
          display: flex;

          .title {
            flex-shrink: 0;
            margin-right: 25px;
            height: 22px;
            font-size: 16px;
            font-family: PingFangSC-Regular, PingFang SC;
            font-weight: 600;
            color: rgba(0, 0, 0, 0.85);
            line-height: 22px;
          }
        }
      }
      .btns {
        margin-top: 20px;
        margin-right: 50px;
        display: flex;
        justify-content: flex-end;

        .button1 {
          border: none;
          background: rgba(24, 144, 255, 1);
          border-radius: 3px;
          font-size: 10px;
          font-weight: 400;
          color: rgba(255, 255, 255, 1);
          line-height: 16px;
          padding: 5px 10px;
        }
        .button2 {
          border: none;
          border-radius: 3px;
          font-size: 10px;
          font-weight: 400;
          background: rgba(255, 255, 255, 1);
          color: rgba(255, 255, 255, 1);
          line-height: 16px;
          padding: 5px 10px;
        }
        .button3 {
          border: none;
          border-radius: 3px;
          font-size: 10px;
          font-weight: 400;
          background: rgba(255, 255, 255, 1);
          line-height: 16px;
          padding: 5px 10px;
          border: 1px solid #29f365;
          color: #f1a312;
        }
      }
    }
    .bottom {
      min-height: 374px;
      background: rgba(255, 255, 255, 1);
      margin-bottom: 18px;
      padding: 0 14px;
      position: relative;
      .btab {
        text-align: left;
      }
    }
    .bb1 {
      position: absolute;
      right: 30px;
      top: 5px;
      width: 230px;
      display: flex;
      justify-content: space-around;
    }
  }
}
</style>
