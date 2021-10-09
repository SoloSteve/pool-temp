<template>
  <v-container
      fill-height
      fluid
      grid-list-xl
  >
    <v-layout wrap>
      <v-flex
          sm6
          xs12
          md6
          lg3
      >
        <material-stats-card
            color="info"
            icon="mdi-water"
            title="Pool Temperature"
            :value="currentPoolTemperature"
            sub-icon="mdi-update"
            :sub-text="'Updated ' + updateDelta"
        />
      </v-flex>
      <v-flex
          sm6
          xs12
          md6
          lg3
      >
        <material-stats-card
            color="info"
            icon="mdi-water-pump"
            title="Pump Room Temperature"
            :value="currentPumpTemperature"
            sub-icon="mdi-update"
            :sub-text="'Updated ' + updateDelta"
        />
      </v-flex>
      <v-flex
          sm6
          xs12
          md6
          lg3
      >
        <material-stats-card
            color="info"
            icon="mdi-chart-line"
            title="Pool Temperature Growth"
            :value="growth + '°C / hour'"
            sub-icon="mdi-update"
            :sub-text="'Updated ' + updateDelta"
        />
      </v-flex>
      <v-flex
          md12
          sm12
          lg4
      >
        <material-chart-card
            :data="dataCompletedTasksChart.data"
            :options="dataCompletedTasksChart.options"
            color="green"
            type="Line"
        >
          <h3 class="title font-weight-light">Pool Temperature History</h3>
          <p class="category d-inline-flex font-weight-light">going back 48 hours</p>

          <template slot="actions">
            <v-icon
                class="mr-2"
                small
            >
              mdi-clock-outline
            </v-icon>
            <span class="caption grey--text font-weight-light">last updated 2 seconds ago</span>
          </template>
        </material-chart-card>
      </v-flex>

    </v-layout>
  </v-container>
</template>

<script>
  import moment from "moment";
  export default {
    data() {
      return {
        currentPoolTemperature: "loading",
        currentPumpTemperature: "loading",
        growth: "loading",
        updateDelta: "",
        updateTime: "",
        dataCompletedTasksChart: {
          data: {
            labels: ['12am', '3pm', '6pm', '9pm', '12pm', '3am', '6am', '9am'],
            series: [
              [230, 750, 450, 300, 280, 240, 200, 190]
            ]
          },
          options: {
            lineSmooth: this.$chartist.Interpolation.cardinal({
              tension: 0
            }),
            low: 0,
            high: 1000, // creative tim: we recommend you to set the high sa the biggest value + something for a better look
            chartPadding: {
              top: 0,
              right: 0,
              bottom: 0,
              left: 0
            }
          }
        }
      }
    },
    methods: {

    },
    mounted() {
      setInterval(async () => {
        const result = await this.$http.get("/data");
        this.currentPoolTemperature = result.data["sensors"]["40:170:188:49:64:20:1:156"].toString();
        this.currentPumpTemperature = result.data["sensors"]["40:170:73:65:64:20:1:64"].toString();
        this.growth = result.data["growth"]["value"].toString();
        this.updateTime  = result.data["time"];
      }, 2000);

      setInterval(() => {
        this.updateDelta = moment.unix(this.updateTime.unix - 2).fromNow()
      }, 100)
    }
  }
</script>

<style>
  .title.display-1.font-weight-light {
    font-size: 50px !important;
  }
</style>
