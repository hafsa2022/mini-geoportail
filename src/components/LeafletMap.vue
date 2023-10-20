<template>
  <div style="height: 1000px; width: 100%" class="">
    <l-map
      style="height: 100%; width: 100%"
      ref="map"
      v-model:zoom="zoom"
      :center="center"
      @ready="mapReady"
    >
      <!-- <l-geo-json
        v-if="show"
        :geojson="OukaimdenStationGeoJson"
        :options="options"
        :options-style="OukaimdenStationStyleFunction"
      />
      <l-geo-json
        v-if="show"
        :geojson="RiverBasinTensiftGeoJson"
        :options="options"
        :options-style="RiverBasinTensiftStyleFunction"
      />
      <l-geo-json
        v-if="show"
        :geojson="RherayaSubBasinGeoJson"
        :options="options"
        :options-style="RherayaSubBasinStyleFunction"
      /> -->

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
      <l-control :position="controlPosition">
        <div>
          <input
            type="checkbox"
            v-model="isGeoJsonVisible"
            @change="toggleGeoJsonLayer"
          />
          <label>Show GeoJSON Layer</label>
        </div>
      </l-control>

      <!-- Layer Group for GeoJSON layers -->
      <l-layer-group ref="geoJsonLayerGroup">
        <l-geo-json
          v-if="show"
          :geojson="OukaimdenStationGeoJson"
          :options="options"
          :options-style="OukaimdenStationStyleFunction"
        />
        <l-geo-json
          v-if="show"
          :geojson="RiverBasinTensiftGeoJson"
          :options="options"
          :options-style="RiverBasinTensiftStyleFunction"
        />
      </l-layer-group>
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
import Oukaimden_Station_GeoJson from "../GeoportailData/Oukaimden_Station.json";
import Rheraya_SubBasin_GeoJson from "../GeoportailData/Rheraya_SubBasin.json";
import RiverBasin_Tensift_GeoJson from "../GeoportailData/RiverBasin_Tensift.json";
import {
  LMap,
  // LGeoJson,
  LControlLayers,
  LLayerGroup,
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
import "leaflet-timedimension/dist/leaflet.timedimension.control.min.css";
export default {
  name: "LeafletMap",
  data() {
    return {
      loading: false,
      show: true,
      enableTooltip: true,
      zoom: 6,
      center: [31.181137630474392, -7.8652742138851677],
      fillColor: "#e4ce7f",
      url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      tileProviders: [
        {
          name: "Open Street Map",
          visible: true,
          attribution:
            '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
          url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
        },
        {
          name: "Open Topo Map",
          visible: false,
          url: "https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png",
          attribution:
            'Map data: &copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>, <a href="http://viewfinderpanoramas.org">SRTM</a> | Map style: &copy; <a href="https://opentopomap.org">OpenTopoMap</a> (<a href="https://creativecommons.org/licenses/by-sa/3.0/">CC-BY-SA</a>)',
        },
        {
          name: "World Imagery",
          visible: false,
          url: "https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}.png",
          attribution:
            "Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community",
        },
      ],
      minTime: 0,
      maxTime: 100,
      timeStep: 1,
      selectedTime: 0,
      OukaimdenStationGeoJson: null,
      RherayaSubBasinGeoJson: null,
      RiverBasinTensiftGeoJson: null,
    };
  },
  components: {
    LMap,
    LTileLayer,
    LControlScale,
    // LGeoJson,
    LControlLayers,
    LLayerGroup,
    LControl,
  },
  computed: {
    options() {
      return {
        onEachFeature: this.onEachFeatureFunction,
      };
    },
    OukaimdenStationStyleFunction() {
      const fillColor = "#FF5733";
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
    RherayaSubBasinStyleFunction() {
      const fillColor = "#33FF3F";
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
    RiverBasinTensiftStyleFunction() {
      const fillColor = this.fillColor;
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
          "<div>" +
            (feature.properties.Name ? "Name:" + feature.properties.Name : "") +
            "</div><div>" +
            (feature.properties.Area_km2
              ? "Area km2: " + feature.properties.Area_km2
              : "") +
            "</div><div>" +
            (feature.properties.OUED
              ? "OUED: " + feature.properties.OUED
              : "") +
            "</div>",
          { permanent: false, sticky: true }
        );
      };
    },
  },
  async created() {
    // this.loading = true;
    this.OukaimdenStationGeoJson = Oukaimden_Station_GeoJson;
    this.RherayaSubBasinGeoJson = Rheraya_SubBasin_GeoJson;
    this.RiverBasinTensiftGeoJson = RiverBasin_Tensift_GeoJson;
    // this.loading = false;
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

      // var timeDimension = new L.TimeDimension({
      //   period: "PT5M",
      // });
      // // helper to share the timeDimension object between all layers
      // map.timeDimension = timeDimension;
      // // otherwise you have to set the 'timeDimension' option on all layers.

      // var player = new L.TimeDimension.Player(
      //   {
      //     transitionTime: 100,
      //     loop: false,
      //     startOver: true,
      //   },
      //   timeDimension
      // );

      // var timeDimensionControlOptions = {
      //   player: player,
      //   timeDimension: timeDimension,
      //   position: "bottomleft",
      //   autoPlay: true,
      //   minSpeed: 1,
      //   speedStep: 0.5,
      //   maxSpeed: 15,
      //   timeSliderDragUpdate: true,
      // };

      // var timeDimensionControl = new L.Control.TimeDimension(
      //   timeDimensionControlOptions
      // );
      // map.addControl(timeDimensionControl);

      const timeDimension = new L.TimeDimension({
        period: "PT1H", // Specify the time interval (e.g., 1 hour)
        autoPlay: true, // Automatically play the time animation
        timeInterval: "2010-01-01/2015-01-01",
      });
      map.timeDimension = timeDimension;
      const player = new L.TimeDimension.Player(
        {
          loop: true, // Enable looping of time animation
          startOver: true, // Restart animation when it reaches the end
          buffer: 1, // Specify the time buffer
        },
        timeDimension
      );
      map.timeDimension.player = player;
      const timeDimensionControl = new L.Control.TimeDimension({
        position: "bottomleft",
        autoPlay: true,
        loopButton: true,
        backwardButton: true,
        forwardButton: true,
        timeSlider: true,
        player: player,
      });
      map.addControl(timeDimensionControl);

      //   var wmsUrl = "http://localhost:8088/geoserver/GeoportailData/wms";
      //   var wmsLayer = L.tileLayer.wms(wmsUrl, {
      //     layers: "GeoportailData:temperature_layer_2010_2015",
      //     format: "image/png",
      //     transparent: true,
      //     attribution: "",
      //   });
      //   // Create and add a TimeDimension Layer to the map
      //   var tdWmsLayer = L.timeDimension.layer.wms(wmsLayer);
      //   tdWmsLayer.addTo(map);

      //   // this.$refs.features.mapObject.;
      // },
      // toggleGeoJsonLayer() {
      //   // Toggle the visibility of the GeoJSON layer group
      //   const layerGroup = this.$refs.geoJsonLayerGroup.mapObject;

      //   if (this.show) {
      //     layerGroup.addLayer(this.OukaimdenStationGeoJson);
      //     layerGroup.addLayer(this.RherayaSubBasinGeoJson);
      //   } else {
      //     layerGroup.clearLayers();
      //   }
    },
  },
};
</script>

<style></style>
