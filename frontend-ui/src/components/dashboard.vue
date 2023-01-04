<template>
  <v-app id="fenswood-volcano">
    <v-app-bar>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title class="bg-red-darken-4 text-white"
        >Fenswood Volcano (Group Project 1 )</v-toolbar-title
      >
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-list-item>
        <v-list-item>
          <v-list-item-title>Group-Project 1</v-list-item-title>
        </v-list-item>
      </v-list-item>

      <v-divider></v-divider>

      <v-list dense>
        <v-list-item
          v-for="item in items"
          :key="item.title"
          :to="item.route"
          link
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main class="bg-blue-lighten-2">
      <v-container>
        <v-row no-gutters justify="space-between">
          <v-col sm="2">
            <v-card tile outlined width="100%" height="100%">
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  Start Mission
                </v-toolbar-title>
                <v-spacer></v-spacer>
                <v-icon color="green" size="55"
                  >mdi-arrow-right-bold-circle</v-icon
                >
              </v-toolbar>

              <v-divider></v-divider>
              <v-card-subtitle>This will start mission</v-card-subtitle>
              <v-divider></v-divider>
              <v-card-text class="bg-grey-lighten-1 text-center">
                <v-btn
                  fab
                  icon
                  size="8em"
                  color="success"
                  @click="sendMissionStart"
                  :disabled="!vehicleState.connected || vehicleState.armed"
                >
                  <v-icon size="80px">mdi-play</v-icon>
                </v-btn>
              </v-card-text>
            </v-card>
          </v-col>
          <v-col sm="2">
            <v-card tile outlined height="100%" width="100%">
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  Abort Mission
                </v-toolbar-title>
                <v-spacer></v-spacer>
                <v-icon color="red" size="60">mdi-alert-octagon</v-icon>
              </v-toolbar>

              <v-divider></v-divider>
              <v-card-subtitle>This will abort mission</v-card-subtitle>
              <v-divider></v-divider>

              <v-card-text class="bg-grey-lighten-1 text-center">
                <v-btn
                  fab
                  icon
                  size="8em"
                  color="red"
                  @click="sendMissionAbort"
                  :disabled="!vehicleState.armed"
                  ><v-icon size="80px">mdi-close-octagon</v-icon></v-btn
                >
              </v-card-text>
            </v-card>
          </v-col>
          <v-col sm="2">
            <v-card
              :color="rosConnection ? 'green' : 'red'"
              max-width="450"
              height="100%"
              width="100%"
            >
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  ROS Connection
                </v-toolbar-title>
                <v-spacer></v-spacer>
                <v-icon :color="rosConnection ? 'green' : 'red'" size="60"
                  >mdi-connection</v-icon
                >
              </v-toolbar>

              <v-divider></v-divider>
              <v-card-subtitle class="bg-white"
                >Connection Status to ROS Bridge</v-card-subtitle
              >
              <v-divider></v-divider>

              <v-card-text class="text-lg-center">
                <v-img
                  :color="rosConnection ? 'green' : 'red'"
                  max-width="150"
                  max-height="100"
                  src="ROS.png"
                ></v-img>
              </v-card-text>
            </v-card>
          </v-col>
          <v-col sm="3">
            <v-card class="mx-auto" max-width="450" height="100%" width="100%">
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  Vehicle State ({{ mavState[vehicleState.system_status] }})
                </v-toolbar-title>
                <v-spacer></v-spacer>
                <v-icon color="green" size="60">mdi-quadcopter</v-icon>
              </v-toolbar>

              <v-card-subtitle>
                Last status :
                {{ vehicleState.header.stamp.sec }}</v-card-subtitle
              >

              <v-divider></v-divider>
              <v-list-item class="bg-grey-lighten-1">
                <div>
                  <p class="text-h5 text-sm-left">
                    Mode : &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{{ vehicleState.mode }}
                  </p>
                  <p class="text-h5 text-sm-left">
                    Status
                    :&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{{
                      mavState[vehicleState.system_status]
                    }}
                  </p>
                  <p class="text-h5 text-sm-left">
                    Connection :&nbsp; {{ vehicleState.connected }}
                  </p>
                  <p class="text-h5 text-sm-left">
                    Guided :&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                    {{ vehicleState.guided }}
                  </p>
                  <p class="text-h5 text-sm-left">
                    Armed
                    :&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                    {{ vehicleState.armed }}
                  </p>
                </div>
              </v-list-item>
            </v-card>
          </v-col>
        </v-row>
      </v-container>

      <v-container>
        <v-row no-gutters justify="space-around">
          <v-col cols="5">
            <v-card height="100%">
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  Camera Feed
                </v-toolbar-title>

                <v-spacer></v-spacer>

                <v-icon color="green" size="60">mdi-camera</v-icon>
              </v-toolbar>
              <v-switch
                v-model="switchCameraFeed"
                color="green"
                true-value="ON"
                false-value="OFF"
                :disabled="!rosConnection"
                :label="`Camera Feed Switched : ${switchCameraFeed}`"
              >
              </v-switch>

              <v-card-subtitle></v-card-subtitle>

              <v-divider></v-divider>
              <v-card-text class="text-center">
                <v-img
                  :src="raw_image"
                  max-width="900"
                  max-height="500"
                ></v-img>
              </v-card-text>
            </v-card>
          </v-col>
          <v-col cols="5">
            <v-card height="100%">
              <v-toolbar class="bg-blue-grey-darken-1">
                <v-toolbar-title class="text-lg-subtitle-1 text-white">
                  GPS Position
                </v-toolbar-title>

                <v-spacer></v-spacer>

                <v-icon color="green" size="60">mdi-satellite-uplink</v-icon>
              </v-toolbar>
              Last status : {{ gps_position.header.stamp.sec }}

              <v-divider></v-divider>
              <v-card-text class="text-center">
                {{ gps_position.longitude }}, {{ gps_position.latitude }}
              </v-card-text>

              <fenswoodMap ref="droneMap"></fenswoodMap>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import FenswoodMap from "@/components/map.vue";
//
// window.require = (lib) => import(lib)
import ROSLIB from "roslib";
import moment from "moment";

const SOCKET_PORT = 9090;
const IMAGE_TOPIC = "/vehicle_1/camera/image_raw/compressed";
const GPS_TOPIC = "/vehicle_1/mavros/global_position/global";
const VEHICLE_STATE = "/vehicle_1/mavros/state";

export default {
  components: {
    FenswoodMap,
  },

  data() {
    return {
      switchCameraFeed: false,
      startMission: false,
      drawer: null,
      items: [
        { title: "Dashboard", icon: "mdi-view-dashboard", route: "fenswood" },
        { title: "Map", icon: "mdi-map", route: "map" },
        { title: "About", icon: "mdi-forum" },
      ],
      ros: null,
      start_message: "",
      rosConnection: false,
      connection_error: false,
      error: null,
      mission_abort: "unknown",
      raw_image: "src/assets/bristol-flight-lab.png",
      gps_position: {
        header: {
          stamp: {
            sec: "2022-12-28T18:20:38.000Z",
            nanosec: 648931200,
          },
          frame_id: "vehicle_1/base_link",
        },
        status: {
          status: -1,
          service: 1,
        },
        latitude: 51.4233627,
        longitude: -2.6716709,
        altitude: 151.43964655094123,
        position_covariance: [-1, 0, 0, 0, 0, 0, 0, 0, 0],
        position_covariance_type: 0,
      },
      image_topic: null,
      vehicleState: {
        header: {
          stamp: {
            sec: 1672229115,
            nanosec: 938030300,
          },
          frame_id: "",
        },
        connected: false,
        armed: false,
        guided: false,
        manual_input: false,
        mode: "UNKNOWN",
        system_status: 0,
      },
      mavState: {
        0: "unknown",
        1: "Booting up",
        2: "Calibrating",
        3: "Standby",
        4: "Active",
        5: "Critical",
        6: "Emergency",
        7: "Poweroff",
        8: "Termination",
      },
      home: [-2.6719303483368435, 51.42353199161681],
      center: [40, 40],
      projection: "EPSG:4326",
      zoom: 2,
      rotation: 0,
      view: null,
    };
  },
  watch: {
    switchCameraFeed(value) {
      console.log("switchCameraFeed " + this.switchCameraFeed);

      if (value === "ON") {
        this.getRawImage();
      } else {
        console.log("unsubscribing from camera feed" + this.image_topic);
        this.image_topic.unsubscribe();
        this.raw_image = "src/assets/bristol-flight-lab.png";
        console.log("After unsubscribing from camera feed" + this.image_topic);
      }
    },
    "vehicleState.header.stamp.sec": {
      handler: function (after, before) {
        // console.log("new" + JSON.stringify(after))
        // console.log("old" + JSON.stringify(before))
      },
      deep: true,
    },

    "gps_position.header.stamp.sec": {
      handler: function (after, before) {
        let coordinate = [
          this.gps_position.longitude,
          this.gps_position.latitude,
        ];
        this.$refs.droneMap.updatedFlightPath(coordinate);
      },
      deep: true,
    },

    gps_position: {
      handler: function (after) {
        let coordinate = [after.longitude, after.latitude];
        this.$refs.droneMap.updatedFlightPath(coordinate);
      },
      deep: true,
    },
  },

  methods: {
    connectRosbridge() {
      // Create a connection to the rosbridge WebSocket server.
      this.ros = new ROSLIB.Ros({
        url: "ws://localhost:" + SOCKET_PORT,
      });

      this.ros.on("connection", function () {
        this.rosConnection = "connected";
        console.log("Connected to websocket server:" + this.rosConnection);
      });

      this.ros.on("error", function (error) {
        this.connection_error = true;
        console.log("Error connecting to websocket server: ", error);
      });
    },

    sendMissionStart() {
      if (this.ros && this.ros.isConnected) {
        let startPublisher = new ROSLIB.Topic({
          ros: this.ros,
          name: "/mission_start",
          messageType: "std_msgs/Empty",
        });
        startPublisher.publish({});
        console.log("Mission Start Pressed");
        this.start_message = "Mission Started";
      } else {
        console.log("No connection to Ros, establishing connection");
        this.connectRosbridge();
      }
    },

    sendMissionAbort() {
      let estopPublisher = new ROSLIB.Topic({
        ros: this.ros,
        name: "/emergency_stop",
        messageType: "std_msgs/Empty",
      });
      estopPublisher.publish({});
      this.mission_abort = "Mission Cancelled";
    },

    connection() {
      if (this.ros) {
        this.rosConnection = this.ros.isConnected;
      }
    },

    updatedImage(message) {
      {
        // console.log("Getting image now")
        let bytes = new Uint8Array(message.data);
        let binary = bytes.reduce(
          (data, b) => (data += String.fromCharCode(b)),
          ""
        );
        this.raw_image = "data:image/jpeg;base64," + btoa(binary);
      }
    },

    getRawImage() {
      console.log("Subscribing image from topic " + IMAGE_TOPIC);
      if (this.image_topic != null) {
        this.image_topic.unsubscribe();
      }
      this.image_topic = new ROSLIB.Topic({
        ros: this.ros,
        name: IMAGE_TOPIC,
        messageType: "sensor_msgs/CompressedImage",
      });
      this.image_topic.subscribe(this.updatedImage);
    },

    updatedState(state) {
      console.log("Vehicle State " + state);
      this.vehicleState = state;
      let date = moment.unix(state.header.stamp.sec);
      this.vehicleState.header.stamp.sec = date.format("D-MMM-YYYY, h:mm:ss A");
    },

    getVehicleState() {
      console.log("Vehicle State");
      if (this.vehicle_state != null) {
        this.vehicle_state.unsubscribe();
      }
      this.vehicle_state = new ROSLIB.Topic({
        ros: this.ros,
        name: VEHICLE_STATE,
        messageType: "mavros_msgs/State",
      });

      this.vehicle_state.subscribe(this.updatedState);
    },

    updatedGPS(message) {
      // console.log("Updated GPS ")
      this.gps_position = message;
      let date = moment.unix(message.header.stamp.sec);
      return (this.gps_position.header.stamp.sec = date.format(
        "D-MMM-YYYY, h:mm:ss A"
      ));
    },

    getGPS() {
      console.log("GPS pos");
      if (this.gpsLocation != null) {
        this.gpsLocation.unsubscribe();
      }
      this.gpsLocation = new ROSLIB.Topic({
        ros: this.ros,
        name: GPS_TOPIC,
        messageType: "sensor_msgs/msg/NavSatFix",
      });

      this.gpsLocation.subscribe(this.updatedGPS);
    },

    geoLocChange(loc) {
      console.log(loc);
      this.view.value.fit([loc[0], loc[1], loc[0], loc[1]], {
        maxZoom: 14,
      });
    },
  },
  created() {
    this.interval = setTimeout(() => this.connection(), 1000);
  },
  mounted() {
    this.connectRosbridge();
    this.getVehicleState();
    this.getGPS();
  },
};
</script>
