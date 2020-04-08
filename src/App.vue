<template>
  <div class="container-fluid" id="app">
    <!-- As a heading -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
      <span class="navbar-brand mb-0 h1">租金计算器</span>
      <div class="collapse navbar-collapse justify-content-end" id="navbarNavDropdown">
        <ul class="navbar-nav">
          <li class="nav-item active">
            <a class="nav-link" href="https://github.com/Andrew8102/rent_caculator">
              Github项目地址
              <span class="sr-only">(current)</span>
            </a>
          </li>
          <li class="nav-item active">
            <a class="nav-link" href="https://moechu.cn/lab.html">
              更多工具
              <span class="sr-only">(current)</span>
            </a>
          </li>
          <li class="nav-item active">
            <a class="nav-link" href="https://moechu.cn/">
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
                <input
                  type="number"
                  class="form-control"
                  min="0.00"
                  max="1.00"
                  step="0.01"
                  @change="redraw"
                  id="item.id"
                  v-model="item.discount"
                />
              </div>
            </div>

            <div class="form-group col-md-5">
              <div class="input-group">
                <div class="input-group-prepend">
                  <span class="input-group-text">从几日开始</span>
                </div>
                <input
                  type="number"
                  class="form-control"
                  id="item.id"
                  @change="redraw"
                  :min="day1"
                  :max="day2"
                  step="1"
                  v-model="item.discount_date"
                />
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
                @click="deleteCoupon(i);redraw()"
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
    <div class="text-center"><p>©2020 Moechu.cn | Under MIT License</p></div>
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
      option: {},
      arrPrice : []
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
            data: this.discountRent[0],
            type: "line",
            yAxisIndex: 0,
            smooth: true
          },
          {
            name: "Price",
            data: this.discountRent[1],
            type: "line",
            yAxisIndex: 1,
            smooth: true
          }
        ]
      };
      //console.log(this.option);
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
    },
    discountRent: function() {
      let arr = this.coupons;
      //冒泡排序，先对打折的日期进行从小到大的排序，对应的折扣一起放置到arr数组当中
      for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length - 1 - i; j++) {
          if (
            parseInt(arr[j]["discount_date"]) >
            parseInt(arr[j + 1]["discount_date"])
          ) {
            let temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
          }
        }
      }
      //由于js内部数组和对象是地址传递引用https://blog.csdn.net/zyddj123/article/details/86636724
      //需要数组转为json字符串，再转为json对象进行赋值https://blog.csdn.net/qq_43363884/article/details/100511380
      const temparr = JSON.parse(JSON.stringify(this.rent[1]))
      //必须使用一个在此处不会受影响的数组
      let price = temparr;
      //console.log(price)
      for (let n = 0; n < arr.length; n++) {
        if (n == arr.length - 1) {
          //判断是否是最后一个折扣，是的话计算日期延续到价格的最后日期，注意其与真实价格数组索引之间的关系
          //其实可以用es2015的for...of新特性，参见https://mp.weixin.qq.com/s/mMXRsg-sNwxsXwk07Z4FIA
          //console.log(n + "," + arr[n]["discount_date"] + "," + price[price.length - 1][0]);
          for (
            let i = parseInt(arr[n]["discount_date"]);
            i <= parseInt(price[price.length - 1][0]);
            i++
          ) {
            //console.log("数组真实索引i:" + (i - this.day1));
            price[i - parseInt(this.day1)][1] = parseFloat(
              (
                price[i - parseInt(this.day1)][1] *
                parseFloat(arr[n]["discount"])
              ).toFixed(2)
            );
          }
        } else {
          //否则就只看折扣区间

          for (
            let i = parseInt(arr[n]["discount_date"]);
            i < parseInt(arr[n + 1]["discount_date"]);
            i++
          ) {
            //console.log("数组真实索引i:" + (i - this.day1));
            price[i - parseInt(this.day1)][1] = parseFloat(
              (
                price[i - parseInt(this.day1)][1] *
                parseFloat(arr[n]["discount"])
              ).toFixed(2)
            );
          }
        }
      }

      let sum = [];
      //console.log(price);
      for (let item of price) {
        //console.log(item[0] + item[1]);
        sum.push([
          parseInt(item[0]),
          parseFloat((item[1] * item[0]).toFixed(2))
        ]);
      }
      let dateset_new = [sum, price];
      return dateset_new;
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
