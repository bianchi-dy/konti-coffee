<template>
  <div class="post__container">    
    <div id="map"/>
  </div>
</template>

<script>
import * as d3 from 'd3';

export default {
  data() {
    return {
    };
  },
  computed: {
    mapInit() {
      if (this.$vuetify.breakpoint.smAndDown) {
        return {
          center: [93.11, 20.3],
          zoom: 6.2,
          style: 'mapbox://styles/kontinentalist/ckggdy1dx0j4w19obbjsuv788',
          bearing: 12.8, 
          pitch: 60,
          popupPos: 'bottom'
        };        
      }      
      return {
        center: [93.11, 20.3],
        zoom: 7,
        style: 'mapbox://styles/kontinentalist/ckggdy1dx0j4w19obbjsuv788',
        bearing: 15, 
        pitch: 60,
        popupPos: 'left'
      };
    },
  },
  async mounted() {
    this.$Mapbox.load().then(() => {
      // initialising map
      this.map = this.$Mapbox.constructMap({
        container: 'map',
        style: this.mapInit.style,
        center: this.mapInit.center,
        zoom: this.mapInit.zoom,
        bearing: this.mapInit.bearing,
        pitch: this.mapInit.pitch,
        maxZoom: this.mapInit.maxZoom,
        interactive: false,
      });

      // loading layers
      this.map.on('load', () => {
        this.loadIncidents();
      });
    });
  },
  methods: {
    loadIncidents() {
      // add sources
      this.map.addSource('incidents', {
        type: 'geojson',
        data: '/data/unhcr/geojson/incidents.geojson',
      });

      // add layer
      this.map.addLayer({
        id: 'incident-points',
        type: 'circle',
        source: 'incidents',
        paint: {
          'circle-opacity': 0.8,
          'circle-stroke-color': '#B0B0B0',
          'circle-stroke-width': 1,
          'circle-radius': [
            'round',[
            '/',
            ['sqrt', ['get', 'total']],
            0.8
            ],
          ],
        },
        layout: {
          'visibility': 'visible',
        }
      });      
      this.map.setPaintProperty('incident-points', 'circle-color', [
        'match',
        ['get', 'incident'],
        'disembarkation after failed voyage',
        '#E04842',
        'disembarkation',
        '#E04842',
        'rescue',
        '#E04842',        
        'returned due to engine failure', 
        '#9D2F90',
        'pushback by authorities',
        '#EBC119',        
        'interception',
        '#EBC119',
        'washed ashore',
        '#62A6F4',
        'capsized',
        '#62A6F4',
        'drowned body recovered',
        '#62A6F4',
        'abandoned by smugglers',
        '#41BE64',
        '#F2F2F2',
      ]);
    },   
    handleMapPopup(e) {
      this.popUp.remove();
      this.map.getCanvas().style.cursor = 'pointer';
      const coordinates = e.features[0].geometry.coordinates.slice();
      const propData = e.features[0].properties
      // popup box
      this.popUp
        .setLngLat(coordinates)
        .setHTML(`
          <p class="caption mb-1">
            <strong>Location:</strong> ${propData.location}
          </p>
          <p class="caption mb-1">
            <strong>Date of incident:</strong> ${propData.date}
          </p> 
          <p class="caption mb-1">
            <strong>Type of incident:</strong> 
            ${propData.incident.charAt(0).toUpperCase() + propData.incident.slice(1)}
          </p> 
          <p class="caption mb-1">
            <strong>Total persons:</strong> ${propData.total}
          </p> 
          <p class="caption mb-1">
            <strong>Total survivors:</strong> ${propData.survivors}
          </p>           
          <p class="caption mb-0">
            <strong>Total death:</strong> ${+propData.total - +propData.survivors}</br>
            ${propData.comment ? propData.comment : ''} 
          </p>                                        
        `)
        .addTo(this.map);
    },
    handleMapInteractivity(){
      // enable dragPan
      this.map.dragPan.enable();

      // cursor drag, dragging
      d3.select(this.map.getCanvas().parentElement).classed('mapboxgl-interactive', true);

      this.map.setMaxZoom(12);
      
      // initialise popup 
      this.popUp = this.$Mapbox
        .popup({closeButton: false, anchor: this.mapInit.popupPos, className: 'incident-tooltip'});

      // Change cursor when hover
      this.map.on('click', 'incident-points', this.handlePopupZoom);
      this.map.on('mousemove', 'incident-points', this.handleMapPopup);
      this.map.on('mouseleave', 'incident-points', this.handleLayerExit);

      if (this.$vuetify.breakpoint.smAndDown) { 
        this.map.off('mousemove', 'incident-points', this.handleMapPopup);
        this.map.off('mouseleave', 'incident-points', this.handleLayerExit);          
        this.map.on('click', 'incident-points', this.handleMapPopup);
        this.map.dragPan.disable();
      }               
    },    
    handleLayerExit() {
      this.map.getCanvas().style.cursor = '';
      this.popUp.remove();
    },      
  },
};
</script>

<style lang="stylus" scoped>
// CSS for all text in SVGs
@import '~vuetify/src/stylus/settings/_variables';
.post__container {
  z-index: 1;
  width: 100%;
  height: 100%;
  position: relative;

  #map {
    width: 100%;
    height: calc(100vh - 64px);
  }
}

>>>.incident-tooltip {
  @media $display-breakpoints.sm-and-down {
    max-width: 300px !important;
  }
  max-width: 280px !important;

  .mapboxgl-popup-content {
    box-shadow: 0px 6px 30px rgba(0, 0, 0, 0.12);
    border-radius: 4px;
    font-family: 'LL Akkurat Pro', Helvetica, sans-serif;
    padding: 8px;
    background-color: #1A1A1A;
    p.caption {
      color: #D1D1D1 !important;
    }    
  }

  .mapboxgl-popup-tip {
    border-right-color: #1A1A1A;
    @media $display-breakpoints.sm-and-down {
      border-top-color: #1A1A1A;
      border-right-color: transparent;
    }    
  }
}

.location-reset {
  right: 8px;
  height: 40px !important;  
  top: 18px;
  position: absolute;
}
</style>
