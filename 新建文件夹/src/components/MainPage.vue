<template>
<div>
  <div>
    <Menu></Menu>
  </div>
  <div class="ec-main">
    <!-- 电量 -->
    <div class="electric-charge">
      <div class="electric-charge-date">选择日期</div>
      <hr color="#ECECEC">
      <div class="electric-charge-content">
        <div class="charge-ratio-left">
          <div class="charge-ratio-left-title">
            <div class="total-electrical-output-title">总发电量:</div>
            <div class="total-electrical-use-title">总用电量:</div>
            <div clsss="energy-storage-title">储能电量:</div>
          </div>
          <div class="charge-ratio-left-num">
            <div class="total-electrical-output-num">{{ totalElectricalOutput }}</div>
            <div class="total-electrical-use-num">{{ totalElectricalUse }}</div>
            <div class="energy-storage-num">{{ energyStorage }}</div>
          </div>
          <div class="charge-ratio-left-unit">
            <div class="total-electrical-output-unit">kWH</div>
            <div class="total-electrical-use-unit">kWH</div>
            <div class="energy-storage-unit">kWH</div>
          </div>
        </div>
        <div class="charge-ratio-middle">
          <div id="power-generation-ratio"></div>
        </div>
        <div class="charge-ratio-right">
          <div id="power-use-ratio"></div>
        </div>
      </div>
    </div>
    <!-- 安全运行 -->
    <div class="safe-run-content">
      <div class="safe-run-days">
        <div class="safe-run-days-img">
          <img src="../../static/img/safe.png" alt=""><span>安全运行</span>
        </div>
        <div class="safe-run-days-count">143</div>
        <div class="safe-run-days-unit">天</div>
      </div>
    </div>
    <!-- 数据 -->
    <div class="safe-run-data">
      <div class="alternating-generatrix-voltage"><span>交流母线电压:</span><span class="alternating-generatrix-voltage-data">227.8</span><span>V</span></div>
      <div class="running-state"><span>运行状态:</span><span class="running-state-content">并网运行</span></div>
      <div class="dc-generatrix-voltage"><span>直流母线电压:</span><span class="dc-generatrix-voltage-data">110.7</span><span>V</span></div>
      <div class="dot-power"><span>并网点功率:</span><span class="dot-power-data">123.8</span><span>kW</span></div>
      <div class="system-frequency"><span>系统频率:</span><span class="system-frequency-data">55</span><span>HZ</span></div>
    </div>
    <div class="microgrid-dist">
      <!-- 微网发用电分布 -->
      <div id="microgrid-elec-dist"></div>
      <!-- 微网运行费用分布 -->
      <div id="microgrid-cost-dist"></div>
    </div>
    <div class="microgrid-electric">
      <!-- 光伏发电功率 -->
      <div id="pvp-generation"></div>
      <!-- 微网用电 -->
      <div id="microgrid-electric-use"></div>
    </div>
    <div class="energy-store-electric">
      <!-- 储能充放电功率 -->
      <div id="energy-store-electric-power"></div>
      <!-- 充电对比图 -->
      <div id="charge-contrast"></div>
    </div>
  </div>
</div>
</template>
<script>
import echarts from 'echarts';
import { throttle } from '../common';
import Menu from './Menu';
export default {
  components: {
    Menu
  },
  data () {
    return {
      totalElectricalOutput: 86.984,
      totalElectricalUse: 865,
      energyStorage: 547,
      safeRunningDays: 143,
      menuShow: false
    };
  },
  mounted () {
    this.powerGenerationRatioChart = echarts.init(document.getElementById('power-generation-ratio'));
    this.powerUseRatioChart = echarts.init(document.getElementById('power-use-ratio'));
    this.microElectricityDist = echarts.init(document.getElementById('microgrid-elec-dist'));
    this.microCostDist = echarts.init(document.getElementById('microgrid-cost-dist'));
    this.pvpGeneration = echarts.init(document.getElementById('pvp-generation'));
    this.microgridElectricUse = echarts.init(document.getElementById('microgrid-electric-use'));
    this.energyStoreElectricPower = echarts.init(document.getElementById('energy-store-electric-power'));
    this.chargeContrast = echarts.init(document.getElementById('charge-contrast'));
    this.resizeChart = throttle(500, false, () => {
      this.powerUseRatioChart.resize();
      this.powerGenerationRatioChart.resize();
      this.microElectricityDist.resize();
      this.microCostDist.resize();
      this.pvpGeneration.resize();
      this.microgridElectricUse.resize();
      this.energyStoreElectricPower.resize();
      this.chargeContrast.resize();
    });
    window.addEventListener('resize', this.resizeChart);
    this.drawPowerGenerationRatio();
    this.drawPowerUseRatio();
    this.drawMicroElectricityDist();
    this.drawMicrogridCostDist();
    this.drawPvpGeneration();
    this.drawMicrogridElectricUse();
    this.drawEnergyStoreElectricPower();
    this.drawChargeContrast();
  },
  methods: {
    // 绘制发电量占比
    drawPowerGenerationRatio () {
      var width = getComputedStyle(document.querySelector('#power-generation-ratio')).width.replace('px', '');
      var center = '';
      var radius = '';
      if (width === '350') {
        radius = '55%';
        center = [175, 175];
      } else {
        radius = '50%';
        center = [150, 150];
      }
      var option = {
        title: {
          text: '发电量占比',
          x: 'center',
          textStyle: {
            fontWeight: 'normal',
            fontSize: 16,
            lineHeight: 22,
            color: '#333333'
          }
        },
        series: [
          {
            type: 'pie',
            radius,
            center,
            itemStyle: {
              normal: {
                color: function(params) {
                  var colorList = [
                    '#F48686', '#F8CA6D', '#7ECAF1'
                  ];
                  return colorList[params.dataIndex];
                }
              }
            },
            label: {
              normal: {
                formatter: value => {
                  var name = value.name;
                  var percentValue = value.percent.toFixed(0);
                  var percent = '%';
                  return `${name}\n${percentValue}${percent}`;
                },
                textStyle: {
                  fontSize: 20
                },
                rich: {
                  percentValue: {
                    fontSize: 20,
                    lineHeight: 43
                  },
                  percent: {
                    fontSize: 16
                  }
                }
              }
            },
            data: [
              { value: 0, name: '' },
              { value: 0, name: '' },
              { value: 0, name: '' }
            ]
          }
        ]
      };
      this.powerGenerationRatioChart.setOption(option);
    },
    //  绘制用电量占比
    drawPowerUseRatio () {
      var width = getComputedStyle(document.querySelector('#power-use-ratio')).width.replace('px', '');
      var center = '';
      var radius = '';
      if (width === '350') {
        radius = '55%';
        center = [175, 175];
      } else {
        radius = '50%';
        center = [150, 150];
      }
      var option = {
        title: {
          text: '用电量占比',
          x: 'center',
          textStyle: {
            fontWeight: 'normal',
            fontSize: 16,
            lineHeight: 22,
            color: '#333333'
          }
        },
        series: [
          {
            type: 'pie',
            radius,
            center,
            itemStyle: {
              normal: {
                color: function(params) {
                  var colorList = [
                    '#63A7E8', '#938CD4', '#B2DB8E'
                  ];
                  return colorList[params.dataIndex];
                }
              }
            },
            label: {
              formatter: value => {
                var name = value.name;
                var percentValue = value.percent.toFixed(0);
                var percent = '%';
                return `${name}\n${percentValue}${percent}`;
              },
              textStyle: {
                fontSize: 20
              },
              rich: {
                percentValue: {
                  fontSize: 20,
                  lineHeight: 43
                },
                percent: {
                  fontSize: 16
                }
              }
            },
            data: [
              { value: 1, name: 'a' },
              { value: 2, name: 'b' },
              { value: 3, name: 'c' }
            ]
          }
        ]
      };
      this.powerUseRatioChart.setOption(option);
    },
    // 绘制微网发用电分布
    drawMicroElectricityDist () {
      var height = getComputedStyle(document.querySelector('#microgrid-elec-dist')).height.replace('px', '');
      var top = height * 0.646;
      // console.log(width);
      // var top = 0;
      // if ( width >= 400 ){
      //   top = 270;
      // } else if (width < 400 && width > 350) {
      //   top = 270;
      // } else if ( width === 350) {
      //   top = 260;
      // } else if ( width < 350 ) {
      //   top = 270;
      // } else {
      //   top = 281;
      // }
      var option = {
        title: {
          text: '微网发用电分布',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: 'transparent',
              color: 'transparent'
            }
          },
          color: '#FFFFFF',
          backgroundColor: 'rgba(0,0,0,0.33)'
          // formatter: params => {
          //   var sum = params.length;
          //   var result = '';
          //   for(let i = 0;i < sum;i++) {
          //     result = result + params[i].seriesName + '\n';
          //   }
          //   return result;
          // }
        },
        grid: [{
          top: 150,
          height: '30%'
        },
        {
          top,
          height: '30%'
        }
        ],
        legend: [
          {
            // x: 'center',
            top: 60,
            width: 600,
            itemWidth: 14,
            itemGap: 32,
            textStyle: {
              color: '#666666',
              fontSize: 14,
              padding: [0, 0, 0, 10]
            },
            data: [{
              name: 'type something',
              icon: 'circle'
            },
            {
              name: 'b',
              icon: 'circle'
            },
            {
              name: 'c',
              icon: 'circle'
            }]
          },
          {
            // x: 'center',
            top: 80,
            data: [{
              name: 'd',
              icon: 'circle'
            },
            {
              name: 'e',
              icon: 'circle'
            },
            {
              name: 'f',
              icon: 'circle'
            }]
          }
        ],
        xAxis: [
          {
            show: false,
            type: 'category',
            boundaryGap: false,
            name: 'h',
            nameTextStyle: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            nameGap: 12,
            data: ['10', '20', '30', '40', '50', '60', '70', '80'],
            axisLine: {
              show: false,
              onZero: true
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          },
          {
            gridIndex: 1,
            type: 'category',
            boundaryGap: false,
            data: ['10', '20', '30', '40', '50', '60', '70', '80'],
            axisLine: {
              show: false,
              onZero: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }

          }
        ],
        yAxis: [
          {
            type: 'value',
            name: 'kWH',
            nameLocation: 'end',
            nameTextStyle: {
              color: '#999',
              fontSize: 14,
              lineHeight: 20
            },
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            splitNumber: 3,
            splitLine: {
              show: false
            },
            scale: true
          },
          {
            gridIndex: 1,
            type: 'value',
            inverse: true,
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            splitNumber: 3,
            splitLine: {
              show: false
            }
          }
        ],
        series: [
          {
            name: 'type something',
            type: 'line',
            stack: '发电总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#9FD7F4'
              }
            },
            data: [30, 40, 50, 60, 50, 40, 30, 35],
            smooth: true
          },
          {
            name: 'b',
            type: 'line',
            stack: '发电总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#F09595'
              }
            },
            data: [35, 45, 55, 65, 55, 45, 35, 40],
            smooth: true
          },
          {
            name: 'c',
            type: 'line',
            stack: '发电总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#FFE0A1'
              }
            },
            data: [40, 50, 60, 70, 80, 50, 60, 45],
            smooth: true
          },
          {
            name: 'd',
            type: 'line',
            stack: '用电总量',
            areaStyle: {},
            symbolSize: 2,
            xAxisIndex: 1,
            yAxisIndex: 1,
            itemStyle: {
              normal: {
                color: '#80BAF2'
              }
            },
            data: [45, 50, 70, 80, 65, 55, 50, 50],
            smooth: true
          },
          {
            name: 'e',
            type: 'line',
            stack: '用电总量',
            areaStyle: {},
            symbolSize: 2,
            xAxisIndex: 1,
            yAxisIndex: 1,
            itemStyle: {
              normal: {
                color: '#CAEAA9'
              }
            },
            data: [60, 60, 65, 80, 80, 80, 70, 65],
            smooth: true
          },
          {
            name: 'f',
            type: 'line',
            stack: '用电总量',
            areaStyle: {},
            symbolSize: 2,
            xAxisIndex: 1,
            yAxisIndex: 1,
            itemStyle: {
              normal: {
                color: '#B4ACF5 '
              }
            },
            data: [65, 65, 70, 85, 85, 85, 75, 65],
            smooth: true
          }
        ]
      };
      this.microElectricityDist.setOption(option);
    },
    // 绘制运行费用分布
    drawMicrogridCostDist () {
      var option = {
        title: {
          text: '微网运行费用分布',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: 'transparent',
              color: 'transparent'
            }
          },
          formatter: '{a}',
          color: '#FFFFFF',
          backgroundColor: 'rgba(0,0,0,0.33)'
        },
        grid: {
          y: 150
        },
        legend: [
          {
            // x: 'center',
            top: 60,
            itemWidth: 14,
            itemGap: 32,
            textStyle: {
              color: '#666666',
              fontSize: 14,
              padding: [0, 0, 0, 10]
            },
            data: [{
              name: 'a',
              icon: 'circle'
            },
            {
              name: 'b',
              icon: 'circle'
            },
            {
              name: 'c',
              icon: 'circle'
            }]
          }
        ],
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            name: 'h',
            nameTextStyle: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            data: ['10', '20', '30', '40', '50', '60', '70', '80'],
            axisLine: {
              show: false,
              onZero: false,
              lineStyle: {
                color: '#999999'
              }
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            data: ['20', '40', '60', '80', '100'],
            axisLine: {
              show: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            splitLine: false
          }
        ],
        series: [
          {
            name: 'a',
            type: 'line',
            stack: '总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#9FD7F4'
              }
            },
            data: [30, 40, 50, 60, 50, 40, 30, 35],
            smooth: true
          },
          {
            name: 'b',
            type: 'line',
            stack: '总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#F09595'
              }
            },
            data: [35, 45, 55, 65, 55, 45, 35, 40],
            smooth: true
          },
          {
            name: 'c',
            type: 'line',
            stack: '总量',
            areaStyle: {},
            symbolSize: 2,
            itemStyle: {
              normal: {
                color: '#FFE0A1'
              }
            },
            data: [40, 50, 60, 70, 80, 50, 60, 45],
            smooth: true
          }
        ]
      };
      this.microCostDist.setOption(option);
    },
    // 绘制光伏发电功率
    drawPvpGeneration () {
      var option = {
        title: {
          text: '光伏发电功率',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#6a7985'
            }
          }
        },
        grid: {
          y: 150
        },
        legend: {
          textStyle: {
            fontSize: 14
          },
          data: [
            {
              name: 'a',
              icon: 'circle'
            }
          ],
          top: 40,
          left: 40,
          itemGap: 50
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            name: 'h',
            nameTextStyle: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            },
            data: ['10', '20', '30', '40', '50', '60', '70', '80'],
            axisLine: {
              show: false,
              onZero: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            data: ['20', '40', '60', '80', '100'],
            axisLine: {
              show: false,
              onZero: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          }
        ],
        series: [
          {
            name: 'a',
            type: 'line',
            stack: '总量',
            areaStyle: {},
            itemStyle: {
              normal: {
                color: '#F8CA6D'
              }
            },
            data: [30, 40, 50, 60, 50, 40, 30, 35],
            smooth: true
          }
        ]
      };
      this.pvpGeneration.setOption(option);
    },
    // 绘制微网用电
    drawMicrogridElectricUse () {
      var option = {
        title: {
          text: '微网用电',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#6a7985'
            }
          }
        },
        grid: {
          y: 150
        },
        legend: {
          textStyle: {
            fontSize: 14
          },
          data: [
            {
              name: 'a',
              icon: 'circle'
            }
          ],
          top: 40,
          left: 40,
          itemGap: 50
        },
        xAxis: {
          type: 'category',
          name: 'h',
          nameLocation: 'start',
          nameTextStyle: {
            color: '#999999',
            fontSize: 14,
            lineHeight: 20
          },
          data: ['10', '20', '30', '40', '50', '60', '70', '80'],
          axisLine: {
            show: true,
            onZero: false,
            lineStyle: {
              color: '#999999'
            }
          },
          axisTick: {
            show: false
          },
          axisLabel: {
            color: '#999999',
            fontSize: 14,
            lineHeight: 20
          },
          boundaryGap: ['25%', '25%']
        },
        yAxis: {
          type: 'category',
          data: ['20', '40', '60', '80', '100'],
          axisLine: {
            show: false,
            onZero: false
          },
          axisTick: {
            show: false
          },
          axisLabel: {
            color: '#999999',
            fontSize: 14,
            lineHeight: 20
          }
        },
        series: {
          name: 'a',
          type: 'line',
          stack: '总量',
          areaStyle: {},
          itemStyle: {
            normal: {
              color: '#63A7E8'
            }
          },
          smooth: true
        }
      };
      this.microgridElectricUse.setOption(option);
    },
    // 绘制储能充放电功率
    drawEnergyStoreElectricPower () {
      var option = {
        title: {
          text: '储能充放电功率',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: 'transparent',
              color: 'transparent'
            }
          },
          formatter: '{a}',
          backgroundColor: 'rgba(0,0,0,0.33)',
          color: '#FFFFFF',
          fontSize: 14,
          lineHeight: 20
        },
        grid: {
          y: 150
        },
        legend: {
          textStyle: {
            fontSize: 14
          },
          data: [
            {
              name: 'test',
              icon: 'circle'
            }
          ],
          top: 60,
          left: 40
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            data: ['10', '20', '30', '40', '50', '60', '70'],
            axisLine: {
              show: false,
              onZero: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          }
        ],
        yAxis: [
          {
            type: 'value',
            data: ['20', '40', '60', '80', '100'],
            axisLine: {
              show: false,
              onZero: false
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#999999',
              fontSize: 14,
              lineHeight: 20
            }
          }
        ],
        series: [
          {
            name: 'test',
            type: 'line',
            stack: '总量',
            symbolSize: 0,
            itemStyle: {
              normal: {
                color: '#F48686'
              }
            },
            data: [30, 40, 50, 60, 50, 40, 30, 35],
            smooth: true
          }
        ]
      };
      this.energyStoreElectricPower.setOption(option);
    },
    // 绘制充电对比图
    drawChargeContrast () {
      var option = {
        title: {
          text: '充电对比图',
          x: 20,
          textStyle: {
            fontWeight: 'normal',
            fontSize: 20,
            lineHeight: 28,
            color: '#666666'
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#6a7985'
            }
          }
        },
        grid: {
          y: 150
        },
        legend: {
          textStyle: {
            fontSize: 14
          },
          data: [
            {
              name: 'a',
              icon: 'circle'
            }
          ],
          top: 40,
          left: 40,
          itemGap: 50
        },
        xAxis: [
          {
            type: 'category',
            boundaryGap: false,
            data: ['10', '20', '30', '40', '50', '60', '70', '80']
          }
        ],
        yAxis: [
          {
            type: 'value',
            data: ['20', '40', '60', '80', '100']
          }
        ],
        series: [
          {
            name: 'a',
            type: 'line',
            stack: '总量',
            areaStyle: {},
            itemStyle: {
              normal: {
                color: '#F48686'
              }
            },
            smooth: true
          }
        ]
      };
      this.chargeContrast.setOption(option);
    }
  }
};
</script>

<style scoped>
@media screen and (min-width:1689px) and (max-width:1920px) {
  .electric-charge {
    width: 68.2%;
    height: 440px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  span {
    display: inline-block;
  }
  .charge-ratio-left-title {
    margin-top: 77px;
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    float: left;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }
  .charge-ratio-left-num {
    float: left;
    margin-top: 60px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 30px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 135px;
    margin-left: 19px;
  }
  .charge-ratio-left-unit {
    float: left;
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }

  #power-generation-ratio,
  #power-use-ratio {
    width: 300px;
    height: 300px;
  }
  .electric-charge-content {
    display: flex;
    justify-content: space-around;
    width: 100%;
  }
  .safe-run-content {
    width: 27.8%;
    height: 84px;
    float: left;
    margin-left: 1%;
    margin-top: 20px;
    background-color: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    height: 100%;
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
    width: 25%;
  }
  .safe-run-days-img>span {
    display: inline;
    margin-left: 1.3%;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 27.8%;
    height: 335px;
    float: left;
    margin-left: 1%;
    margin-top: 20px;
    background-color: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-data>div {
    width: 43.2%;
    height: 21.5%;
    line-height: 72px;
    background: #EEF1FA;
    border-radius: 4px;
    float: left;
    padding-left: 15px;
    display: flex;
    justify-content: space-around;
  }
  .safe-run-data>span {
    display: inline;
  }
  .running-state {
    justify-content: space-between!important;
  }
  .running-state>span:last-child {
    margin-right: 60px!important;
  }
  .safe-run-data>div>span:last-child {
    margin-right: 15px;
  }
  .system-frequency>span:last-child {
    margin-right: 6px;
  }
  .alternating-generatrix-voltage,
  .dc-generatrix-voltage,
  .system-frequency {
    margin-left: 5.4%;
  }
  .running-state,
  .dot-power {
    margin-left: 3.4%;
  }
  .alternating-generatrix-voltage,
  .running-state {
    margin-top: 40px;
  }
  .dc-generatrix-voltage,
  .dot-power,
  .system-frequency {
    margin-top: 18px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 24px;
    margin-right: 6.1%;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 24px;
    margin-left: 14.8%;
    margin-right: 6.1%;
  }
  .microgrid-dist,
  .microgrid-electric {
    width: 68.2%;
    height: 484px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use {
    width: 47%;
    height: 90%;
    float: left;
    margin-left: 2%;
    margin-top: 2%;
  }
  .energy-store-electric {
    width: 27.8%;
    height: 990px;
    float: right;
    margin-right: 1.4%;
    margin-top: -485px;
    background-color: #FFFFFF;
    border-radius: 4px;
  }
  #energy-store-electric-power {
    width: 90%;
    height: 450px;
    margin-top: 4%;
  }
  #charge-contrast {
    width: 90%;
    height: 450px;
    margin-top: 8%;
  }
}
@media screen and (min-width:1315px) and (max-width:1688px) {
  .electric-charge {
    width: 68.2%;
    height: 440px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left
  }
  span {
    display: inline-block;
  }
  .charge-ratio-left-title {
    margin-top: 77px;
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    float: left;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }
  .charge-ratio-left-num {
    float: left;
    margin-top: 60px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 30px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 135px;
    margin-left: 19px;
  }
  .charge-ratio-left-unit {
    float: left;
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }

  #power-generation-ratio,
  #power-use-ratio {
    width: 300px;
    height: 300px;
  }
  .electric-charge-content {
    display: flex;
    justify-content: space-around;
    width: 100%;
  }
  .safe-run-content {
    width: 27.8%;
    height: 84px;
    float: left;
    margin-left: 1%;
    margin-top: 20px;
    background-color: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
    width: 25%;
  }
  .safe-run-days-img>span {
    display: inline;
    margin-left: 1.3%;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 27.8%;
    height: 335px;
    float: left;
    margin-left: 1%;
    margin-top: 20px;
    background-color: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-data>div {
    width: 43.2%;
    height: 21.5%;
    line-height: 72px;
    background: #EEF1FA;
    border-radius: 4px;
    float: left;
    padding-left: 5px;
    display: flex;
    justify-content: space-around;
  }
  .safe-run-data>span {
    display: inline;
  }
  .system-frequency>span:last-child {
    margin-right: 6px;
  }
  .alternating-generatrix-voltage,
  .dc-generatrix-voltage,
  .system-frequency {
    margin-left: 5.4%;
  }
  .running-state,
  .dot-power {
    margin-left: 3.4%;
  }
  .alternating-generatrix-voltage,
  .running-state {
    margin-top: 40px;
  }
  .dc-generatrix-voltage,
  .dot-power,
  .system-frequency {
    margin-top: 18px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 20px;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 20px;
    margin-left: 14.8%;
  }
  .microgrid-dist,
  .microgrid-electric {
    width: 68.2%;
    height: 484px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use {
    width: 47%;
    height: 90%;
    float: left;
    margin-left: 2%;
    margin-top: 2%;
  }
  .energy-store-electric {
    width: 27.8%;
    height: 990px;
    float: right;
    margin-right: 1.4%;
    margin-top: -485px;
    background-color: #FFFFFF;
    border-radius: 4px;
  }
  #energy-store-electric-power {
    width: 90%;
    height: 450px;
    margin-top: 4%;
  }
  #charge-contrast {
    width: 90%;
    height: 450px;
    margin-top: 8%;
  }
}
@media screen and (min-width:878px) and (max-width:1314px) {
  .electric-charge {
    width: 96.8%;
    height: 440px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
  }
  span {
    display: inline-block;
  }
  .charge-ratio-left {
    margin-left: 5px;
  }
  .charge-ratio-left-title {
    margin-top: 77px;
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    float: left;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }
  .charge-ratio-left-num {
    float: left;
    margin-top: 60px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 28px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 100px;
    margin-left: 10px;
  }
  .charge-ratio-left-unit {
    float: left;
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }
  #power-generation-ratio,
  #power-use-ratio {
    width: 300px;
    height: 300px;
  }
  .electric-charge-content {
    display: flex;
    justify-content: space-around;
    width: 100%;
  }
  .safe-run-content {
    width: 96.8%;
    height: 84px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
  }
  .safe-run-days-img>span {
    display: inline;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 96.8%;
    height: 84px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
    display: flex;
    justify-content: space-around;
  }
  .safe-run-data>div {
    line-height: 72px;
    width: 19%;
    height: 72px;
    text-align: center;
    background: #EEF1FA;
    border-radius: 4px;
    margin-top: 5px;
  }
  .safe-run-data>div>span:first-child {
    margin-right: 5px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 22px;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 22px;
  }
  .microgrid-dist,
  .microgrid-electric,
  .energy-store-electric {
    width: 96.8%;
    height: 484px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use,
  #energy-store-electric-power,
  #charge-contrast {
    width: 45%;
    height: 90%;
    float: left;
    margin-left: 5%;
    margin-top: 2%;
  }
}
@media screen and (min-width:557px) and (max-width:877px) {
  .electric-charge {
    width: 96.8%;
    height: 1120px;
    border-radius: 4px;
    background-color: #FFFFFF;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    min-width: 350px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 40px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 135px;
    margin-left: 19px;
  }
  .charge-ratio-left-title {
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    margin-top: 77px;
  }
  .charge-ratio-left {
    height: 300px;
    margin: 0 auto;
    text-align: center;
    display: flex;
    justify-content: space-around;
  }
  .charge-ratio-middle {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .charge-ratio-right {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }

  .charge-ratio-left-num {
    margin-top: 60px;
  }

  .charge-ratio-left-unit {
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }
  #power-generation-ratio,
  #power-use-ratio {
    width: 350px;
    height: 350px;
    margin: 0 auto;
  }
  .safe-run-content {
    width: 96.8%;
    height: 84px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
  }
  .safe-run-days-img>span {
    display: inline;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 96.8%;
    height: 164px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-data>div {
    float: left;
    line-height: 72px;
    width: 30%;
    height: 72px;
    margin-top: 6px;
    text-align: center;
    background: #EEF1FA;
    margin-left: 2.5%;
    border-radius: 4px;
  }
  .safe-run-data>div>span:first-child {
    margin-right: 5px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 22px;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 22px;
  }
  .microgrid-dist,
  .microgrid-electric,
  .energy-store-electric {
    width: 96.8%;
    height: 840px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use,
  #energy-store-electric-power,
  #charge-contrast {
    width: 90%;
    height: 400px;
    float: left;
    margin-left: 5%;
    margin-top: 2%;
  }
}
@media screen and (min-width:367px) and (max-width:556px) {
  .electric-charge {
    width: 96.8%;
    height: 1120px;
    border-radius: 4px;
    background-color: #FFFFFF;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    min-width: 350px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 40px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 135px;
    margin-left: 19px;
  }
  .charge-ratio-left-title {
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    margin-top: 77px;
  }
  .charge-ratio-left {
    height: 300px;
    margin: 0 auto;
    text-align: center;
    display: flex;
    justify-content: space-around;
  }
  .charge-ratio-middle {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .charge-ratio-right {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }

  .charge-ratio-left-num {
    margin-top: 60px;
  }

  .charge-ratio-left-unit {
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }
  #power-generation-ratio,
  #power-use-ratio {
    width: 350px;
    height: 350px;
    margin: 0 auto;
  }
  .safe-run-content {
    width: 96.8%;
    height: 84px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
  }
  .safe-run-days-img>span {
    display: inline;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 96.8%;
    height: 240px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-data>div {
    float: left;
    line-height: 72px;
    width: 46%;
    height: 72px;
    margin-top: 6px;
    text-align: center;
    background: #EEF1FA;
    margin-left: 3%;
    border-radius: 4px;
  }
  .safe-run-data>div>span:first-child {
    margin-right: 5px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 22px;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 22px;
  }
  .microgrid-dist,
  .microgrid-electric,
  .energy-store-electric {
    width: 96.8%;
    height: 840px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use,
  #energy-store-electric-power,
  #charge-contrast {
    width: 90%;
    height: 400px;
    float: left;
    margin-left: 5%;
    margin-top: 2%;
  }
}
@media screen and (max-width:366px) {
  .electric-charge {
    width: 96.8%;
    height: 1120px;
    border-radius: 4px;
    background-color: #FFFFFF;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    min-width: 350px;
  }
  .total-electrical-output-num,
  .total-electrical-use-num,
  .energy-storage-num {
    margin-bottom: 22px;
    font-size: 40px;
    line-height: 60px;
    color: #52AFEE;
    border-bottom: 1px solid #ECECEC;
    width: 135px;
    margin-left: 19px;
  }
  .charge-ratio-left-title {
    font-size: 16px;
    width: 80px;
    line-height: 22px;
    color: #333333;
    margin-top: 77px;
  }
  .charge-ratio-left {
    height: 300px;
    margin: 0 auto;
    text-align: center;
    display: flex;
    justify-content: space-around;
  }
  .charge-ratio-middle {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .charge-ratio-right {
    height: 400px;
    margin: 0 auto;
    text-align: center;
  }
  .total-electrical-output-title,
  .total-electrical-use-title {
    margin-bottom: 67px;
  }

  .charge-ratio-left-num {
    margin-top: 60px;
  }

  .charge-ratio-left-unit {
    margin-top: 89px;
    width: 35px;
    margin-left: 9px;
  }
  .total-electrical-output-unit,
  .total-electrical-use-unit,
  .energy-storage-unit {
    font-size: 16px;
    line-height: 22px;
    margin-bottom: 66px;
    color: #666666;
  }
  #power-generation-ratio,
  #power-use-ratio {
    width: 350px;
    height: 350px;
    margin: 0 auto;
  }
  .safe-run-content {
    width: 96.8%;
    height: 84px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-days {
    display: flex;
    justify-content: space-around;
  }
  .safe-run-days>div {
    line-height: 84px;
  }
  .safe-run-days-img {
    font-size: 16px;
  }
  .safe-run-days-img>span {
    display: inline;
  }
  .safe-run-days-count {
    color: #666666;
    font-size: 36px;
  }
  .safe-run-days-unit {
    margin-right: 8.8%;
    font-size: 16px;
  }
  .safe-run-data {
    width: 96.8%;
    height: 400px;
    margin-left: 1.6%;
    margin-right: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    border-left: 10px solid #63A7E8;
  }
  .safe-run-data>div {
    float: left;
    line-height: 72px;
    width: 90%;
    height: 72px;
    margin-top: 6px;
    text-align: center;
    background: #EEF1FA;
    margin-left: 5%;
    border-radius: 4px;
  }
  .safe-run-data>div>span:first-child {
    margin-right: 5px;
  }
  .alternating-generatrix-voltage-data,
  .dc-generatrix-voltage-data,
  .dot-power-data {
    color: #52AFEE;
    font-size: 22px;
  }
  .system-frequency-data {
    color: #DB0822;
    font-size: 22px;
  }
  .microgrid-dist,
  .microgrid-electric,
  .energy-store-electric {
    width: 96.8%;
    height: 840px;
    margin-left: 1.6%;
    margin-top: 20px;
    background: #FFFFFF;
    border-radius: 4px;
    float: left;
  }
  #microgrid-elec-dist,
  #microgrid-cost-dist,
  #pvp-generation,
  #microgrid-electric-use,
  #energy-store-electric-power,
  #charge-contrast {
    width: 90%;
    height: 400px;
    float: left;
    margin-left: 5%;
    margin-top: 2%;
  }
}
.electric-charge-date {
  width: 220px;
  height: 34px;
  position: relative;
  left: 28px;
  top: 14px;
  border-radius: 2px;
  border: 1px solid #E3E3E3;
}
hr {
  margin-top: 30px;
  margin-left: 1.6%;
  margin-right: 3.4%;
}
.electric-charge,
.safe-run-days,
.safe-run-data,
.microgrid-dist,
.microgrid-electric,
.energy-store-electric {
  box-shadow: 2px 2px 4px rgba(188,182,182,0.5);
}
</style>
