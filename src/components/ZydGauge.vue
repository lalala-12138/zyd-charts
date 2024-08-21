<template>
  <div v-loading="loading" class="zyd-gauge-container">
    <div v-if="loading || dataSource != -1" class="zyd-gauge" ref="zydGauge"></div>
    <ZydEmpty :url="emptyData.url" :desc="emptyData.desc" v-if="!loading && dataSource == -1" />
  </div>
</template>
<script>
import ZydEmpty from './ZydEmpty.vue';

export default {
  name: 'ZydLine',
  components: { ZydEmpty },
  props: {
    loading: {
      default: false,
      type: Boolean,
    },
    setOption: {
      default: () => {},
      type: Object,
    },
    max: {
      default: 180,
      type: Number,
    },
    dataSource: {
      default: -1,
      type: Number,
    },
    emptyData: {
      default: () => {
        return {};
      },
      type: Object,
    },
  },
  data() {
    return {};
  },
  mounted() {
    this.init();
  },
  watch: {
    dataSource: function () {
      this.init();
    },
  },
  methods: {
    deepMerge(target, source) {
      for (const key in source) {
        if (source[key] && typeof source[key] === 'object') {
          if (!target[key] || typeof target[key] !== 'object') {
            target[key] = {};
          }
          deepMerge(target[key], source[key]);
        } else {
          target[key] = source[key];
        }
      }
      return target;
    },

    init() {
      const myChart = this.$echarts.init(this.$refs.zydGauge);
      const option = {
        series: [
          //白色圈刻度
          {
            name: '',
            type: 'gauge',
            radius: '168.5%',
            splitNumber: 24,
            center: ['50%', '85%'],
            startAngle: 180, //刻度起始
            endAngle: 0, //刻度结束
            z: 4,
            axisTick: {
              show: false,
            },
            splitLine: {
              length: 18, //刻度节点线长度
              lineStyle: {
                width: 3,
                color: 'white',
              }, //刻度节点线
            },
            axisLabel: {
              show: false,
              color: '#',
              fontSize: 12,
            }, //刻度节点文字颜色
            pointer: {
              show: false,
            },
            axisLine: {
              lineStyle: {
                opacity: 0,
              },
            },
            detail: {
              show: false,
            },
            data: [
              {
                value: 0,
                name: '',
              },
            ],
          },
          //外圈刻度
          {
            name: '外圈刻度m', //刻度背景
            type: 'gauge',
            z: 2,
            radius: '140%',
            splitNumber: 4,
            startAngle: 180,
            endAngle: 0,
            min: 0,
            max: this.max,
            center: ['50%', '85%'], //整体的位置设置
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: [
                  [
                    this.dataSource < 0
                      ? (this.dataSource - Math.floor(this.dataSource / 10) * 10) /
                        (this.max - Math.floor(this.dataSource / 10) * 10)
                      : this.dataSource / this.max,
                    '#00B3FE',
                  ],
                  [1, '#00B3FE'],
                ],
                width: 35,
                opacity: 0, //刻度背景宽度
              },
            },
            splitLine: {
              show: false,
            },
            data: [
              {
                show: false,
                value: this.dataSource,
              },
            ],
            axisLabel: {
              show: true,
              //   distance: 10,
              textStyle: {
                color: '#999999Y',
                fontSize: '15',
              },
            },
            pointer: {
              show: true,
              shadowColor: 'auto', //默认透明
              shadowBlur: 5,
              length: '80%',
              radius: '10%',
              width: 5, //指针粗细
              itemStyle: {
                color: '#195BDD',
              },
            },
            axisTick: {
              show: false,
            },
            detail: {
              show: 0,
            },
          },
          //蓝圈背景
          {
            name: '蓝圈背景', //刻度背景
            type: 'gauge',
            z: 2,
            radius: '150%',
            splitNumber: 25,
            startAngle: 180,
            endAngle: 0,
            min: this.dataSource < 0 ? Math.floor(this.dataSource / 10) * 10 : 0,
            max: 1800,
            center: ['50%', '85%'], //整体的位置设置
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: [
                  [this.dataSource / this.max, '#2355D8'],
                  [1, '#ccc'],
                ],
                width: 15,
                opacity: 1, //刻度背景宽度
              },
            },
            splitLine: {
              show: false,
            },
            data: [
              {
                show: false,
                value: this.dataSource,
              },
            ],
            axisLabel: {
              show: false,
            },
            pointer: {
              show: false,
            },
            axisTick: {
              show: false,
            },
            detail: {
              show: 0,
            },
          },
          //刻度
          {
            name: '刻度尺',
            type: 'gauge',
            z: 4,
            radius: '150%',
            splitNumber: 4,
            startAngle: 180,
            endAngle: 0,
            // min: 0,
            // max: 15,
            center: ['50%', '85%'], //整体的位置设置
            axisLine: {
              // 坐标轴线
              lineStyle: {
                // 属性lineStyle控制线条样式
                color: [
                  [this.dataSource, '#e7e7e7'],
                  [1, '#e7e7e7'],
                ],
                width: 10,
                opacity: 0, //刻度背景宽度
              },
            },
            data: [
              {
                value: this.dataSource,
              },
            ],
            axisLabel: {
              show: false,
            },
            pointer: {
              show: false,
            },
            detail: {
              show: false,
            },
            axisTick: {
              show: true,
              lineStyle: {
                color: '#e7e7e7',
                width: 3,
              },
              length: 2,
              splitNumber: 6,
            },
            splitLine: {
              length: 15, //刻度节点线长度
              lineStyle: {
                width: 3,
                color: '#e7e7e7',
              }, //刻度节点线
            },
          },
        ],
      };
      this.$nextTick(() => {
        myChart.setOption(deepMerge(option, this.setOption));
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.zyd-gauge-container {
  width: 100%;
  height: 300px;
  background: #fff;
  .zyd-gauge {
    width: 100%;
    height: 100%;
  }
}
</style>
