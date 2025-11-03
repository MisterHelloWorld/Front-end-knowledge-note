# Openlayers 地图 前端笔记



#### 一、基础地图组件

**前提条件：首先需要下载 openlayers 地图：yarn add ol**

**额外：基础地图组件中需要确保地图加载完成事件只触发一次，会用到 lodash 工具依赖：yarn add lodash**

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

// 瓦片图层对应的 XYZ 图层源，原先使用 new ol.source.XYZ() 设置图层源，现使用 new XYZ() 设置即可
import XYZ from "ol/source/XYZ";

// 用于设置默认控件
import { defaults } from "ol/control";

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
          // 图层显示状态，默认显示，后续控制隐藏或者显示只需通过调用 “某个图层.setVisible(布尔值)” 更新即可
          visible: true,
          title: "wmts",
          source: new XYZ({
            // 与运维协商好访问该地图资源的访问路径（z代表瓦片的缩放级别，x代表瓦片的列号，y代表瓦片的行号）
            // 正常离线地图的目录结构，每一层取名规范应该按照 z/x/y 的形式进行取名，若不对，则需要确定该地图资源的每一层目录名称代表什么含义
            url: "/地图资源的访问路径/{z}/{x}/{y}.png",
            // 离线地图资源，选择平面投影（投影坐标系，也就是墨卡托坐标系，编号为 EPSG:3857）
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
          // 图层显示状态，默认显示，后续控制隐藏或者显示只需通过调用 “某个图层.setVisible(布尔值)” 更新即可
          visible: true,
          title: "wmts",
          source: new XYZ({
            url: "http://t0.tianditu.gov.cn/DataServer?T=vec_c&x={x}&y={y}&l={z}&tk=ca0d54bde130e0e4984b58c8fad41a44",
            // 在线地图资源，选择经纬度投影（地理坐标系，也就是 WGS84 坐标系，编号为 EPSG:4326）
            projection: "EPSG:4326",
            wrapX: false,
            // 若某些在线地图最大层级只到某个级别，想要继续放大，会变空白的情况下，使用如下两行代码，用于指定在多少层级下进行插补画面
            maxZoom: 指定层级（一般是在线地图支持的最大层级）,
            interpolate: true,
          }),
        }),
        // 文字标注图层
        new TileLayer({
          // 图层显示状态，默认显示，后续控制隐藏或者显示只需通过调用 “某个图层.setVisible(布尔值)” 更新即可
          visible: true,
          title: "wmts",
          source: new XYZ({
            url: "http://t0.tianditu.gov.cn/DataServer?T=cva_c&x={x}&y={y}&l={z}&tk=ca0d54bde130e0e4984b58c8fad41a44",
            // 在线地图资源，选择经纬度投影（地理坐标系，也就是 WGS84 坐标系，编号为 EPSG:4326）
            projection: "EPSG:4326",
            wrapX: false,
            // 若某些在线地图最大层级只到某个级别，想要继续放大，会变空白的情况下，使用如下两行代码，用于指定在多少层级下进行插补画面
            maxZoom: 指定层级（一般是在线地图支持的最大层级）,
            interpolate: true,
          }),
        }),
      ],
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
          // this.offlineMap,
        ],
        // 设置地图的视图
        view: new View({
          // 设置中心点（该经纬度地点会在地图中间显示）
          center: [111, 30],
          // 无论底图是墨卡托投影（EPSG:3857）还是 EPSG:4326，此处都强制转换为 EPSG:4326 形式，后续经纬度统统不需要使用 fromLonLat 进行坐标转换
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
        // 设置地图控件是否隐藏（如果需要隐藏的话）
        controls: defaults({
          // 右下角的地图信息控件
          Attribution: false,
          // 全屏控件
          FullScreen: false,
          // 鼠标位置控件
          MousePosition: false,
          // 鸟瞰图控件
          OverviewMap: false,
          // 指北针控件
          Rotate: false,
          // 比例尺控件
          ScaleLine: false,
          // 缩放按钮控件
          Zoom: false,
          // 缩放滚动条控件
          ZoomSlider: false,
          // 放大到设定区域控件
          ZoomToExtent: false,
        }),
      });

      // 监听地图是否加载完成，使用 lodash 的 once 方法，确保地图加载完成事件只触发一次（二选一）
      map.on(
        "rendercomplete",
        _.once(() => {
          this.$emit("renderComplete", map);
        })
      );
      // 如果不希望等待地图完全加载完成（地图服务挂了）才执行后续操作，则不需要监听，直接通知父组件即可（二选一）
      this.$emit("renderComplete", map);
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

2. **在需要使用基础地图组件的页面中，创建适合大小的父容器盒子，将地图组件放入，该地图组件会自动适应容器大小**



#### 二、地图常用内置工具类

**地图相关 api 在 Vue 中的引入技巧：只要知道原始的使用方式，将 `ol.x.y` 转变为 `import y from "ol/x/y"`**

```javascript
// 传统要素
import Overlay from "ol/Overlay";

// 用于创建矢量图层
import VectorLayer from "ol/layer/Vector";
// 用于创建矢量图层源
import VectorSource from "ol/source/Vector";
// 用于创建聚合图层源（需要基于矢量图层源创建聚合图层源）
import Cluster from 'ol/source/Cluster';
// 用于创建 Feature 要素（常把创建好的 Feature 要素添加进创建好的矢量图层源中）
import Feature from "ol/Feature";

// 注意：以下要素类型都是给 Feature 要素的 geometry 属性设置，修改同理是修改 geometry 属性
// 用于给 Feature 设置点要素：new Point([经度, 纬度])
import Point from "ol/geom/Point";
// 用于给 Feature 设置线要素：new LineString([[经度A, 纬度A], [经度B, 纬度B], [经度C, 纬度C]])
import LineString from "ol/geom/LineString";
// 用于给 Feature 设置多边形要素（注意是三层结构）：new Polygon([[[经度A, 纬度A], [经度B, 纬度B], [经度C, 纬度C], [经度A, 纬度A]]])
import Polygon from "ol/geom/Polygon";
// 用于给 Feature 设置圆形要素（内置的特殊多边形要素）：new Circle([经度, 纬度], 半径)，由于在 view 视图上已经指定了 EPSG:4326 形式（经纬度投影），因此传入的半径也必须是度的单位，需要将米单位转换为度单位（转换公式：米 / 111319.444）
import Circle from "ol/geom/Circle";

// 用于给 Feature 要素设置样式
import Style from "ol/style/Style";

// 用于给 Style 中的 image 属性创建图标类（主要是点要素常用）
import Icon from "ol/style/Icon";
// 用于给 Style 中的 image 属性创建圆点类，代替图标（主要是点要素常用）
import CircleStyle from 'ol/style/Circle'

// 用于给 Style 中的 stroke 属性创建描边类（主要是线要素、多边形要素常用）
// 或者给文本类中的 stroke 属性创建描边类，设置文字的描边及相关样式
import Stroke from "ol/style/Stroke";

// 用于 Style 中的 fill 属性创建填充类（主要是多边形要素常用）
// 或者给文本类中 fill 属性、backgroundFill 属性，以及圆点类中的 fill 属性，创建颜色填充类（给文字、文字背景、以及直接绘制的圆点填充颜色）
import Fill from "ol/style/Fill";

// 用于给 Style 中的 text 属性创建文本类（点、线、多边形要素通用）
import Text from "ol/style/Text";

// 用于在地图上基于某个图层创建选择交互（使用时需指定图层）
import Select from 'ol/interaction/Select'
// 选择交互的方式（常见鼠标移入交互）
import { pointerMove } from 'ol/events/condition'

// 用于在地图上基于某个图层创建绘制交互（使用时需指定图层）
import Draw from "ol/interaction/Draw";

// 用于将二维数组的经纬度坐标列表转换为地理范围
import { boundingExtent } from 'ol/extent'
// 用于计算两个点的实际距离（单位米）
import { getDistance } from 'ol/sphere'
// 用于计算线要素的总长度或者多边形要素的周长
import { getLength } from 'ol/sphere'
// 用于将经纬度投影（EPSG:4326）的经纬度（也就是常见的度数）转换为墨卡托投影（EPSG:3857）的经纬度（米单位）
import { fromLonLat } from "ol/proj";
```



#### 三、地图常用内置方法

```javascript
// 获取当前视图经纬度范围
const [minLon, minLat, maxLon, maxLat] = this.map.getView().calculateExtent(this.map.getSize());
// 获取当前视图层级
const zoom = this.map.getView().getZoom();
// 将经纬度位置转换成像素位置
const pixel = this.map.getPixelFromCoordinate([经度, 纬度])
// 计算两点的实际距离（需引入 getDistance 方法）
const distance = getDistance([经度, 纬度], [经度, 纬度])
// 以动画的形式缩放层级或移动位置（也可同时存在）
this.map.getView().animate({
  // 缩放层级
  zoom: 13,
  // 中心点移动位置
  center: [经度, 纬度],
  // 动画持续时间
  duration: 1000
})
// 将二维数组的经纬度坐标列表转换为地理范围（找到所有坐标点的边界）
const extent = boundingExtent([[经度, 纬度], [经度, 纬度], ...])
// 将地图的缩放层级和移动位置调整到某个地理范围（需要先将实际数据，转换成二维数组的经纬度坐标列表，再通过 boundingExtent 方法转换成地理范围）
// 注意：如果无法准确定位，可能是数据中存在异常的经纬度，要确保每一条数据的经度、纬度同时存在，若还是无法定位，可能是地图的缩放级别做了限制
this.map.getView().fit(地理范围, {
  // 指定内边距
  padding: [100, 100, 100, 100],
  // 动画持续时间
  duration: 1000
})

// 拿到所有图层
const layers = this.map.getLayers().getArray();
// 查找某个图层（一般在创建图层时记录该图层，若未记录，则需要在创建图层时，指定图层唯一标识，然后通过图层唯一标识进行查找）
const 某个图层 = layers.find((layer) => layer.get("key") === "图层唯一标识");
// 设置某个图层显示或者隐藏
某个图层.setVisible(布尔值)


// 查找某个图层源
const 某个图层源 = 某个图层.getSource()
// 查找某个要素(需要在生成要素时给每个要素指定 id 唯一标识)
const 某个要素 = 某个图层源.getFeatureById(给要素指定的id标识)
// 获取某个要素的几何信息（通常用于获取线要素或者多边形要素的几何信息，用于计算线的长度或多边形的周长）
const 几何信息 = 某个要素.getGeometry()
// 根据线要素或者多边形要素的几何信息，计算线要素的总长度或者多边形要素的周长（需引入 getLength 方法）
const length = 某个要素.getLength(几何信息)
// 更改某个要素的几何信息，如：点要素、多边形要素、线要素的位置和形状（需要先找到某个要素，再根据几何对象类型的不同，设置不同类型的几何数据）
某个要素.setGeometry(new Point([经度, 纬度]))
// 给某个要素设置新的样式（初始样式一般在创建图层时通过样式函数设置）
某个要素.setStyle([new Style({样式}), new Style({样式})...])
```



#### 四、创建图层及对应要素的核心步骤

**规范：实际开发中，一个功能模块对应一个图层，相互独立，没有联系，每个图层之间的逻辑不要交叉影响**

1. **书写创建图层及对应要素的方法：**

```javascript
    创建图层的方法 (数据列表) {
      // 每次创建前，先移除图层及图层内部的要素
      if (this.图层名称) {
        // 移除图层
        this.map.removeLayer(this.图层名称)
        // 清除图层源中的现有要素
        this.图层名称.getSource().clear()
      }
      // 每次创建前，先移除图层的选择交互
      if (this.选择交互名称) {
        this.map.removeInteraction(this.选择交互名称)
      }

      // 创建矢量图层源
      const vectorSource = new VectorSource()
      // 若想实现聚合效果，则需要基于矢量图层源创建聚合图层源
      const cluster = new Cluster({
        // 聚合距离
        distance: 30,
        // 绑定矢量图层源
        source: vectorSource
      })

      // 遍历数据，创建要素
      数据列表.forEach((item) => {
        // 创建一个要素
        const feature = new Feature({
          // 创建点要素
          geometry: new Point([经度, 纬度]),
          // 给要素指定 id 唯一标识（实际开发中用于查找要素）
          id: '实际数据的id',
          // 要素唯一标识，用于在点击事件或样式函数中区分当前点击的要素类别（未设置图层唯一标识无法进行区分，或同一个图层下有多个要素类别，必须设置）
          key: '要素唯一标识',
          // ...其它需要携带的信息数据
        })
        // 将该要素添加到矢量图层源中
        vectorSource.addFeature(feature)
      })

      // 创建矢量图层
      const 图层名称 = new VectorLayer({
        // 用于控制图层的显示层级（当 A 图层的层级大于 B 图层的层级，即便 B 图层中要素的层级高于 A 图层中要素的 层级，但仍然 A 要素大于 B 要素层级）
        zIndex: 数值
        // 使用矢量图层源（二选一）
        source: vectorSource,
        // 若想实现聚合效果，此处使用聚合图层源（二选一）
        source: cluster,
        // 图层唯一标识是必须的，用于在点击事件中区分当前点击的图层类别（若未设置，则必须保证要素设置了唯一标识，二者必须要有一个能区分的，优先图层）
        key: '图层唯一标识',
        // 图层显示状态，默认显示，后续控制隐藏或者显示只需通过调用 “某个图层.setVisible(布尔值)” 更新即可
        visible: true,
        // 自定义要素样式
        style: (feature) => {
          // 拿到当前普通要素信息或聚合要素信息（聚合要素是一个特殊的集合要素，其中 properties.features 中包含当前聚合项下所有要素）
          const properties = feature.getProperties()
          // 普通要素的样式设置，可根据当前要素信息中保存的自定义属性进行条件判断，区分不同情况下需要显示的要素样式（二选一）
          return [
            // 多个样式相互独立
            new Style({
              // ...与下方同理     
            }),
            // 多个样式相互独立
            new Style({
              // 点要素：image 属性（图标形式）
              image: new Icon({
                src: 图标路径,
                rotation: 旋转角度,
                scale: 缩放大小,
                // 图标按照经纬度居中
                anchor: [0.5, 0.5],
                // 旋转角度
                rotation: 弧度（利用 Math.PI来计算）
              }),
              // 点要素：image 属性（直接绘制圆）
              image: new CircleStyle({
                // 半径
                radius: 半径,
                // 填充颜色
                fill: new Fill({
                  color: "#75F94C",
                }),
                // 描边
                stroke: new Stroke({
                  // 描边颜色
                  color: "#043c7b",
                  // 描边宽度
                  width: 0.5,
                }),
              }),

              // 线、多边形要素：stroke 属性
              stroke: new Stroke({
                // 描边颜色
                color: "#043c7b",
                // 描边宽度
                width: 5,
                // 若为虚线，指定虚线间隔
                lineDash: [10, 10]
              }),

              // 多边形要素：fill 属性
              fill: new Fill({
                color: "#043c7b", // 填充颜色
              }),

              // 点、线、多边形要素通用：text 属性
              text: new Text({
                // 文字内容
                text: `此处是需要显示的文本`,
                // 文字大小和字体
                font: '14px Calibri,sans-serif',
                // 文字颜色
                fill: new Fill({
                  color: '#ffffff'
                }),
                // 文字背景颜色
                backgroundFill: new Fill({
                  color: '#000000'
                }),
                // 描边
                stroke: new Stroke({
                  // 描边颜色
                  color: "#043c7b",
                  // 描边宽度
                  width: 0.5,
                }),
                // 文字位置偏移
                offsetX: 0,
                offsetY: 0,
                // 背景的内边距
                padding: [上, 右, 下, 左],
                // 旋转角度
                rotation: 弧度（利用 Math.PI来计算）
              }),
              // 用于控制同一个图层下，某一个要素的显示层级（切记：此处针对的是该图层内的要素层级，无法影响到其它图层的要素层级）
              zIndex: 数值
            })
          ]
          // 若当前是聚合效果，可通过 size===1 来判断当前是否为实际要素并设置实际要素的样式，否则为聚合状态的样式（二选一）
          const size = properties.features.length
          if (size === 1) {
            // 拿到当前聚合形式下实际要素信息
            const currentProperties = properties.features[0].getProperties()
            // 可根据要素信息中保存的自定义属性进行条件判断，区分不同情况下需要显示的要素样式
            return [
              // 多个样式相互独立
              new Style({
              // ...     
              }),
              // 多个样式相互独立
              new Style({
              // ...     
              }),
            ]
          } else {
            return [
              // 多个样式相互独立
              new Style({
              // ...     
              }),
              // 多个样式相互独立
              new Style({
              // ...     
              }),
            ]
          }
          // 若想控制要素的显示或者隐藏，只需要在样式函数中，根据要素信息判断，返回 null 即可隐藏（最优解）
        }
      })
      
        // 基于图层创建选择交互
      const 选择交互名称 = new Select({
        layers: [图层名称],
        // 选择交互方法（鼠标移入）
        condition: pointerMove,
        style: (feature) => { 鼠标移入时的样式 }
      })

      // 添加该图层
      this.map.addLayer(图层名称)
      // 记录该图层（用于判断图层是否存在，移除图层，或在实际开发中查找该图层中某个要素更新位置等）
      this.图层名称 = 图层名称
      // 添加该交互
      this.map.addInteraction(选择交互名称)
      // 记录该交互
      this.选择交互名称 = 选择交互名称
    }
```

2. **将请求拿到的数据列表，传入并调用该方法即可**

```javascript
    this.创建图层的方法(数据列表)
```



#### 五、关于获取要素携带的信息数据详解

**前提须知：要素携带的额外信息数据，最终会存放在要素的 value 当中，与 geometry 几何信息在一起**

**通用取值方法：feature.getProperties()，拿到的是要素的 value 值**

**补充：feature.get(属性名)可直接拿到 value 中的某个属性**

##### （一）普通要素

**点击或者 style 函数中拿到的 feature，就是当前要素本身，可直接通过 feature.getProperties() 方法拿到要素携带的信息数据及几何信息**

##### （二）聚合要素

**聚合状态下，无论是点击，还是 style 函数中拿到的 feature，实际上是一个特殊的集合要素，value 中包含的是 features（集合要素包含的所有要素，数组形式）和 geometry（集合要素的几何信息），本质上可以理解为这个集合要素也携带了额外的信息数据，只不过这个信息数据名字叫 features，包含了所有要素，因此仍然可以通过 feature.getProperties() 方法拿到这个集合要素中的 features 数组信息，当拿到该数组中某一个实际要素时，可再次通过 feature.getProperties() 方法拿到该实际要素携带的信息数据及几何信息**



#### 六、地图加载完成后，绑定点击事件和移动事件

```vue
<template>
  <div class="map-wrap">
    <!--使用地图组件-->
    <base-map ref="baseMap" @renderComplete="renderComplete" />
  </div>
</template>

<script>
export default {
  name: "MapWrap",
  methods: {
    // 地图加载完成触发
    async renderComplete(map) {
      // 保存地图实例
      this.map = map;

      // 监听地图点击事件
      map.on("click", (e) => {
        console.log("当前鼠标点击位置的经纬度：", e.coordinate);
        // 拿到当前点击的要素所属的图层
        const layer = map.forEachFeatureAtPixel(evt.pixel, (feature, layer) => {
          // 返回当前要素
          return layer
        })
        // 拿到当前点击的要素
        const feature = map.forEachFeatureAtPixel(evt.pixel, (feature) => {
          // 返回当前要素
          return feature
        })
        // 如果当前点击的要素所属图层存在
        if (layer) {
          // 拿到当前图层信息
          const currentLayerInfo = layer.getProperties()
          // 根据图层唯一标识区分当前点击的是什么图层
          if (currentLayerInfo.key === '图层唯一标识') {
            // 如果点击的要素存在
            if (feature) {
              // 拿到当前要素信息
              const currentFeatureInfo = feature.getProperties()
              if (currentFeatureInfo.key === '要素唯一标识') {
                // 执行某些操作...
              }
            }
          }
        } else {
          console.log('点击了地图空白区域')
        }
      });
      // 监听鼠标移动事件
      map.on("moveend", (e) => {
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.map-wrap {
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
```



#### 七、计算两点延长线方向相对于正 X 轴的旋转角度

```javascript
// 传入起始点和终点的经纬度，计算旋转角度（注意此处计算的结果是弧度）
const calcAngle = (start, end) => {
  // 计算 x 坐标差值
  const dx = end.longitude - start.longitude
  // 计算 y 坐标差值
  const dy = end.latitude - start.latitude
  // 计算旋转角度
  return Math.atan2(dy, dx)
}
```

**使用：主要应用于图标或者文字，图标若是具备朝向特征，需要 UI 提供的原始图标的初始朝向为正右，在 rotation 属性中使用 -calcAngle( 起始点, 终点 ) 即可，若有偏差，可利用 Math.PI（90 度 = π/2 弧度）换算弧度，通过增、减这个弧度来进行调整**



#### 八、计算两点的像素距离

```javascript
// 传入起始点和终点的经纬度，计算两点的像素距离
const calcDistance = (start, end) => {
  // 起始点的像素位置
  const startPixel = this.map.getPixelFromCoordinate([start.longitude, start.latitude])
  // 终点的像素位置
  const endPixel = this.map.getPixelFromCoordinate([end.longitude, end.latitude])
  // 计算 x 坐标差值
  const dx = end.longitude - start.longitude
  // 计算 y 坐标差值
  const dy = end.latitude - start.latitude
  // 计算平方根，得到两点距离
  return Math.sqrt(dx * dx + dy * dy)
}
```



#### 九、缩短两点延长线方向的距离，得到两个新点的经纬度

1. **在 utils 文件夹中新建 shortenLineBoth.js 文件，书写如下代码：**

```javascript
// 角度转弧度
function toRadians (degrees) {
  return (degrees * Math.PI) / 180
}

// 弧度转角度
function toDegrees (radians) {
  return (radians * 180) / Math.PI
}

/**
 * 计算两点之间的方位角
 * @param {number} lon1 起点经度
 * @param {number} lat1 起点纬度
 * @param {number} lon2 终点经度
 * @param {number} lat2 终点纬度
 * @return {number} 方位角（度）
 */
function getBearing (lon1, lat1, lon2, lat2) {
  const φ1 = toRadians(lat1)
  const φ2 = toRadians(lat2)
  const Δλ = toRadians(lon2 - lon1)

  const y = Math.sin(Δλ) * Math.cos(φ2)
  const x = Math.cos(φ1) * Math.sin(φ2) - Math.sin(φ1) * Math.cos(φ2) * Math.cos(Δλ)
  const θ = Math.atan2(y, x)

  return (toDegrees(θ) + 360) % 360
}

/**
 * 从起点沿方位角移动指定距离，计算新的经纬度
 * @param {number} lon 起点经度
 * @param {number} lat 起点纬度
 * @param {number} bearing 方位角（度）
 * @param {number} distance 距离（米）
 * @return {Object} 新的经纬度 {lon, lat}
 */
function movePoint (lon, lat, bearing, distance) {
  const R = 6371000 // 地球半径（米）
  const λ1 = toRadians(lon)
  const φ1 = toRadians(lat)
  const θ = toRadians(bearing)
  const d = distance

  const φ2 = Math.asin(Math.sin(φ1) * Math.cos(d / R) + Math.cos(φ1) * Math.sin(d / R) * Math.cos(θ))

  const λ2 =
    λ1 + Math.atan2(Math.sin(θ) * Math.sin(d / R) * Math.cos(φ1), Math.cos(d / R) - Math.sin(φ1) * Math.sin(φ2))

  return {
    lon: toDegrees(λ2),
    lat: toDegrees(φ2)
  }
}

/**
 * 计算两点之间的距离（米）
 * @param {number} lon1 起点经度
 * @param {number} lat1 起点纬度
 * @param {number} lon2 终点经度
 * @param {number} lat2 终点纬度
 * @return {number} 距离（米）
 */
function getDistance (lon1, lat1, lon2, lat2) {
  const R = 6371000
  const φ1 = toRadians(lat1)
  const φ2 = toRadians(lat2)
  const Δφ = toRadians(lat2 - lat1)
  const Δλ = toRadians(lon2 - lon1)

  const a = Math.sin(Δφ / 2) * Math.sin(Δφ / 2) + Math.cos(φ1) * Math.cos(φ2) * Math.sin(Δλ / 2) * Math.sin(Δλ / 2)
  const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))

  return R * c
}

/**
 * 从两端同时缩短两点之间的线段
 * @param {number} lon1 起点经度
 * @param {number} lat1 起点纬度
 * @param {number} lon2 终点经度
 * @param {number} lat2 终点纬度
 * @param {number} ratio 保留比例（0-1之间，0.8表示缩短20%，两端各缩短10%）
 * @return {Object} 缩短后的线段端点 {start: {lon, lat}, end: {lon, lat}}
 */
const shortenLineBoth = (lon1, lat1, lon2, lat2, ratio) => {
  // 计算原始距离
  const distance = getDistance(lon1, lat1, lon2, lat2)
  // 计算需要缩短的总距离
  const totalReduction = distance * (1 - ratio)
  // 每端需要缩短的距离（两端平均分配）
  const eachEndReduction = totalReduction / 2

  // 计算从起点到终点的方位角
  const forwardBearing = getBearing(lon1, lat1, lon2, lat2)
  // 计算从终点到起点的反向方位角（用于从终点向起点方向移动）
  const backwardBearing = (forwardBearing + 180) % 360

  // 计算新的起点（从原起点向终点方向移动缩短距离）
  const newStart = movePoint(lon1, lat1, forwardBearing, eachEndReduction)
  // 计算新的终点（从原终点向起点方向移动缩短距离）
  const newEnd = movePoint(lon2, lat2, backwardBearing, eachEndReduction)

  return {
    start: newStart,
    end: newEnd
  }
}

export default shortenLineBoth
```

2. **在需要的地方引入并调用，使用方式：**

```javascript
const {start, end} = shortenLineBoth(起始点经度, 起始点纬度, 终点经度, 终点纬度, 缩短比例 0-1 之间)
// 转换后的数据：start.lon   start.lat   end.lon   end.lat
```


