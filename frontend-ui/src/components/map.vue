<template>
  <div id="map" style="width: 100%; height: 100%">{{ flightpath }}</div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import Feature from "ol/Feature";
import Point from "ol/geom/Point";
import LineString from "ol/geom/LineString";
import Polyline from "ol/format/Polyline";
import VectorLayer from "ol/layer/Vector";
import VectorSource from "ol/source/Vector";
import Style from "ol/style/Style";
import Circle from "ol/style/Circle";
import Fill from "ol/style/Fill";
import Stroke from "ol/style/Stroke";
import { Vector as layerVector } from "ol/layer";
import { Vector as sourceVector } from "ol/source";
import { fromLonLat, transform } from "ol/proj";
import { Icon } from "ol/style";

export default {
  name: "fenswoodMap",
  props: ["flightpath"],
  data() {
    return {
      elevations: [6, 12, 18],
      map: null,
      view: null,
      iconSource: null,
      iconStyle: null,
      iconFeature: null,
      homeCoordinates: [-2.671671, 51.4233628],
    };
  },
  mounted() {
    this.view = new View({
      center: fromLonLat(this.homeCoordinates),
      zoom: 20,
    });
    this.iconFeature = new Feature({
      geometry: new Point(fromLonLat(this.homeCoordinates)),
    });
    this.iconSource = new VectorSource({
      features: [this.iconFeature],
    });
    this.iconStyle = new Style({
      image: new Icon({
        src: "quadcopter.png",
        scale: 0.4,
        color: "blue",
      }),
    });
    this.iconLayer = new VectorLayer({
      source: this.iconSource,
      style: this.iconStyle,
    });
    this.map = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
      ],
      view: this.view,
    });
    this.map.addLayer(this.iconLayer);
  },

  methods: {
    updatedFlightPath(coordinate) {
      this.flightPath = new Feature({
        geometry: new Point(fromLonLat(coordinate)),
      });
      this.iconFeature.setGeometry(new Point(fromLonLat(coordinate)));

      let newStyle = new Style({
        image: new Circle({
          radius: 2,
          fill: new Fill({ color: "red" }),
        }),
      });

      this.flightPath.setStyle(newStyle);

      this.map
        .getLayers()
        .getArray()[1]
        .getSource()
        .addFeature(this.flightPath);
      this.map.setView(
        new View({
          center: fromLonLat(coordinate),
          zoom: 17,
        })
      );
    },
  },
};
</script>
