<template>
  <div id="main">
    <DoctorHeader :department="department" :name="name"></DoctorHeader>
    <div class="main_content">
      <div class="patient">
        <div class="pname">王小二</div>
        <div class="pname">男</div>
        <div class="pname">34岁</div>
      </div>
      <div class="saoma">
        <div>
          <a-input class="ainput" v-model="xcode" placeholder="请先扫获取患者授权，查询患者最近三次历史就诊记录" />
        </div>
        <div class="pname">
          <a-button type="primary" @click="getRecentDiagnoseMock">扫码(Mock)</a-button>
        </div>
        <div class="pname">
          <a-button type="primary" @click="getRecentDiagnose">读卡</a-button>
        </div>
        <div class="pname">
          <a-button type="primary">录入诊断</a-button>
        </div>
      </div>
      <div class="recent">
        <div class="incontent">
          <a-card title :bordered="true" v-for="(recent, index) in recents" :key="index" :class="index === 0 ? 'css1' : 'css2'">
            <div>
              <div>
                {{ recent.docname }}
                <span style="width:20px"></span>
                {{ recent.docdept }}
              </div>
            </div>
            <div>
              <div>{{ recent.starttime }}</div>
            </div>
            <div>
              <div class="text">
                {{ recent.result }}
                <span style="width:20px"></span>
                {{ recent.totalmoney }}
              </div>
            </div>
          </a-card>
        </div>
      </div>
      <div class="maininfo">
        <div class="recipe">
          <div style="text-align:left">
            <a-tabs defaultActiveKey="1">
              <a-tab-pane tab="处方" key="1">
                <a-table :columns="columns" :dataSource="recipedata" :rowKey="recipedata.key" :pagination="pagination" :loading="loading">
                  <template slot="money" slot-scope="text">￥{{ text }}</template>
                  <template slot="allmoney" slot-scope="text">￥{{ text }}</template>
                </a-table>
                <div class="recipeamount">
                  合计：
                  <span style="color: red;">￥{{ recipeamount }}</span>
                </div>
              </a-tab-pane>
            </a-tabs>
          </div>
        </div>
        <div style="height:30px"></div>
        <div class="recipe">
          <div style="text-align:left">
            <a-tabs defaultActiveKey="1">
              <a-tab-pane tab="检查" key="1">
                <div class="checkcss">
                  <a-card title :bordered="true" class="css1" v-for="(checks, index) in checkdtos" :key="index">
                    <div>
                      <img src="@/assets/c1.png" />
                      <div class="text">项目名称：{{ checks.description }}(费用:￥{{ checks.money }})</div>
                    </div>
                    <div>
                      <img src="@/assets/c2.png" />
                      <div class="text">检查科室：{{ checks.department }}</div>
                    </div>
                    <div>
                      <img src="@/assets/c3.png" />
                      <div class="text">检查时间：{{ checks.patientage }}</div>
                    </div>
                  </a-card>
                </div>
                <div class="recipeamount">
                  合计：
                  <span style="color: red;">￥{{ checkamount }}</span>
                </div>
              </a-tab-pane>
            </a-tabs>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
const columns = [
  {
    title: "药品名称",
    dataIndex: "medicinename",
    // width: "20%",
    scopedSlots: { customRender: "medicinename" }
  },
  {
    title: "品牌",
    dataIndex: "brandname",
    scopedSlots: { customRender: "brandname" }
  },
  {
    title: "出库包装",
    dataIndex: "sellpackage",
    scopedSlots: { customRender: "sellpackage" }
  },
  {
    title: "药品剂型",
    dataIndex: "dosageform",
    scopedSlots: { customRender: "dosageform" }
  },
  {
    title: "剂量/次",
    dataIndex: "dosesiz",
    scopedSlots: { customRender: "dosesiz" }
  },
  {
    title: "使用频次",
    dataIndex: "frequency",
    scopedSlots: { customRender: "frequency" }
  },
  {
    title: "药品数量",
    dataIndex: "medicinecnt",
    scopedSlots: { customRender: "medicinecnt" }
  },
  {
    title: "药品价格",
    dataIndex: "money",
    scopedSlots: { customRender: "money" }
  },
  {
    title: "费用小计",
    dataIndex: "allmoney",
    scopedSlots: { customRender: "allmoney" }
  }
];
import DoctorHeader from "@/components/header/doc-header";
export default {
  components: { DoctorHeader },
  data() {
    return {
      name: "",
      department: "",
      recents: [],
      recipedata: [],
      checkdtos: [],
      pagination: {},
      loading: false,
      columns,
      recipeamount: 0,
      checkamount: 0,
      xcode: "",
      tipinfo: ""
    };
  },
  created() {
    this.getDoctorAndPatients();
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
    recipedata(newValue) {
      let amount = 0;
      newValue.map(item => {
        amount = amount + parseFloat(item.allmoney);
      });
      this.recipeamount = amount.toFixed(2);
    },
    checkdtos(newValue) {
      let amount = 0;
      newValue.map(item => {
        amount = amount + parseFloat(item.money);
      });
      this.checkamount = amount.toFixed(2);
    }
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
    getDoctorAndPatients() {
      //查询医生信息
      this.$ajax
        .get("api/v1/doctor1")
        .then(res => {
          window.console.log(res);
          this.department = res.data.department;
          this.name = res.data.doctorName;
        })
        .catch(res => {
          window.console.log(res);
        });
    },
    getRecentDiagnoseMock() {
      this.recents = [];
      //查询医生信息
      this.$ajax
        .get("/api/v1/diagnose/recentaa")
        .then(res => {
          window.console.log(res);
          this.recents = res.data.list;
          this.checkdtos = res.data.list[0].checkDTOS;
          this.getSingleMoney(res.data.list[0].recipe.medicine);
          //   this.recipedata = res.data.list[0].recipe.medicine;
        })
        .catch(res => {
          window.console.log(res);
        });
    },
    getRecentDiagnose() {
      this.recents = [];
      if (this.xcode === "") {
        this.tipinfo = "输入患者二维码";
        this.error();
        return;
      }
      this.$ajax
        .get("/api/v1/diagnose/recent", {
          headers: {
            "X-CARD-CODE": this.xcode
          }
        })
        .then(res => {
          window.console.log(res);
          this.recents = res.data;
          this.checkdtos = res.data[0].checkDTOS;
          this.getSingleMoney(res.data[0].recipe.medicine);
          //   this.recipedata = res.data.list[0].recipe.medicine;
        })
        .catch(res => {
          window.console.log(res);
        });
      this.$ajax
        .post("/api/v1/diagnose", null, {
          headers: {
            "Content-Type": "application/json",
            "X-CARD-CODE": this.xcode
          }
        })
        .then(res => {
          window.console.log(res);
        })
        .catch(res => {
          window.console.log(res);
        });
    },
    getSingleMoney(colsss) {
      for (let i = 0; i < colsss.length; i++) {
        const element = colsss[i];
        element.allmoney = (element.medicinecnt * element.money).toFixed(2);
      }
      this.recipedata = colsss;
    },
    getSingleMoney1(colsss) {
      for (let i = 0; i < colsss.length; i++) {
        const element = colsss[i];
        element.key = "key" + i;
        element.allmoney = (element.medicinecnt * element.money).toFixed(2);
      }
      this.recipedata = colsss;
    },
    success() {
      this.$success({
        title: "成功提示",
        // JSX support
        content: this.tipinfo
      });
    },
    error() {
      this.$error({
        title: "错误提示",
        content: this.tipinfo
      });
    }
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
  .main_content {
    flex-direction: column;
    .patient {
      width: 100%;
      height: 60px;
      display: flex;
      justify-content: center;
      align-items: center;
      .pname {
        height: 33px;
        width: 100px;
        font-size: 24px;
        font-family: PingFangSC-Regular, PingFang SC;
        font-weight: 400;
        color: rgba(51, 51, 51, 1);
        line-height: 33px;
        text-align: left;
      }
    }
    .saoma {
      width: 100%;
      height: 60px;
      display: flex;
      justify-content: center;
      align-items: center;
      .ainput {
        width: 600px;
      }
      .pname {
        width: 100px;
      }
    }
    .recent {
      width: 100%;
      height: 180px;
      display: flex;
      justify-content: center;
      align-items: center;
      .incontent {
        width: 1000px;
        display: flex;
        // justify-content: space-between;
        .css1 {
          width: 300px;
          height: 145px;
          background: rgba(135, 182, 242, 1);
          box-shadow: 0px 2px 8px 0px rgba(0, 0, 0, 0.2);
          border-radius: 2px;
          color: white;
          margin: 20px;
          div {
            padding-bottom: 10px;
            height: 35px;
            display: flex;
            flex-direction: row;
            align-items: center;
            .text {
              font-size: 16px;
              font-family: PingFangSC-Regular, PingFang SC;
              font-weight: 400;
              color: rgba(255, 255, 255, 1);
              line-height: 28px;
            }
          }
        }
        .css2 {
          width: 300px;
          height: 145px;
          background: rgba(255, 255, 255, 1);
          box-shadow: 0px 2px 8px 1px rgba(0, 0, 0, 0.09);
          border-radius: 2px;
          margin: 20px;
          div {
            padding-bottom: 10px;
            height: 35px;
            display: flex;
            flex-direction: row;
            align-items: center;
            .text {
              font-size: 16px;
              font-family: PingFangSC-Regular, PingFang SC;
              font-weight: 400;
              line-height: 28px;
            }
          }
        }
      }
    }
    .maininfo {
      width: 100%;
      min-height: 1200px;
      display: flex;
      align-items: center;
      flex-direction: column;
      .recipe {
        width: 1000px;
        min-height: 547px;
        background: rgba(255, 255, 255, 1);
      }
    }
  }
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
}
.recipeamount {
  text-align: right;
  margin: 15px;
  font-size: 16px;
  font-weight: 400;
  /*color: rgba(255, 91, 91, 1);*/
  color: rgba(0, 0, 0, 0.65);
  line-height: 21px;
}
.checkcss {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  flex-wrap: wrap;
  .css1 {
    width: 299px;
    height: 142px;
    margin-left: 30px;
    margin-top: 20px;
    box-shadow: 0px 2px 8px 1px rgba(0, 0, 0, 0.09);
    position: relative;
    div {
      padding-bottom: 10px;
      height: 35px;
      display: flex;
      flex-direction: row;
      align-items: center; /*垂直居中*/
      // justify-content: center; /*水平居中*/
      img {
        vertical-align: middle;
        align-items: center;
      }
      .text {
        flex: 1;
        margin-top: 10px;
        margin-left: 5px;
      }
    }
    .inc1 {
      position: absolute;
      right: 10px;
      bottom: 10px;
    }
  }
}
</style>
