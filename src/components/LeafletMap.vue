<template>
  <div style="height: 1000px; width: 100%" class="">
    <l-map
      style="height: 100%; width: 100%"
      ref="map"
      v-model:zoom="zoom"
      :center="center"
      measureControl="true"
      @ready="mapReady"
    >
      <l-geo-json
        v-if="show"
        :geojson="geojson"
        :options="options"
        :options-style="styleFunction"
      />
      <l-control-layers position="topright"></l-control-layers>
      <l-tile-layer
        v-for="tileProvider in tileProviders"
        :key="tileProvider.name"
        :name="tileProvider.name"
        :visible="tileProvider.visible"
        :url="tileProvider.url"
        :attribution="tileProvider.attribution"
        layer-type="base"
      />
      <l-control-scale
        size="50px"
        position="bottomright"
        :imperial="true"
        :metric="false"
      ></l-control-scale>
      <l-control class="" position="topright">
        <v-btn icon> <v-icon>mdi-download</v-icon></v-btn>
      </l-control>
    </l-map>
  </div>
</template>

<script>
import {
  LMap,
  LGeoJson,
  LControlLayers,
  LTileLayer,
  LControlScale,
  LControl,
} from "@vue-leaflet/vue-leaflet";
import L from "leaflet";
import { GeoSearchControl, OpenStreetMapProvider } from "leaflet-geosearch";
import "leaflet/dist/leaflet.css";
import "leaflet-fullscreen/dist/leaflet.fullscreen.css";
import "leaflet-fullscreen/dist/Leaflet.fullscreen";
import "leaflet-geosearch/dist/geosearch.css";
import "leaflet-measure";
import "leaflet-measure/dist/leaflet-measure.css";
import "leaflet-draw";
import "leaflet-draw/dist/leaflet.draw.css";
import "leaflet-timedimension";
// import "leaflet-timedimension/dist/leaflet.timedimension.control.min.css";
export default {
  name: "LeafletMap",
  data() {
    return {
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 6,
      center: [48, -1.219482],
      geojson: null,
      fillColor: "#e4ce7f",
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      tileProviders: [
        {
          name: "OpenStreetMap",
          visible: true,
          attribution:
            '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
          url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
        },
        {
          name: "OpenTopoMap",
          visible: false,
          url: "https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png",
          attribution:
            'Map data: &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, <a href="http://viewfinderpanoramas.org">SRTM</a> | Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)',
        },
      ],
      minTime: 0,
      maxTime: 100,
      timeStep: 1,
      selectedTime: 0,
    };
  },
  components: {
    LMap,
    LTileLayer,
    LControlScale,
    LGeoJson,
    LControlLayers,
    LControl,
  },
  computed: {
    options() {
      return {
        onEachFeature: this.onEachFeatureFunction,
      };
    },
    styleFunction() {
      const fillColor = this.fillColor; // important! need touch fillColor in computed for re-calculate when change fillColor
      return () => {
        return {
          weight: 2,
          color: "#ECEFF1",
          opacity: 1,
          fillColor: fillColor,
          fillOpacity: 1,
        };
      };
    },
    onEachFeatureFunction() {
      if (!this.enableTooltip) {
        return () => {};
      }
      return (feature, layer) => {
        layer.bindTooltip(
          "<div>code:" +
            feature.properties.code +
            "</div><div>nom: " +
            feature.properties.nom +
            "</div>",
          { permanent: false, sticky: true }
        );
      };
    },
  },
  async created() {
    this.loading = true;
    const response = await fetch(
      "https://rawgit.com/gregoiredavid/france-geojson/master/regions/pays-de-la-loire/communes-pays-de-la-loire.geojson"
    );
    const data = await response.json();
    this.geojson = data;
    this.loading = false;
  },
  methods: {
    mapReady() {
      const map = this.$refs.map.leafletObject;

      map.addControl(new L.Control.Fullscreen());

      const provider = new OpenStreetMapProvider();
      const searchControl = new GeoSearchControl({
        provider: provider,
      });
      map.addControl(searchControl);

      var measureControl = new L.Control.Measure({
        completedColor: "#365c8f",
        activeColor: "#365c8f",
        position: "topleft",
      });
      measureControl.addTo(map);

      var drawControl = new L.Control.Draw();
      drawControl.addTo(map);

      // Initialize the TimeDimension control
      const timeDimension = new L.TimeDimension({});
      // Add it to the map
      map.addControl(timeDimension);

      // Create your time series data and time layer as mentioned in the previous response

      // Add the time-based layer to the TimeDimension control
      // timeDimension.addTimedLayer(timeLayer);
    },
  },
};
</script>

<style></style>
