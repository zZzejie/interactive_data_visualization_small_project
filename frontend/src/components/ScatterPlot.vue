<template>
  <div>
    <v-row align="center" justify="center" class="mt-1 mb-0">
      <h3>Overview of {{ $props.selectedCategory }} Companies</h3>
    </v-row>
    <div style="height: 90vh">
      <div id='myScatterPlot' style="height: inherit"></div>
    </div>
  </div>
</template>




<script>
import Plotly from 'plotly.js/dist/plotly';
export default {
  name: "ScatterPlot",
  props: [
    "selectedCategory"
  ],
  data: () => ({
    ScatterPlotData: {x: [], y: [], name: [], color: []},
    categoryColors: { // Define colors for each category
      bank: '#3498db',   // blue
      tech: '#2ecc71',   // green
      health: '#e74c3c'  // red
    }
  }),
  mounted() {
    this.fetchData()
  },
  methods: {
    async fetchData() {
      // req URL to retrieve companies from backend
      var reqUrl = 'http://127.0.0.1:5000/companies?category=' + this.$props.selectedCategory
      console.log('ReqURL ' + reqUrl)
      // await response and data
      const response = await fetch(reqUrl)
      const responseData = await response.json();

      responseData.forEach((company) => {
        const categoryColor = this.categoryColors[company.category] || '#000000'; // Get the color for the category or black if not defined
        this.ScatterPlotData.name.push(company.name);
        this.ScatterPlotData.x.push(company.founding_year);
        this.ScatterPlotData.y.push(company.employees);
        this.ScatterPlotData.color.push(categoryColor);
      });
      this.drawScatterPlot()
    },
    drawScatterPlot() {
      var trace1 = {
        x: this.ScatterPlotData.x,
        y: this.ScatterPlotData.y,
        mode: 'markers',
        type: 'scatter',
        text: this.ScatterPlotData.name,
        marker: {
          // Add a marker that sets the color for each company based on the category color
          color: this.ScatterPlotData.color,
          size: 12
        },
        legendgroup: 'Companies',
        name: 'Companies'
      };
      var data = [trace1];
      var layout = {
        // Add axis titles
        xaxis: {
          title: 'Founding Years',
          showgrid: false,
          zeroline: false
        },
        yaxis: {
          title: 'Employees',
          showline: false
        },
        legend: {
          tracegroupgap: 10,  // Gap between legend entries
          itemsizing: 'constant'
        }
      };
      var config = {responsive: true, displayModeBar: false}
      Plotly.newPlot('myScatterPlot', data, layout, config);
      this.clickScatterPlot()
    },

    clickScatterPlot() {
      var that = this;
      var myPlot = document.getElementById('myScatterPlot');
      var originalColors = [...this.ScatterPlotData.color]; // Store original colors

      myPlot.on('plotly_click', function (data) {
        var clickedIndices = data.points.map(point => point.pointNumber);

        for (var i = 0; i < data.points.length; i++) {
          // Get the index of point and retrieve the associated company name
          var pn = data.points[i].pointNumber;
          var companyName = that.ScatterPlotData.name[pn];

          // Emitting an event to change the currently selected company in the configuration panel
          that.$emit('changeCurrentlySelectedCompany', companyName);

          var colors = originalColors.map((color, index) => {
            return clickedIndices.includes(index) ? color : '#000000';
          });

          var update = {'marker': { color: colors, size: 12 }};
          Plotly.restyle('myScatterPlot', update);
        }
      });
    }


  },
  // "watch" the selectedCategory prop for changes and, when it changes, re-fetch the data and redraw the scatterplot
  watch: {
    selectedCategory: function () {
      this.ScatterPlotData.x = [];
      this.ScatterPlotData.y = [];
      this.ScatterPlotData.name = [];
      this.ScatterPlotData.color = [];
      this.fetchData();
    }
  }
}
</script>




<style scoped>

</style>