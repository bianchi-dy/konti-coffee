<template>
<div>
  <!-- sticky legend desktop -->
  <div class="map__legend mapboxgl-custom-legend hidden-sm-and-down" 
    v-if="step > 6">
    <v-flex xs12 class="pa-2 mb-3 map__legend-first">
      <div class="legend-container">
        <h4 class="caption font-weight-bold">Total persons</h4>
        <v-layout class="legend-pop mt-2">
          <v-flex xs6 lg11 align-self-center>
            <div class="legend-pop__label">
              <div class="dotted-line" />
              <div class="label__inner"/>
              <div class="dotted-line2" />
            </div>
          </v-flex>

          <v-flex xs5 offset-xs1>
            <v-layout wrap fill-height>
              <v-flex xs12 class="caption">350</v-flex>
              <v-flex xs12 align-self-end mb-2 class="caption">30</v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </div>
    </v-flex>

    <v-flex xs12 align-left class="pa-2 map__legend-second">
      <p class="caption mb-2"><strong>Types of incidents</strong></p>                      
      <v-flex xs12 v-for="(item, i) in incidents" :key="i">
        <v-layout>
          <v-flex xs1 class="mr-2"><span class="dot" :style="{'background-color': `${item.color}`}"/></v-flex>
          <v-flex xs10><p class="caption mb-2">{{ item.type }}</p></v-flex>
        </v-layout>                                              
      </v-flex>
    </v-flex>
  </div>
</div>
</template>

<script>
export default {
  name: 'Legend',
  props: {
    step: {
      type: Number,
      required: true,
    },
  },  
  data() {
    return {
      incidents: [
        {type: 'Disembarkation or rescue', color: '#E04842'},
        {type: 'Returned due to engine failure ', color: '#9D2F90'},
        {type: 'Intercepted or pushed back by authorities ', color: '#EBC119'},
        {type: 'Washed ashore, capsized or drowned ', color: '#62A6F4'},
        {type: 'Abandoned by smugglers', color: '#41BE64'},
      ]
    }
  }
};
</script>

<style lang="stylus" scoped>
.dot {
  height: 10px;
  width: 10px;
  margin-right: 8px;
  border-radius: 50%;
  display: inline-block;
}

.mapboxgl-custom-legend {
  position: absolute;
  z-index: 99;
  right: 24px;
  bottom: 30px;
  width: 150px;
}

.map__legend-first, .map__legend-second {
  background-color: rgba(26, 26, 26, 0.9);
  -webkit-backdrop-filter: blur(20px);
  backdrop-filter: blur(20px);   
}

.map__legend {
  box-shadow: 0px 1px 3px rgba(0, 0, 0, 0.14);
  border-radius: 4px;

  p.caption {
    color: #D1D1D1 !important;
  }

  .legend-container {
    color: #959595 !important;
    div.caption, {
      color: #959595 !important;
    }
    h4.caption {
      color: #D1D1D1 !important;
    }

    .legend-pop {

      .legend-pop__label {
        position: relative;
        border: 1px solid #959595;
        border-radius: 50%;
        width: 100%;

        &:before {
          content: '';
          display: block;
          padding-top: 100%;
        }

        .dotted-line {
          position: absolute;
          top: 0;
          left: 50%;
          width: 50%;
          height: 1px;
          border-top: 1px dotted #959595;
        }

        .dotted-line2 {
          position: absolute;
          bottom: 25%;
          left: 50%;
          width: 50%;
          height: 1px;
          border-top: 1px dotted #959595;
        }

        .label__inner {
          position: absolute;
          bottom: 0;
          left: 50%;
          transform: translateX(-50%);
          border: 1px solid #959595;
          border-radius: 50%;
          width: 25%;
          height: 25%;
        }
      }
    }
  }
}
</style>
