<template>
  <div class="mainContainer">
    <v-container fluid class="controlPanelContainer">
      <v-row>
        <v-col cols="12" md="2" class="sideBar">
          <v-row>
            <v-col cols="12" sm="12">
              <div class="control-panel-font"><v-icon>mdi-domain</v-icon> <p style="padding-left: 8px">Company Overview</p></div>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-select
                  :items="categories.values"
                  label="Select a category"
                  dense
                  v-model="categories.selectedValue"
                  @change="changeCategory"
                  class="mySelect"
                  color="rgba(20, 164, 77, 1)"
              ></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <div class="control-panel-font"><v-icon>mdi-chart-line</v-icon> <p style="padding-left: 8px">Profit View of Company</p></div>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-select
                  :items="companies.values"
                  label="Select a company"
                  dense
                  v-model="companies.selectedValue"
                  @change="changeCompany"
                  color="rgba(20, 164, 77, 1)"
              ></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-select
                  :items="algorithm.values"
                  label="Select an algorithm"
                  dense
                  v-model="algorithm.selectedValue"
                  @change="changeAlgorithm"
                  color="rgba(20, 164, 77, 1)"
              ></v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <div class="control-panel-font"><v-icon>mdi-account-group</v-icon> <p style="padding-left: 8px">Size View of Company</p></div>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" sm="12">
              <v-select
                  :items="categories.values"
                  label="Select a category"
                  dense
                  v-model="categories.selectedValue"
                  @change="changeCategory"
                  color="rgba(20, 164, 77, 1)"
              ></v-select>
            </v-col>
          </v-row>
        </v-col>

        <v-col cols="12" md="5">
          <!-- Allow an emitted event from the child component (Company Overview) to the parent component (Configuration Panel).-->
          <ScatterPlot :key="scatterPlotId"
                       :selectedCategory="categories.selectedValue"
                       @changeCurrentlySelectedCompany="changeCurrentlySelectedCompany"
          />
          <BarChart :key="barChartId"
                    :selectedCategory="categories.selectedValue"
                    :selectedCompany="companies.selectedValue"
                    :averageEmployeeCount="averageEmployeeCount"
                    @changeAverageEmployeeCount="changeAverageEmployeeCount"
                    @changeCurrentlySelectedCompany="changeCurrentlySelectedCompany"
          />
        </v-col>
        <v-col cols="12" md="5">
          <LinePlot :key="linePlotId"
                    :selectedCompany="companies.selectedValue"
                    :selectedAlgorithm="algorithm.selectedValue"/>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>
<script>
import ScatterPlot from './ScatterPlot';
import LinePlot from './LinePlot';
import BarChart from './BarChart';

export default {
  components: {ScatterPlot, LinePlot, BarChart},
  data: () => ({
    scatterPlotId: 0,
    linePlotId: 0,
    barChartId: 0,
    averageEmployeeCount: 0,

    categories: {
      values: ['All', 'tech', 'health', 'bank'],
      selectedValue: 'All'
    },
    companies: {
      values: ['alphabet', 'apple', 'amazon', 'microsoft', 'meta', 'united health', 'johnson and johnson', 'pfizer', 'cvs health', 'mckesson', 'ubs', 'credit suisse', 'jp morgan', 'goldman sachs', 'bank of america'],
      selectedValue: 'alphabet'
    },
    algorithm: {
      values: ['none', 'random', 'regression'],
      selectedValue: 'none'
    },
  }),
  methods: {
    changeCategory() {
      this.scatterPlotId += 1
      this.barChartId += 1
    },
    changeCompany() {
      this.linePlotId += 1
    },
    changeAlgorithm() {
      this.linePlotId += 1
    },
    changeCurrentlySelectedCompany(companyName) {
      this.companies.selectedValue = companyName
      this.changeCompany()
    },
    changeAverageEmployeeCount(averageEmployeeCount) {
      this.averageEmployeeCount = averageEmployeeCount
    }
  },

  }
</script>


<style scoped>
.mainContainer {
  background: rgba(20, 164, 77, .05);
}

.control-panel-font {
  font-family: "Open Sans", verdana, arial, sans-serif;
  align-items: center;
  font-size: 15px;
  border-bottom: 1px solid rgba(20, 164, 77, .75);
  display: flex;
  font-weight: 500;
  height: 40px;
}
.sideBar {
  border-right: 1px solid rgba(20, 164, 77, .75);
  background: rgba(20, 164, 77, .05);
  padding-left: 17px;
  height: calc(100vh - 50px);
}
</style>
