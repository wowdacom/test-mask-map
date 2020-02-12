<template>

  <div style="height: 500px; width: 100%">
    <div style="height: 200px overflow: auto;">
      <p>First marker is placed at {{ withPopup.lat }}, {{ withPopup.lng }}</p>
      <p>Center is at {{ currentCenter }} and the zoom is: {{ currentZoom }}</p>
      <button @click="showLongText">
        Toggle long popup
      </button>
      <button @click="showMap = !showMap">
        Toggle map
      </button>
    </div>
    <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      style="height: 80%"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
      <l-control-layers></l-control-layers>
      <l-layer-group 
          layerType="overlay"
          name="Sources">
          <l-marker
            v-for="item in info"
            :key="item.properties.id"
            :lat-lng="[item.geometry.coordinates[1], item.geometry.coordinates[0]]">
          </l-marker>
      </l-layer-group>

    </l-map>
  </div>
</template>

<script>
import axios from 'axios'
import { latLng } from "leaflet";
import { LMap, LControlLayers, LTileLayer, LLayerGroup, LMarker, LPopup, LTooltip } from "vue2-leaflet";

export default {
  name: "Example",
  components: {
    LMap,
    LTileLayer,
    LLayerGroup,
    LControlLayers,
    LMarker,
    LPopup,
    LTooltip
  },
  data() {
    return {
      zoom: 16,
      center: latLng(25.0610406,121.6384072),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      withPopup: latLng(25.0610406,121.6384072),
      withTooltip: latLng(25.0610406,121.6384072),
      currentZoom: 18,
      currentCenter: latLng(25.0610406,121.6384072),
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5
      },
      showMap: true,
      info: []
    };
  },
  mounted () {
    axios
      .get('https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json?fbclid=IwAR0dXW-tt7CbdYkC_QTCfP5oIEmjdtLWfeFQnFkt_u7QUoyTGh40oy1ZLBc')
      .then((res) => { this.info = res.data.features; console.log(res) })
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    showLongText() {
      this.showParagraph = !this.showParagraph;
    },
    innerClick() {
      alert("Click!");
    }
  }
};
</script>
