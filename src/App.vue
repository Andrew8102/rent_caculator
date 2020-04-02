<template>
  <div class="container-fluid" id="app">
    <!-- As a heading -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
      <span class="navbar-brand mb-0 h1">租金计算器</span>
      <div class="collapse navbar-collapse justify-content-end" id="navbarNavDropdown">
        <ul class="navbar-nav">
          <li class="nav-item active">
            <a class="nav-link" href="#">
              更多工具
              <span class="sr-only">(current)</span>
            </a>
          </li>
          <li class="nav-item active">
            <a class="nav-link" href="#">
              返回主页
              <span class="sr-only">(current)</span>
            </a>
          </li>
        </ul>
      </div>
    </nav>
    <div class="tb">
      <div class>
        <Chart id="myChart" :option="option" />
        <div class="form-row justify-content-around" style="margin-bottom:10px;">
          <div class="card-text">定价公式：{{rent[2]}}</div>
          <div class="card-text">总价公式：{{rent[3]}}</div>
        </div>
      </div>

      <div class id="form">
        <form action>
          <h3>输入基准租金价格</h3>
          <div class="form-row justify-content-center">
            <div class="form-group col-md-6">
              <label for="d1">第几天</label>
              <input type="text" class="form-control" id="day1" @change="redraw" v-model="day1" />
            </div>
            <div class="form-group col-md-6">
              <label for="p1">价格</label>
              <input type="text" class="form-control" id="price1" @change="redraw" v-model="price1" />
            </div>
          </div>
          <div class="form-row justify-content-center">
            <div class="form-group col-md-6">
              <label for="d2">第几天</label>
              <input type="text" class="form-control" id="day2" @change="redraw" v-model="day2" />
            </div>
            <div class="form-group col-md-6">
              <label for="p2">价格</label>
              <input type="text" class="form-control" id="price2" @change="redraw" v-model="price2" />
            </div>
          </div>
          <div class="form-row form-group">
            <h3>填写折扣</h3>
            <div class="btn btn-primary" @click="addCoupon">新增折扣</div>
          </div>

          <div class="coupon form-row justify-content-center" v-for="(item, i) in coupons" :key="i">
            <div class="form-group col-md-6">
              <div class="input-group">
                <div class="input-group-prepend">
                  <span class="input-group-text">优惠折扣</span>
                </div>
                <input type="text" class="form-control" id="item.id" v-model="item.discount" />
              </div>
            </div>

            <div class="form-group col-md-5">
              <div class="input-group">
                <div class="input-group-prepend">
                  <span class="input-group-text">从几日开始</span>
                </div>
                <input type="text" class="form-control" id="item.id" v-model="item.discount_date" />
              </div>
            </div>

            <div class="form-group col-md-1 align-self-center">
              <svg
                class="bi bi-x-circle"
                width="1em"
                height="1em"
                viewBox="0 0 16 16"
                fill="currentColor"
                xmlns="http://www.w3.org/2000/svg"
                @click="deleteCoupon(i)"
              >
                <path
                  fill-rule="evenodd"
                  d="M8 15A7 7 0 108 1a7 7 0 000 14zm0 1A8 8 0 108 0a8 8 0 000 16z"
                  clip-rule="evenodd"
                />
                <path
                  fill-rule="evenodd"
                  d="M11.854 4.146a.5.5 0 010 .708l-7 7a.5.5 0 01-.708-.708l7-7a.5.5 0 01.708 0z"
                  clip-rule="evenodd"
                />
                <path
                  fill-rule="evenodd"
                  d="M4.146 4.146a.5.5 0 000 .708l7 7a.5.5 0 00.708-.708l-7-7a.5.5 0 00-.708 0z"
                  clip-rule="evenodd"
                />
              </svg>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import Chart from "./components/Chart";
export default {
  name: "App",
  components: {
    Chart
  },
  data() {
    return {
      day1: 5,
      day2: 30,
      price1: 6.7,
      price2: 1.7,
      coupons: [
        {
          id: 1,
          discount_date: 7,
          discount: 0.8
        },
        {
          id: 2,
          discount_date: 14,
          discount: 0.7
        },
        {
          id: 3,
          discount_date: 21,
          discount: 0.6
        }
      ],
      option: {}
    };
  },
  methods: {
    addCoupon() {
      let n = this.coupons.length;
      this.coupons.push({
        id: n,
        discount_date: 14,
        discount: 0.7
      });
      console.log(this.coupons);
    },
    deleteCoupon(i) {
      this.coupons.splice(i, 1);
      console.log("删除了第" + i);
    },
    redraw() {
      this.option = {
        title: {
          text: "数码产品租金计算器",
          textStyle: { fontSize: 25 },
          textAlign: "auto"
        },
        tooltip: {},
        xAxis: {
          name: "租赁日期",
          type: "value"
        },
        legend: {
          data: [
            {
              name: "Price",
              // 强制设置图形为圆。
              icon: "circle",
              // 设置文本为红色
              textStyle: {
                color: "blue"
              }
            },
            {
              name: "Sum",
              // 强制设置图形为圆。
              icon: "circle",
              // 设置文本为红色
              textStyle: {
                color: "red"
              }
            }
          ]
        },
        yAxis: [
          {
            name: "总租金",
            type: "value"
          },
          {
            name: "每日定价",
            type: "value"
          }
        ],
        series: [
          {
            name: "Sum",
            data: this.rent[0],
            type: "line",
            yAxisIndex: 0,
            smooth: true
          },
          {
            name: "Price",
            data: this.rent[1],
            type: "line",
            yAxisIndex: 1,
            smooth: true
          }
        ]
      };
      console.log(this.option);
    }
  },
  mounted() {
    this.redraw();
  },
  computed: {
    //xf(x)=ax+b,f(x)=a+b/x
    //ax+y=b,cx+y=d
    //where a,c=days b,d=sum=price*days,solve x,y
    //Ans: x=(b-d)/(a-c),y=(ad-bc)/(a-c)
    //which means delta_price = price2*days2 - price1*days1/days2-days1, fixed_earn = (price1-price2)*days1*days2/days2-days1
    //Final equation: sum = delta_price * rent_day + fixed_earned
    rent: function() {
      let p1 = parseFloat(this.price1);
      let p2 = parseFloat(this.price2);
      let d1 = parseInt(this.day1);
      let d2 = parseInt(this.day2);
      let delta = 0,
        fixed_earn = 0,
        sum = 0,
        daily_price = 0;
      let dataset_sum = [],
        dataset_price = [],
        dataset = [];
      delta = (p2 * d2 - p1 * d1) / (d2 - d1);
      fixed_earn = ((p1 - p2) * d1 * d2) / (d2 - d1);
      for (let i = d1; i < d2; i++) {
        sum = parseFloat((delta * i + fixed_earn).toFixed(2));
        daily_price = parseFloat((delta + fixed_earn / i).toFixed(2));
        dataset_sum.push([i, sum]);
        dataset_price.push([i, daily_price]);
      }
      let x = "y=" + delta + "+" + fixed_earn + "/x";
      let xfx = "y=" + delta + "x+" + fixed_earn;
      console.log("租金公式:" + xfx);
      console.log("定价公式:" + x);
      dataset.push(dataset_sum, dataset_price, x, xfx);
      return dataset;
    }
  }
};
</script>

<style>
.tb {
  width: 100%;
  padding: 10px 20px;
  box-shadow: 0 0 5px #919191;
}
#mychart1 {
  text-align: center;
  margin-top: 5vh;
}
body {
  max-width: 800px;
  margin: 0 auto;
}
svg {
  vertical-align: baseline;
}
</style>
