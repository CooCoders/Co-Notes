# Note of  Three.js

## 组成

Three.js 构建程序的组成有三部分：场景 -- 相机 -- 渲染器（其整个过程与现实生活中拍照的过程是一致的）

- 场景 Scene
  - 网格模型 Mesh
    - 几何体：定义几何体的种类，例如球体、立方体、圆柱体等等
    - 材质：用于表现几何体的颜色、透明度等等
  - 光源 Light
    - 定义环境光、点光源或者平行光的位置以及颜色等等
- 相机 Camera
  - 相当于观察者的眼睛位置，设置观察物体的位置、观察方向等等
- 渲染器 Renderer
  - 渲染器作用的直观理解相当于快门，得到经过渲染后观察者所见的场景结果

## 周期动画渲染

```js
function render() {
    renderer.render(scene, camera);//执行渲染操作
    mesh1.rotateY(0.005);//每次绕y轴旋转0.01弧度
    mesh3.rotateY(0.01);//每次绕y轴旋转0.01弧度
    scene.rotateY(0.003);
    requestAnimationFrame(render);//请求再次执行渲染函数render
}
render();
var controls = new THREE.OrbitControls(camera, renderer.domElement);
```



## 点线面模型

- 点模型：将几何体的每个顶点渲染为一个方形区域
- 线模型：使用线将几何体顶点连接起来
- 网格模型：三个顶点确定一个三角形，网格模型通过一系列三角形拼接组成一个曲面

网格模型更为精细，在属性中设置 wireframe 可以看到构成几何体的所有三角形：

```js
var material3 = new THREE.MeshLambertMaterial({
    color: 0xff0000,
    wireframe: true,
});
```

## 几何体创建

### SphereGeometry - 球

创建球体，其构造函数格式为：

```
SphereGeometry(radius, widthSegments[32], heightSegments[16], phiStart[0], phiLength[2*PI], thetaStart[0], thetaLength[2*PI])
```

其中：

- widthSegments 、heightSegments 表示水平和竖直方向上的顶点数，通过多个等间距的顶点构建水平的圆环或竖直的圆环，其中前者默认为32， 后者默认值为16
- phiStart等参数用于控制生成不规则的球体（球体切片）（球体创建是通过绕y轴和x轴创建的，后四个参数可以控制这些顶点的起点）

### RingGeometry - 圆环

圆环构造函数为：

```
RingGeometry(innerRadius[0.5], outerRadius[1], thetaSegments[>=3, default=8], phiSegments[>=1,default=8], thetaStart[0])
```

### CylinderGeometry - 圆柱

构造函数格式：

```
CylinderGeometry(
	radiusTop: 顶部半径 default=1
	radiusBottom: 底部半径 default=1
	height：高度 default=1
	radialSegments： 圆柱侧面的分段数 相当于横向分了多少段 default=8
	heightSegments： 圆柱侧面沿高度的分段数 纵向分了多少段 default=1
	openEnded: boolean 指明圆锥的底面是开放的还是封闭的 default=false（封顶） 如果设置为true 在高度设置为0的时候展示的是一个圆环
	thetaStart：第一个分段的起始角度 default=0
	thetaLength： 圆柱底面圆扇区的中心角 default=PI*2
)
```

最后两个依然用来构造不完整的圆柱

### EllipseCurve - 椭圆曲线 

构造函数格式：

```
EllipseCurve(
	aX: 椭圆中心x坐标 default 0
	aY: y坐标 default 0
	xRadius： x轴方向上的椭圆半径 default 1
	yRadius： y轴方向上的椭圆半径 default 1
	aStartAngle：弧度表示的 x轴起曲线开始的角度 default 0
	aEndAngle： 弧度表示的 x轴起的曲线终止的角度 default PI*2
	aClockwise： 是否按照顺时针方向绘制 default false
	aRotation： 旋转角度
)
```

绘制椭圆曲线，过程为： 创建曲线 -> 取点 -> 使用这些点构建缓冲几何体：

```js
var curve = new THREE.EllipseCurve(0, 0, 65, 70, aRoation=3);
const points = curve.getPoints(50);
console.log(points);
const geometry = new THREE.BufferGeometry().setFromPoints(points);
var material = new THREE.LineBasicMaterial({
    color: 0xff0000,
});
var line = new THREE.Line(geometry, material);
```



## Raycaster 光线投射

该方法用于鼠标点击获取，在三维空间中计算鼠标移过了什么物体，其构造方法为：

```
Raycaster(
	origin: 光线投射的原点向量 （vector3）
	direction： 射线提供方向的方向向量，应标准化 （vector3）
    near：返回的所有结果应该比 near值大 （float default=0）
    far：返回的所有结果应该比 far 小（float default=inf.）
)
```

后两个相当于限定距离，限制指定距离内的对象被选中。

两个主要方法：

```
raycaster.setFromCamera (coords: vector2,  camera: Camera)
```

- coords： **标准化后的鼠标坐标**（方向）
- camera：相机（射线原点）

```
var res = raycaster.intersectObject(object: Object3D, recursive: boolean, optionalTarget: Array)
```

- object：检查与射线相交的几何体（一般我们会首先将所有可点击的物体加入到物体列表进行传入，或者直接使用scene.children 对场景的所有物体进行检查）
- recursive：default=false 表示只检查对象本身，不检查对象后代（如果使用了Group将多个物体设置为一组，需要设置为 true）
- optionalTarget：结果目标数组，每次使用时必须清空

**该方法返回的是所有与射线交叉的数组**，这些相交的物体会按照距离进行排序，实际中一般都是用最近的物体表示点击的物体，所以会使用 **res[0]**，返回的数组形式：

```
[{distance, point, face, object...}, {}, {}...]
```

其中 distance 表示射线原点与相交点之间的距离 point 表示相交点的位置坐标 object 表示相交的物体，因此如果要对点击的物体做操作的话要使用 `res[0].object.xxx`





## requestAnimationFrame 周期性渲染

每次执行 render 方法，浏览器就会渲染出一帧图像显示，因此，物体移动动画效果的原理就是： 物体移动 + 重新渲染。由此设定渲染页面的间隔，就可以实现物体移动的动画。

设定自动渲染的方法上，可以但不推荐使用 `window.setInterval()`方法，现在更好的选择是 `window.requestAnimationFrame()`方法。

`requestAnimationFrame()`的参数为**待调用的函数**，该方法会向浏览器发起执行该函数的请求，而执行是由浏览器决定，默认情况下是 60FPS



## 光源设置

使用到的光源有：

**点光源**：从发光点向外发散，可以使物体表面不同区域的明暗程度变化，需要设置位置

**环境光**：没有特定方向的光源 主要是整体均匀的改变物体表面的明暗程度

## 相机设置

根据正射投影和透视投影两种，three.js中的相机分为两类：正投影相机对象 和 透视投影相机 ，后者即是人的眼睛观察世界的方式。

正投影相机对象的建立：

```js
var width = window.innerWidth;
var height = window.innerHeight;
var k = width / height;
// 范围系数 值越大显示的范围越大
var s = 250;

// 正投影相机对象
var camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1500);
// OrthographicCamera(left, right, top, bottom, near, far)
```

其中前四个参数表示渲染空间的边界（left、right、top、bottom），near表示从距离相机多远的位置开始渲染，一般设置非常小的值 default=0.1， far 表示停止渲染的位置 default=1000

例如，相机和光源的基础设置：

```js
var point = new THREE.PointLight(0xffffff);
point.position.set(400, 200, 300);
scene.add(point);

var ambient = new THREE.AmbientLight(0x444444);
scene.add(ambient);
```



## WebGLRenderer - 渲染器 

用于渲染场景，其基本设置如：

```js
// 创建渲染器
var renderer = new THREE.WebGLRenderer(antialias=true);
// 设定渲染器尺寸 颜色
renderer.setSize(width, height);
renderer.setClearColor(0xcccccc);
/* renderer.domElement 表示渲染结果 一个canvas对象
           需要向 body （或者某个局部div）中追加进去*/
document.body.appendChild(renderer.domElement);
renderer.render(scene, camera);
```

其中 `new THREE.WebGLRenderer(parameter： object)`创建了一个渲染器，参数 antialias 表示是否开启抗锯齿，默认为 false。

renderer的属性 domElement 表示一个 canves，渲染器在上面绘制输出，可以将它添加到 body（全局） 或者 div（局部） 元素 上进行展示

## 其他

### 改变物体材质，位置

使用对应的 set 方法：

```
mesh1.position.set(0, 60, 0)
intersects[0].object.material.color.set(0x000000)
```

### 改变物体半径

注意直接设置 geometry 的 radius 是不起作用的（虽然从控制台看见确实设置成功了），真正能看到的画面来自于 mesh ，因此需要对 mesh 进行设置：

```
mesh.scale.set(0x000000)
```

(原始文档中提到，geometry 实例化之后，对其参数的修改不会改变其样式)

### 修改位置信息

```
mesh.position.set(x, y, z);
```

### 修改物体旋转角度

```
mesh.rotation.x = rotateX;
mesh.rotation.y = rotateY;
mesh.rotation.z = rotateZ;
```

### 修改物体是否可见

```
material.visible = false; // 这里最好设置下material的transparent为true
```

### MeshBasicMaterial 与 MeshPhongMaterial

#### MeshBasicMaterial

这是一种简单材质，**不会考虑环境场景中光照的影响**，这种材质的网格会被渲染成简单的平面多边形，也可以显示几何体的线框，对场景的雾化会有反应，主要属性：

- color：颜色
- wireframe：渲染成线框，适合调试
- wireframeLinewidth：线框宽度
- fog：雾化，true或者 false，设置是否受到雾化影响

#### MeshPhongMaterial

可以创建一种光亮的材质，可以模拟带有高光的闪亮表面，例如漆木，主要属性：

- color：颜色
- shininess：光滑度，指定高光部分的亮度默认为30
- specular：材料高光部分的颜色，默认为0x111111深灰色，如果设置为与color一样，会得到类似金属一样的材质，设置为灰色则看起来更像塑料
- wrapAround：true 或者 false，使用这个lambert光照技术，光下降的更微妙，如果网格有粗糙、黑暗的地区， 启用后这个属性阴影将会变得柔和并且分布的更加均匀 

> 参考：https://blog.csdn.net/youlinhuanyan/article/details/104344383

### 获取点击位置的坐标

Three js 有个 bug， 在点击物体之后，如果直接使用检测到的物体输出其坐标：

```
intersects[0].object.position
```

会发现输出的结果始终为 0，如果想要获得点击位置的真实三维坐标，要使用 .point 方法获得坐标：

```
intersects[0].point.x
```

