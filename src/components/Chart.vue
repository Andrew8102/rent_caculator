<template>
  <div :id="id" :style="style"></div>
</template>

<script>
export default {
  name: "Chart",
  data() {
    return {
      //echarts实例
      chart: ""
    };
  },
  props: {
    //父组件需要传递的参数：id，width，height，option
    id: {
      type: String
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "300px"
    },
    option: {
      type: Object,
      //Object类型的prop值一定要用函数return出来，不然会报错。原理和data是一样的，
      //使用闭包保证一个vue实例拥有自己的一份props
      default() {
        return {
          title: { text: "数码产品租金计算器" },
          tooltip: {},
          xAxis: {
            name: "x",
            type: "value"
          },
          yAxis: [
            {
              name: "y",
              type: "value"
            },
            {
              name: "y",
              type: "value"
            }
          ],
          series: [
            {
              data: [
                [1, 1],
                [2, 3]
              ],
              type: "line",
              yAxisIndex: 0,
              smooth: true
            },
            {
              data: [
                [1, 1],
                [2, 3]
              ],
              type: "line",
              yAxisIndex: 1,
              smooth: true
            }
          ]
        };
      }
    }
  },

  computed: {
    style() {
      return {
        height: this.height,

        width: this.width
      };
    }
  },

  mounted() {
    this.init();
  },

  methods: {
    init() {
      this.chart = this.$echarts.init(document.getElementById(this.id));
      this.chart.setOption(this.option);
      window.addEventListener("resize", this.chart.resize);
    }
  },
  watch: {
    //观察option的变化
    option: {
      handler(newVal, oldVal) {
        if (this.chart) {
          if (newVal) {
            this.chart.setOption(newVal);
          } else {
            this.chart.setOption(oldVal);
          }
        } else {
          this.init();
        }
      },
      deep: true //对象内部属性的监听，关键。
    }
  }
};
</script>