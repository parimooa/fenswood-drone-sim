<template>

  <div id="map" style="width: 100%; height: 100%">{{ flightpath }}


  </div>
</template>

<script>
import Map from 'ol/Map';
import View from 'ol/View';
import TileLayer from 'ol/layer/Tile';
import OSM from 'ol/source/OSM';
import Feature from 'ol/Feature';
import Point from 'ol/geom/Point';
import LineString from 'ol/geom/LineString';
import Polyline from 'ol/format/Polyline';
import VectorLayer from 'ol/layer/Vector';
import VectorSource from 'ol/source/Vector';
import Style from 'ol/style/Style';
import Circle from 'ol/style/Circle'
import Fill from 'ol/style/Fill'
import Stroke from 'ol/style/Stroke';
import {Vector as layerVector} from "ol/layer";
import {Vector as sourceVector} from "ol/source";
import {transform} from "ol/proj";
import {Icon} from "ol/style";

export default {
  name: 'fenswoodMap',
  props: ['flightpath'],
  data() {
    return {
      elevations: [6, 12, 18],
      map: null,
      // coordinates: [
      //   [
      //     -2.6718700833877165,
      //     51.423590217956075
      //   ],
      //   [
      //     -2.6718475668135966,
      //     51.42356626670491
      //   ],
      //   [
      //     -2.671845580057095,
      //     51.42355305221594
      //   ],
      //   [
      //     -2.6718442555525144,
      //     51.42352951389816
      //   ],
      //   [
      //     -2.671874719153436,
      //     51.42351588644556
      //   ],
      //   [
      //     -2.6719303483368435,
      //     51.42353199161681
      //   ],
      //   [
      //     -2.6719310105895886,
      //     51.42355057449896
      //   ],
      //   [
      //     -2.6719137920305513,
      //     51.423577829375205
      //   ],
      //   [
      //     -2.671870039966791,
      //     51.42359050736286
      //   ]
      // ],
    }
  },
  mounted() {
    this.map = new Map({
      // the map will be created using the 'map-root' ref
      target: 'map',
      layers: [
        // adding a background tiled layer
        new TileLayer({
          source: new OSM(), // tiles are served by OpenStreetMap
          name: 'OpenStreetMap',
          isBaseMap: true
        }),
        new layerVector({
          source: new sourceVector({
            features: []
          }),
          style: [new Style({
            image: new Circle({
              radius: 2,
              fill: new Fill({color: 'red'})
            })
          }),
            // new Style({
            //     image: new Icon({
            //       src: 'quadcopter.png',
            //       anchor: [0.9, 0.2],
            //       anchorXUnits: 'fraction',
            //       anchorYUnits: 'fraction',
            //       opacity: 0.75,
            //       scale: 0.2,
            //       color: "red"
            //     })
            //   })]
          ]
        })

      ],
      // the map view will initially show the whole world
      view: new View({
        zoom: 20,
        center: transform([-2.671671, 51.4233628], 'EPSG:4326', 'EPSG:3857'),
        constrainResolution: true
      }),
    })


  },

  methods: {
    updateFlightPath() {
      this.coordinates.forEach((coordinate, i) => {
        setTimeout(() => {
          // decode the polyline string into an array of coordinates
          // const decoded = new Polyline().readGeometry(coordinates, {dataProjection: 'EPSG:4326', featureProjection: 'EPSG:3857'});
          // create a new LineString feature with the decoded coordinates
          this.flightPath = new Feature({
            geometry: new Point(transform(this.coordinates[i], 'EPSG:4326', 'EPSG:3857'))
          });
          // update the map with the new flight path
          console.log()
          console.log(this.map)
          this.map.getLayers().getArray()[1].getSource().addFeature(this.flightPath);
        }, i * 1000);
      })

    },
    updatedFlightPath(coordinate) {
      let flightPath = new Feature({
        geometry: new Point(transform(coordinate, 'EPSG:4326', 'EPSG:3857'))
      });
      // update the map with the new flight path
      // console.log("coordinates " + coordinate)
      this.map.getLayers().getArray()[1].getSource().addFeature(flightPath);

    }
  }
}


</script>
