<template>
  <div>
    <v-row align="center" justify="center" class="mt-1 mb-0">
      <h3>Profit View of Company: {{ $props.selectedCompany }}</h3>
    </v-row>
    <div style="height: 90vh">
      <div id='myLinePlot' style="height: inherit"></div>
    </div>
  </div>
</template>




<script>
import Plotly from 'plotly.js/dist/plotly';
export default {
  name: "LinePlot",
  props: ["selectedCompany", "selectedAlgorithm"],
  data: () => ({
    LinePlotData: {x: [], y: [], color: [], symbol: []},
  }),
  mounted() {
    this.fetchData()
  },
  methods: {
    async fetchData() {
      // req URL to retrieve single company from backend
      var reqUrl = 'http://127.0.0.1:5000/companies/' + this.$props.selectedCompany +
          '?algorithm=' + this.$props.selectedAlgorithm
      console.log("ReqURL " + reqUrl)

      // await response and data
      const response = await fetch(reqUrl)
      const responseData = await response.json();

      // transform data to usable by lineplot
      responseData.profit.forEach((profit) => {
        this.LinePlotData.x.push(profit.year)
        this.LinePlotData.y.push(profit.value)
      })

      // assign color and symbol based on year
      this.LinePlotData.x.forEach((year) => {
        switch (year) {
          case 2022:
            this.LinePlotData.color.push("#DC4C64")
            this.LinePlotData.symbol.push("star-diamond")
            break;
          default:
            this.LinePlotData.color.push("#3B71CAFF")
            this.LinePlotData.symbol.push("circle")
        }
      })

      // draw the lineplot after the data is transformed
      this.drawLinePlot()
    },

    refetchData() {
      this.LinePlotData.x = [];
      this.LinePlotData.y = [];
      this.LinePlotData.color = [];
      this.LinePlotData.symbol = [];
      this.fetchData();
    },

    drawLinePlot() {
      var trace = {
        x: this.LinePlotData.x,
        y: this.LinePlotData.y,
        type: 'scatter',
        mode: 'lines+markers',
        marker:
            {
              color: this.LinePlotData.color,
              symbol: this.LinePlotData.symbol,
              size: 10
            },
        hovertemplate: (this.LinePlotData.x === 2022 ? "estimate": "") + "%{x}: %{y}"
      };

      var data = [trace];

      var layout = {
        // add axis titles
        xaxis: {
          title: 'Years',
          showgrid: false,
          zeroline: false
        },
        yaxis: {
          title: 'Profit',
          showline: false
        }
      };

      var config = { responsive: true, displayModeBar: false };
      Plotly.newPlot('myLinePlot', data, layout, config);
    }



  },
  watch: {
    selectedCompany() {
      this.LinePlotData.x = [];
      this.LinePlotData.y = [];

      this.fetchData();
    },
    selectedAlgorithm() {
      this.refetchData();
    },
  }
}
</script>


<style scoped>

</style>