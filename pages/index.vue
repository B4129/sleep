<template>
  <v-row>
    <v-col>
      <line-chart :chartData="chart" :options="chartOptions"></line-chart>
    </v-col>
  </v-row>
</template>

<script>
import LineChart from "~/components/LineChart.vue";
import moment from "moment";

export default {
  data() {
    return {
      chart: ""
    };
  },

  components: {
    LineChart
  },
  created() {
    this.chart = {
      //◆データ
      labels: this.thisMonthLabels, //ラベル名
      datasets: this.makeData,
      other: this.monthData.map(day => day.other)
    };
  },
  computed: {
    thisMonthLabels() {
      const month = moment().daysInMonth();
      return [...Array(month).keys()].map(i => ++i);
    },
    timeLabel() {
      return [
        { name: "朝", id: 0 },
        { name: "昼", id: 1 },
        { name: "夕方", id: 2 },
        { name: "夜", id: 3 }
      ];
    },
    chartOptions() {
      return {
        //◆オプション
        responsive: true, //グラフ自動設定
        legend: {
          //凡例設定
          display: true, //表示設定
          labels: {
              fontSize:18,
            generateLabels: function(data) {
              return [
                {
                  text: "データなし",
                },
                {
                  text: "限界",
                  fillStyle: "#FF0000cc"
                },
                {
                  text: "不調",
                  fillStyle: "#FF9900cc"
                },
                {
                  text: "普通",
                  fillStyle: "#FFFF00cc"
                },
                {
                  text: "良好",
                  fillStyle: "#00FF00cc"
                },
                {
                  text: "覚醒",
                  fillStyle: "#0000FFcc"
                }
              ];
            }
          }
        },
        tooltips: {
          enabled: true,
          callbacks: {
            title: function(tooltipItems, data) {
              return data.other[tooltipItems[0].index];
            }
          }
        },
        title: {
          //タイトル設定
          display: true, //表示設定
          fontSize: 18, //フォントサイズ
          text: "" //ラベル
        },
        scales: {
          //軸設定
          yAxes: [
            {
              //y軸設定
              stacked: true,
              display: true, //表示設定
              scaleLabel: {
                //軸ラベル設定
                display: true, //表示設定
                labelString: "2月", //ラベル
                fontSize: 18 //フォントサイズ
              },
              ticks: {
                //最大値最小値設定
                fontSize: 18, //フォントサイズ
                stepSize: 5 //軸間隔
              }
            }
          ],
          xAxes: [
            {
              //x軸設定
              display: true, //表示設定
              barPercentage: 0.4, //棒グラフ幅
              categoryPercentage: 0.4, //棒グラフ幅
              scaleLabel: {
                //軸ラベル設定
                display: true, //表示設定
                labelString: "時間(8～24時)", //ラベル
                fontSize: 18 //フォントサイズ
              },
              ticks: {
                fontSize: 18, //フォントサイズ
                min: this.MIN_X_AXES_VALUE,
                max: this.MAX_X_AXES_VALUE
              }
            }
          ]
        },
        layout: {
          //レイアウト
          padding: {
            //余白設定
            left: 100,
            right: 50,
            top: 0,
            bottom: 0
          }
        }
      };
    },
    makeData() {
      const labels = this.timeLabel;
      return labels.map((label, index) => {
        return {
          label: label.name,
          borderWidth: 1,
          data: this.monthData.map(_ => {
            return (index + 1) * this.scale + this.MIN_X_AXES_VALUE;
          }),
          backgroundColor: this.monthData.map(
            data => this.calculationColor[data.stats[label.id]]
          )
        };
      });
    },
    calculationColor() {
      return {
        1: "#FF0000cc",
        2: "#FF9900cc",
        3: "#FFFF00cc",
        4: "#00FF00cc",
        5: "#0000FFcc"
      };
    },
    monthData() {
      return [
        { day: 1, stats: [], other: "" },
        { day: 2, stats: [], other: "" },
        { day: 3, stats: [], other: "" },
        { day: 4, stats: [], other: "" },
        { day: 5, stats: [], other: "" },
        { day: 6, stats: [], other: "" },
        { day: 7, stats: [], other: "" },
        { day: 8, stats: [], other: "" },
        { day: 9, stats: [], other: "" },
        { day: 10, stats: [], other: "" },
        { day: 11, stats: [], other: "" },
        { day: 12, stats: [], other: "" },
        { day: 13, stats: [], other: "" },
        { day: 14, stats: [], other: "" },
        { day: 15, stats: [], other: "" },
        { day: 16, stats: [], other: "" },
        { day: 17, stats: [], other: "" },
        { day: 18, stats: [], other: "" },
        { day: 19, stats: [], other: "" },
        { day: 20, stats: [2, 2, 1, 1], other: "" },
        { day: 21, stats: [2, 2, 2, 2], other: "" },
        { day: 22, stats: [5, 5, 5, 5], other: "休日" },
        { day: 23, stats: [5, 5, 5, 4], other: "休日" },
        { day: 24, stats: [5, 5, 4, 4], other: "休日" },
        { day: 25, stats: [1, 1, 1, 1], other: "24日02:30就寝" },
        { day: 26, stats: [1, 3, 1, 1], other: "サプリメント服用" },
        { day: 27, stats: [1,1,2,1], other: "" },
        { day: 28, stats: [], other: "" },
        { day: 29, stats: [], other: "" }
      ];
    },
    MIN_X_AXES_VALUE: () => 8,
    MAX_X_AXES_VALUE: () => 24,
    scale() {
      return (
        (this.MAX_X_AXES_VALUE - this.MIN_X_AXES_VALUE) / this.timeLabel.length
      );
    }
  }
};
</script>
