<template>
  <div class="check-table">
    <!-- form -->
    <a-drawer title="开检查" :width="400" placement="right" :closable="false" :visible="drawerVisible">
      <a-form :form="form" @submit="onFormSubmit">
        <a-form-item label="项目名称">
          <a-select
            v-decorator="[
          'description',
          { rules: [{ required: true, message: '请选择项目名称!' }] },
        ]"
            style="width: 100%"
            @change="handleChange"
            placeholder="请输入项目名称"
          >
            <a-select-option value="血常规">血常规</a-select-option>
            <a-select-option value="溶菌酶测定">溶菌酶测定</a-select-option>
            <a-select-option value="普通视力检查">普通视力检查</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="科室">
          <a-select
            v-decorator="[
          'department',
          { rules: [{ required: true, message: '请输入科室!' }] },
        ]"
            style="width: 100%"
            @change="handleChange"
            placeholder="请输入项目名称"
          >
            <a-select-option value="检测科">检测科</a-select-option>
            <a-select-option value="X光室">X光室</a-select-option>
            <a-select-option value="CT室">CT室</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="费用">
          <a-input-number
            style="width: 100%;"
            :min="0"
            :max="1000"
            v-decorator="[
              'money',
              {
                rules: [
                  { type: 'number', required: true, message: '请输入检查费用' }
                ]
              }
            ]"
          />
        </a-form-item>
        <a-form-item>
          <a-button type="primary" html-type="submit">确定</a-button>
          <a-button type="primary" style="margin-left: 10px;" @click="onDrawerInvisible">取消</a-button>
        </a-form-item>
      </a-form>
    </a-drawer>
    <!-- table -->
    <div class="css">
      <a-card title :bordered="true" class="css1" v-for="(checks,index) in tableData" :key="index">
        <div>
          <img src="@/assets/c1.png" />
          <div class="text">项目名称：{{ checks.description }}(费用:{{ checks.money }})</div>
        </div>
        <div>
          <img src="@/assets/c2.png" />
          <div class="text">检查科室：{{ checks.department }}</div>
        </div>
        <div>
          <img src="@/assets/c3.png" />
          <div class="text">检查时间：2019-11-22</div>
        </div>
        <div class="inc1">
          <img src="@/assets/c4.png" @click="onDelData(checks.diagnosecode)" />
        </div>
      </a-card>
    </div>
    <div class="amount">
      合计：
      <span style="color: red;">￥{{ amount }}</span>
    </div>
  </div>
</template>

<script>
const tableColumns = [
  {
    title: "项目名称",
    dataIndex: "description",
    scopedSlots: { customRender: "description" }
  },
  {
    title: "科室",
    dataIndex: "department",
    scopedSlots: { customRender: "department" }
  },
  {
    title: "费用",
    dataIndex: "money",
    scopedSlots: { customRender: "money" }
  }
];

export default {
  name: "check-table",
  data() {
    return {
      // table
      size: "default",
      tableColumns,
      tableData: [],
      tableSelectedRowKeys: [],
      // drawer
      drawerVisible: false,
      // form
      form: this.$form.createForm(this),
      // auto input
      autoInputDataSource: [],
      // other
      amount: 0
    };
  },
  watch: {
    tableData(newVal) {
      let amount = 0;
      newVal.map(item => {
        amount = amount + item.money;
      });
      this.amount = amount;
    }
  },
  methods: {
    // form
    onFormSubmit(event) {
      event.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          values["diagnosecode"] = new Date().getTime();
          values["id"] = new Date().getTime();
          this.tableData.push(values);
          this.onDrawerInvisible();
          this.form.resetFields();
        }
      });
    },
    // drawer
    onDrawerVisible() {
      this.drawerVisible = true;
    },
    handleChange() {
      window.console.log("change");
    },
    onDrawerInvisible() {
      this.drawerVisible = false;
    },
    // table
    onTableSelectedChange(tableSelectedRowKeys) {
      this.tableSelectedRowKeys = tableSelectedRowKeys;
    },
    onDelData(diagnosecode) {
      window.console.log(diagnosecode);
      this.tableData = this.tableData.filter(item => {
        return item.diagnosecode != diagnosecode;
      });
      window.console.log(this.tableData);
    },
    onDeleteTableData() {
      this.tableData = this.tableData.filter(item => {
        return this.tableSelectedRowKeys.indexOf(item.diagnosecode) < 0;
      });
      this.tableSelectedRowKeys = [];
    },
    // component
    onCompleted() {
      this.$emit("onCompleted", this.tableData);
    }
  }
};
</script>

<style lang="less" scoped>
.bg {
  background: red;
}
.css {
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
.actions {
  display: flex;
  flex-direction: row;
  justify-content: flex-end;

  .action {
    margin: 0 5px 15px;
  }
}

.amount {
  text-align: right;
  margin: 15px;
  font-size: 16px;
  font-weight: 400;
  /*color: rgba(255, 91, 91, 1);*/
  color: rgba(0, 0, 0, 0.65);
  line-height: 21px;
}
</style>
