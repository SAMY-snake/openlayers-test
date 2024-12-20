<template>
  <div class="main">
    <div class="map" id="map" ref="map"></div>
    <div class="icon-wrap">
      <img src="@/assets/icon-1.svg" @click="addMarker()" />
      <span class="font-1">保存</span>
    </div>
    <div v-if="isShowPopup" class="popup">
      <div class="p-header">
        {{ selectRow.name }}
        <div class="close" @click="isShowPopup = false">x</div>
      </div>
      <div class="p-content">
        <div class="p-row">
          <span>点位信息1:</span>
          <span>1111</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Map from "ol/Map.js";
import View from "ol/View.js";
import ImageLayer from "ol/layer/Image.js";
import Projection from "ol/proj/Projection.js";
import Static from "ol/source/ImageStatic.js";
import { getCenter } from "ol/extent.js";
import { Draw, Modify, Snap } from "ol/interaction.js";
import { Vector as VectorSource } from "ol/source.js";
import { Vector as VectorLayer } from "ol/layer.js";
export default {
  name: "HelloWorld",
  data() {
    return {
      map: null,
      markerLayer: null,
      vectorSource: null,
      isShowPopup: false,
      selectRow: {},
    };
  },
  mounted() {
    this.initMap();
  },
  methods: {
    initMap() {
      const extent = [0, 0, 1980, 1080];
      const projection = new Projection({
        code: "xkcd-image",
        units: "pixels",
        extent: extent,
      });
      let vectorSource = new VectorSource();
      const vector = new VectorLayer({
        source: vectorSource,
        style: {
          "icon-src": require("@/assets/icon-1.svg"),
          "icon-width": 60,
          "icon-height": 30,
        },
      });

      this.map = new Map({
        layers: [
          new ImageLayer({
            source: new Static({
              url: require("@/assets/test1.jpg"),
              projection: projection,
              imageExtent: extent,
            }),
          }),
          vector,
        ],
        target: "map",
        view: new View({
          projection: projection,
          center: getCenter(extent),
          zoom: 2,
          maxZoom: 8,
        }),
      });
      const modify = new Modify({ source: vectorSource });
      this.map.addInteraction(modify);
      this.vectorSource = vectorSource;
      this.initMapClick();
    },
    initMapClick() {
      this.map.on("singleclick", (evt) => {
        console.log(111, evt.coordinate, evt);
      });
    },
    addMarker() {
      let draw = new Draw({
        source: this.vectorSource,
        type: "Point",
        style: {
          "icon-src": require("@/assets/icon-1.svg"),
          "icon-width": 60,
          "icon-height": 30,
        },
      });
      this.map.addInteraction(draw);
      let snap = new Snap({ source: this.vectorSource });
      this.map.addInteraction(snap);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main,
.map {
  width: 100%;
  height: 100%;
}

.icon-wrap {
  position: absolute;
  top: 20px;
  left: 20px;
  width: 200px;
  height: 300px;
  background-color: rgba(0, 0, 0, 0.5);
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  img {
    width: 40px;
  }
  .font-1 {
    font-size: 18px;
    color: #fff;
  }
}
</style>
