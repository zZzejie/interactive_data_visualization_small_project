<template>
  <div>
    <v-row align="center" justify="center" class="mt-1 mb-0">
      <h3>Size Comparison in {{ $props.selectedCategory }} Companies</h3>
    </v-row>
    <div style="height: 90vh">
      <div id='myBarChart' style="height: inherit"></div>
    </div>
  </div>
</template>

<script>
import Plotly from 'plotly.js/dist/plotly';
export default {
  name: "BarChart",
  props: ["selectedCategory", "selectedCompany", "averageEmployeeCount"],
  data: () => ({
    BarChartData: {x: [], y: [], name: [], color: []},

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
        this.BarChartData.name.push(company.name);
        this.BarChartData.x.push(company.name);
        this.BarChartData.y.push(company.employees);
        this.BarChartData.color.push(categoryColor); // push the color to the color array for each company
      });
      this.drawBarChart()
    },
    drawBarChart() {
      var trace1 = {
        x: this.BarChartData.x,
        y: this.BarChartData.y,
        mode: 'markers',
        type: 'bar',
        marker: {
          color: this.BarChartData.color
        },

        legendgroup: 'Companies',
        name: "", // Remove the trace name

      };
      // Call a method after creating the plot which is responsible to handle the click events.
      var data = [trace1];
      var layout = {
        // Add axis titles
        xaxis: {
          title: 'Companies',
          showgrid: false,
          zeroline: false
        },
        yaxis: {
          title: 'Number of Employees',
          showline: false
        },
        showlegend:false,  // Hide the legend

        margin: {
          b: 180
        },
      };
      var config = {responsive: true, displayModeBar: false}
      Plotly.newPlot('myBarChart', data, layout, config);
      this.clickBarChart()
    },

    // Upon a click, retrieve the point number and then emit an event with (pn+1 = company id).
    // Emitting an event will notify the parent (configuration panel) that the selected company has changed.
    // Then, revert all colors to black in case of previous clicks and change the current selection to blue.
    // Then, call the Plotly. restyle function to update the plot.

    clickBarChart() {
      var that = this;
      var myPlot = document.getElementById('myBarChart');
      var originalColors = [...this.BarChartData.color]; // Store original colors

      myPlot.on('plotly_click', function (data) {
        var clickedIndices = data.points.map(point => point.pointNumber);

        for (var i = 0; i < data.points.length; i++) {
          var pn = data.points[i].pointNumber;
          // Extract necessary data for emitting the event, if needed
          var clickedBarData = that.BarChartData.name[pn];
          that.$emit('changeCurrentlySelectedBar', clickedBarData);
        }

        // Change all unclicked bars to black while keeping the clicked bar's color unchanged
        var colors = originalColors.map((color, index) => {
          return clickedIndices.includes(index) ? color : '#000000';
        });

        var update = { 'marker': { color: colors } };
        Plotly.restyle('myBarChart', update);
      });
    }


  },
  watch: {
    selectedCategory: function () {
      this.BarChartData.x = [];
      this.BarChartData.y = [];
      this.BarChartData.name = [];
      this.BarChartData.color = [];
      this.fetchData();
    }
  }
}
</script>


<style scoped>

</style>