# Openlayers 地图 前端笔记



### 一、地图相关依赖在 Vue 中的引入和使用

**前提条件：首先需要下载 openlayers 地图：yarn add ol**

1. **创建基础地图组件（注册为全局组件）并使用：**

```vue
<template>
  <!-- 地图容器 -->
  <div id="map"></div>
</template>

<script>
// 引入 openLayers 基本样式（此处必须，否则地图样式不生效）
import "ol/ol.css";

// 本质就是原生的 ol.Map，原先使用 new ol.Map() 进行地图创建，现使用 new Map() 创建即可
import Map from "ol/Map";
// 地图的视图，原先使用 new ol.View() 设置地图的视图，现使用 new View() 设置即可
import View from "ol/View";

// 瓦片图层，原先使用 new ol.layer.Tile() 设置图层，现使用 new TileLayer() 设置即可
import TileLayer from "ol/layer/Tile";
// 瓦片图层对应的 TileWMS 图层源，原先使用 new ol.source.TileWMS() 设置图层源，现使用 new TileWMS() 设置即可
import TileWMS from "ol/source/TileWMS";
// 瓦片图层对应的 XYZ 图层源，原先使用 new ol.source.XYZ() 设置图层源，现使用 new XYZ() 设置即可
import XYZ from "ol/source/XYZ";

// 工具函数（后续需使用 lodash 的 once 方法解决地图加载完成事件频繁触发的问题）
import _ from "lodash";
export default {
  // 组件名称
  name: "BaseMap",
  data() {
    return {
      // 离线地图配置（将下载好的离线地图资源发给运维代理，并协商好访问该地图资源的地址）
      offlineMap: [
        new TileLayer({
          visible: true,
          title: "wmts",
          source: new XYZ({
            // 与运维协商好访问该地图资源的地址
            url: "/map-server/{z}/{x}/{y}.png",
            projection: "EPSG:3857",
            wrapX: false,
          }),
          className: "offlineMap",
        }),
      ],
      // 在线地图配置
      onlineMap: [
        // 基础图层（无任何文字标注）
        new TileLayer({
          visible: true,
          title: "wmts",
          source: new XYZ({
            url: "http://t0.tianditu.gov.cn/DataServer?T=vec_c&x={x}&y={y}&l={z}&tk=ca0d54bde130e0e4984b58c8fad41a44",
            projection: "EPSG:4326",
            wrapX: false,
          }),
        }),
        // 文字标注图层
        new TileLayer({
          visible: true,
          title: "wmts",
          source: new XYZ({
            url: "http://t0.tianditu.gov.cn/DataServer?T=cva_c&x={x}&y={y}&l={z}&tk=ca0d54bde130e0e4984b58c8fad41a44",
            projection: "EPSG:4326",
            wrapX: false,
          }),
        }),
        // 长江流域航道图层（此处举例接入长江流域的航道图）
        new TileLayer({
          source: new TileWMS({
            url: "http://36.156.157.116:8090/geoserver/CJ/wms?format=image/png&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&width=256&height=256&layers=CJ%3AG_CJ_NJ&CJAUTH=CJUSER&TRANSPARENT=true&tiled=true&STYLES=",
            serverType: "geoserver",
            projection: "EPSG:3857",
          }),
        }),
      ],
      // ais 绿点图层（此处举例接入船舶的 ais 绿点数据）
      AisGreenPointLayer: new TileLayer({
        source: new TileWMS({
          url: "http://36.156.157.116:8090/greenPoint/cite/wms?format=image/png&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&width=256&height=256&layers=cite:ais_point&CJAUTH=CJUSER&TRANSPARENT=true&tiled=true&STYLES=",
          serverType: "geoserver",
          projection: "EPSG:3857",
        }),
      }),
    };
  },
  mounted() {
    // 初始化创建地图
    this.createMap();
  },
  methods: {
    // 初始化创建地图
    createMap() {
      // 地图实例对象
      const map = new Map({
        // 设置地图图层（...offlineMap：离线地图，...onlineMap：在线地图）
        layers: [
          ...this.onlineMap,
          // this.AisGreenPointLayer,
        ],
        // 设置地图的视图
        view: new View({
          // 设置中心点（该经纬度地点会在地图中间显示）
          center: [111, 30],
          // 必须设置，以 4326 格式作为参考坐标
          projection: "EPSG:4326",
          // 初始层级（数字大：局部详细，数字小：全局概览）
          zoom: 12,
          // 最大层级（若是离线地图，下载层级假设为 0-15 级，别此最大只能填 15）
          maxZoom: 15,
          // 最小层级
          minZoom: 3,
        }),
        // 让 id 为 map 的 div 作为地图的容器
        target: "map",
      });

      // 监听地图是否加载完成，使用 lodash 的 once 方法，确保地图加载完成事件只触发一次
      map.on(
        "rendercomplete",
        _.once(() => {
          this.$emit("renderComplete", map);
        })
      );
    },
  },
};
</script>

<style lang="scss" scoped>
// 给地图容器设置宽高为父容器大小
#map {
  width: 100%;
  height: 100%;
}
</style>
```

**注意：在需要使用基础地图组件的页面中，创建适合大小的容器，将地图组件放入，该地图组件会自动适应容器大小**



2. **开发中常用的地图内置方法：**

**（一）方法概览**

```javascript
// 传统要素，原先使用 new ol.Overlay() 设置传统要素，现使用 new Overlay() 设置即可
import Overlay from "ol/Overlay";

// 矢量图层，原先使用 new ol.layer.Tile() 设置图层，现使用 new VectorLayer() 设置即可
import VectorLayer from "ol/layer/Vector";
// 矢量图层对应的 VectorSource 图层源，原先使用 new ol.source.Vector() 设置图层源，现使用 new VectorSource() 设置即可
import VectorSource from "ol/source/Vector";
// VectorSource 图层源中的 Feature 要素，原先使用 new ol.Feature() 创建要素，现使用 new Feature() 创建即可
import Feature from "ol/Feature";

// 用于给 Feature 要素设置经纬度
import Point from "ol/geom/Point";
// 用于给 Feature 要素设置样式
import Style from "ol/style/Style";
// 用于给 Feature 要素设置图标
import Icon from "ol/style/Icon";
// 若不设置图标，可以直接绘制圆
import CircleStyle from 'ol/style/Circle'
// 用于给 Feature 要素设置文字
import Text from "ol/style/Text";
// 用于给 Feature 要素中的某个属性（如文字、或文字背景）设置颜色
import Fill from "ol/style/Fill";
// 用于给 Feature 要素中的文字设置描边
import Stroke from "ol/style/Stroke";

// 用于在地图上基于某个图层创建绘制交互（使用时需指定图层）
import Draw from "ol/interaction/Draw";
// 用于在地图上创建要素的选择交互（使用时需指定方式，如 pointerMove 鼠标移入选择）
import Select from "ol/interaction/Select";
// 要素的选择交互的指定方式（鼠标移入）
import { pointerMove } from "ol/events/condition";
```

