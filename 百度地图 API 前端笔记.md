# 百度地图 API 前端笔记



### 一、地图的基本创建

**前提条件：在 index.html 中引入百度地图 API**

```html
<script type="text/javascript" src="https://api.map.baidu.com/api?v=1.0&type=webgl&ak=密钥"></script>
```

**当引入该 API 后，就会出现一个全局对象 BMapGL（B代表百度，Map代表地图，GL代表绘图后缀名），该对象中存在很多地图操作的相关方法**



**使用步骤：**

1. **首先创建一个容器，设定好宽高，取一个容器 ID 名**
2. **创建地图画布，并开启鼠标滚轮缩放功能**

```javascript
  // 创建地图实例（通过 BMapGL 里的 Map 方法，传入容器ID，构建地图实例，通常为 map）
  var map = new BMapGL.Map("容器ID名");
  // 创建坐标（通过 BMapGL 里的 Point 方法，可以创建坐标）
  var point = new BMapGL.Point(经度, 纬度);
  // 通过 map 实例中的 centerAndZoom 方法（center 代表中心，Zoom 代表缩放），将坐标作为地图中心点，并设定默认缩放级别（数字形式）
  map.centerAndZoom(point, 默认缩放级别);
  // 通过 map 实例中的 enableScrollWheelZoom 方法（激活滚轮缩放），开启鼠标滚轮缩放功能（一般地图都具备此功能，因此在创建基本地图时，可开启该功能）
  map.enableScrollWheelZoom(true);
```

3. **将地图画布中的自带 logo 和版权信息清除（通过控制台定位 class，设置 display:none 即可）**

**尤其注意：地图本身是支持缩放的，但是支持鼠标滚轮缩放需要如上配置，显示缩放控件详见《地图控件》，另外可配置最小缩放级别和最大缩放级别**

```javascript
// 在创建地图实例时，增加一个对象，书写如下属性，可配置最小缩放级别和最大缩放级别
var map = new BMapGL.Map("容器ID名",{
    // 最小缩放级别
    minZoom: 数字,
    // 最大缩放级别
    maxZoom: 数字
});
```



### 二、解决首屏加载地图性能消耗较大的问题

**可通过异步加载 script 标签（该 script 标签引入的是百度地图 API）解决此问题，也就是 window.onload 方法包裹代码块（通过手动创建 script 标签，并设定 src 值，最后插入到当前页面），由于该 script 标签是异步加载的，因此自上而下执行，无法直接获取 BMapGL 这个对象，所以需要将创建地图画布并开启鼠标滚轮功能的代码用一个函数进行包裹，通过异步加载 script 标签，并回调该函数实现地图的基本创建**

```javascript
// 将创建地图的基本步骤使用 init 函数进行包裹
function init() {
  // 创建地图实例（通过 BMapGL 里的 Map 方法，传入容器ID，构建地图实例，通常为 map）
  var map = new BMapGL.Map("容器ID名");
  // 创建坐标（通过 BMapGL 里的 Point 方法，可以创建坐标）
  var point = new BMapGL.Point(经度, 纬度);
  // 通过 map 实例中的 centerAndZoom 方法（center 代表中心，Zoom 代表缩放），将坐标作为地图中心点，并设定缩放级别（数字形式）
  map.centerAndZoom(point, 缩放级别);
  // 开启鼠标滚轮缩放功能（一般都地图都具备此功能，因此在创建基本地图时，附带开启该功能）
  map.enableScrollWheelZoom(true);
}

// 异步加载 script 标签，并执行 init 函数
window.onload = function () {
  var script = document.createElement("script");
  script.src = "https://api.map.baidu.com/api?v=1.0&type=webgl&ak=密钥&callback=init";
  document.body.appendChild(script);
};
```



### 三、设置地图旋转角度和倾斜角度

```JavaScript
//设置地图旋转角度（初始化时可设置，通过其它事件或者方法可再次调用该方法进行设置旋转角度）
map.setHeading(选择角度);
//设置地图的倾斜角度（初始化时可设置，通过其它事件或者方法可再次调用该方法进行设置倾斜角度）
map.setTilt(倾斜角度);
```

**禁用旋转和倾斜功能：**

```javascript
// 在创建地图实例时，增加一个对象，书写如下属性，可禁止地图旋转和倾斜功能
var map = new BMapGL.Map("容器ID名",{
    enableRotate: false,
    enableTilt: false
});
```



### 四、设置地图显示模式（变更地图类型）

```javascript
// 该方法可设置地图类型，传入一个类型常量即可
map.setMapType(地图类型常量);

地图类型常量：
1.标准地图：BMAP_NORMAL_MAP
2.地球模式：BMAP_EARTH_MAP
3.普通卫星地图：BMAP_SATELLITE_MAP
```



### 五、地图控件

```javascript
// 添加比例尺控件（可传入控件配置选项 opts，也可直接传入配置对象）
var scaleCtrl = new BMapGL.ScaleControl(opts);
map.addControl(scaleCtrl);

// 添加缩放控件（可传入控件配置选项 opts，也可直接传入配置对象）
var zoomCtrl = new BMapGL.ZoomControl(opts);
map.addControl(zoomCtrl);

// 添加城市列表控件（可传入控件配置选项 opts，也可直接传入配置对象）
var cityCtrl = new BMapGL.CityListControl(opts);
map.addControl(cityCtrl);
```

**每种控件都可以传入一个 opts，可以设置当前控件停在地图的四角位置（左上角、右上角、左下角、右下角），并且设置偏移值：**

```javascript
// 控件相关配置（可不设置 opts 变量，直接将整个配置对象传入控件）
var opts = {
    // 设置控件定位的四角位置
    anchor: 四角位置常量,
    // 设置相对于四角位置的偏移距离（无单位，纯数字）
    offset: new BMapGL.Size(基于x轴相隔某个角的距离, 基于y轴相隔某个角的距离)
}

四角位置常量：
1.地图的左上角：BMAP_ANCHOR_TOP_LEFT
2.地图的右上角：BMAP_ANCHOR_TOP_RIGHT
3.地图的左下角：BMAP_ANCHOR_BOTTOM_LEFT
4.地图的右下角：BMAP_ANCHOR_BOTTOM_RIGHT
```

**尤其注意：new BMapGL.Size（数字，数字）这个构造方法，对不同的属性使用，效果都不同，对于 offset 代表偏移量，无属性则代表宽高（重要）**



### 六、自定义地图配色

1. **在百度地图 API 的开发者平台中，选择控制台，找到个性化地图，设置地图配色，并发布，获取样式 ID**
2. **初始化地图时，可通过如下方法，加载该配色**

```javascript
map.setMapStyleV2({     
  styleId: '样式ID'
});
```



### 七、百度地图绘图

##### （一）设置 icon 图标

**icon 图标，常用于给 marker 标注使用，即自定义 marker 的样式为 icon 图标样式**

```javascript
var myIcon = new BMapGL.Icon(
    // 设置 icon 图标的地址
    "图片地址", 
    // 设置 icon 图标的宽高
    new BMapGL.Size(宽, 高), 
    {    
    // 当 icon 图标给 marker 使用时，需要指定 icon 图标相对于 point 坐标的位置，使 point 坐标处于 icon 图标的中心点或者自定义位置
    anchor: new BMapGL.Size(数字, 数字),     
    }
);  
```



##### （二）设置 marker 标注

**marker 标注，需传入 point 坐标，可额外传入 icon 图标，自定义 marker 的样式为 icon 图标样式，最后通过 map.addOverlay 方法添加到地图上**

```javascript
// point 坐标的定义方式
var point = new BMapGL.Point(经度, 纬度);

// 基于 point 坐标和 icon 图标创建 marker 标注
var marker = new BMapGL.Marker(point, {
  // 自定义 marker 的样式，myIcon 通过 new BMapGL.Icon 构造函数实现，详见《设置 icon 图标》
  icon: myIcon,
});
// 在地图上添加 marker 标注
map.addOverlay(marker);
```

**尤其注意：点（又称为标注）、线、面及标注，都属于覆盖物，绝大部分覆盖物，都通过 map.addOverlay () 方法添加到地图上**

**通过给 marker 标注绑定事件，触发某些操作**

```javascript
marker.addEventListener("事件类型", function(){
    // 事件类型可参考开发文档
    处理程序 
});
```



##### （三）绘制 Polyline 线

```JavaScript
// 通过 BMapGL 里的 Polyline 方法，绘制 polyline 线
var polyline = new BMapGL.Polyline(
    // 传递一个数组，数组里是多个坐标，依据这些坐标进行线的绘制
    [
		new BMapGL.Point(经度, 纬度),
		new BMapGL.Point(经度, 纬度),
		new BMapGL.Point(经度, 纬度)
	], 
    // 传递一个对象，可配置线的样式，参考文档进行配置
    {
    // 颜色
    strokeColor:"blue",
    // 粗细
    strokeWeight:2,
    // 透明度
    strokeOpacity:0.5
    }
);
// 在地图上添加 polyline 线
map.addOverlay(polyline);
```



##### （四）绘制 polygon 面

```javascript
// 通过 BMapGL 里的 Polygon 方法，绘制 polygon 面
var polygon = new BMapGL.Polygon(
    // 传递一个数组，数组里是多个坐标，依据这些坐标进行面的绘制
    [
		new BMapGL.Point(经度, 纬度),
		new BMapGL.Point(经度, 纬度),
		new BMapGL.Point(经度, 纬度)
    ], 
    // 传递一个对象，可配置面的样式，参考文档进行配置
    {strokeColor:"blue", strokeWeight:2, strokeOpacity:0.5});
// 在地图上添加 polygon 面
map.addOverlay(polygon);
```



##### （五）设置 label 文本标注（文本框）

1. **创建基本 label 文本标注**

```javascript
// 通过 BMapGL 里的 Label 方法，创建 label 文本标注
var label = new BMapGL.Label('文本内容', {
    // 设置 label 文本标注的坐标位置
    position: point,
    // 设置 label 文本标注相对于坐标的偏移位置
    offset: new BMapGL.Size(数字, 数字)
})
// 在地图上添加 label 文本标注
map.addOverlay(label);
```

2. **设置 label 文本标注的样式（自定义 CSS 样式）**

```javascript
label.setStyle({
    // 此处可书写 CSS 样式
    width: '100px',
    height: '100px'
    color: '#000',
    fontSize: '30px',
    border: '2px solid #1E90FF'
})
```

3. **通过给 label 文本标注绑定事件，触发某些操作**

```javascript
label.addEventListener("事件类型", function(){
    // 事件类型可参考开发文档
    处理程序 
});
```



##### （六）设置 infoWindow 信息窗口

```javascript
// 通过 BMapGL 里的 InfoWindow 方法，创建 infoWindow 信息窗口
var infoWindow = new BMapGL.InfoWindow('文本内容（可以是 html 代码）',{
    // 设置信息窗口标题
    title: "标题",
    // 设置信息窗口宽度
    width: 250,
    // 设置信息窗口高度
    height: 100,
    // 设置相对于坐标的偏移位置
    offset: new BMapGL.Size(数字, 数字)
});
// 基于信息窗口和坐标，在地图上打开信息窗口
map.openInfoWindow(infoWindow, map.getCenter());
```

