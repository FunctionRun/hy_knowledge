# 可视化库中的图表制作流程

--------------------------------------------------------------------------------

## 可视化库的整体设计思路

ycharts的定位是能够快速构建可复用的满足特定需求的定制化图表，于echarts和hcharts类似， 都是采用配置参数加上轻量级API接口的方式来制作图表；图表库在整体上采用MVC设计模式，外加事件和行为的管理等；

--------------------------------------------------------------------------------

## 可视化库中的类关系

![Ycharts的类关系图](./images/assets/ycharts_class.svg)

### 继承关系阐释

可视化库采用的是类似MVC的设计模式，因此代码组织也是严格按照该设计模式来进行的；这里主要说明的是M和V层的继承关系；

#### M层的继承关系

1. M层的基类是Model类,位于src/model/Model.js文件中，通常不允许直接实例化该类；
2. GlobalModel继承自Model,直接对应图表的M层，GlobalModel位于src/Model/GlobalModel.js文件中；
3. ComponentModel也继承自Model，是图表内部的组件的M层,ComponentModel位于src/Model/ComponentModel.js文件中；
4. SeriesModel继承自ComponentModel,它主要对应于图表中的绘制图形的部分的M层，也是为了与组件的M层的类进行区分；
5. 在src/model/GlobalDefault.js保存这全局的默认配置参数，主要是为应对参数缺失的情况，能够为整个图表提供较好的默认行为和样式等；

#### V层的继承关系

1. V层并没有提供类似于M的基类；
2. Chart类位于src/view/chart.js文件中，是图表绘制部分的V层；
3. Component类位于src/view/component.js文件中，对应图表中组件的V层;

#### 继承关系的图示

![曲线图的不同组成部分以及继承管子](./images/assets/曲线图例.png)

--------------------------------------------------------------------------------

## 作图规范

1. 图表的目录结构规范

  - 图表都放置在Ycharts/src/charts目录下，图表各自都有对应的目录；
  - 图表都必须有对应的Model和View类，比如barSerie.js和barView.js;

2. 图表的M层规范

  - 图表的M层都必须满足上面的类关系图的继承关系;
  - 图表都必须指定type，dependencies, defaultOption的参数；

    - type: String类型，表示图表的类型, 比如'serie.bar'；
    - dependencies: Array类型，主要表示组件的依赖关系，形如['grid', 'xAxis', 'yAxis']；
    - defaultOption: Object类型，图表的serie部分的默认参数；

3. 图表的V层规范

  - 图表的V层都必须满足上面的类关系图的继承关系;
  - 图表都必须实现render方法，主要是用来绘制具体的图表；

  --------------------------------------------------------------------------------

## 作图流程

### 文件结构

按照图表的规范，新建相关文件夹和文件，每个图表都有M、V层；如下图的目录结构所示，其中xxxSeries.js是图表绘图区域部分的M层文件，而xxxView.js是图表绘图区域的V层文件;

文件结构如下：

```
  ├── index.js
  ├── bar
  │   ├── barSeries.js
  │   └── barView.js
  ├── force
  │   ├── forceSeries.js
  │   └── forceView.js
```

### 图表的M层

图表的M层都继承自SeriesModel类

```javascript
    //　从src/model/SeriesModel.js中引入SeriesModel类
    import SeriesModel from '../../model/SeriesModel';

    // 每个绘图区域都继承自SeriesModel
    // 而export default的导出该类，以便其他模块能够引用
    export default class XxxModel extends SeriesModel {

    }
```

1. 首先调用父类构造器

  ```javascript
  /**
  * [render description]
  * @param  {Object} opt         从应用中传入的配置参数
  * @param  {Object} parentModel 该参数暂时还没实际应用
  * @param  {Object} globalModel 整个图表的M层，是GlobalModel类的实例
  * @param {String} id           每个model都有唯一对应的id标识
  */
  super(opt, parentModel, globalModel, id);
  ```

2. 为图表的M层指定type，主要是用来标示图表的类别

  ```javascript
  this.type = 'xxx';
  ```

3. dependencies的主要目的是标示该图表类型的组件依赖关系，以便开发或者使用人员能够正确的满足这种依赖关系

  ```javascript
  // 指定绘图的组件依赖，比如绘制柱状图需要依赖网格,横纵坐标轴组件
  this.dependencies = ['grid', 'xAxis', 'yAxis'];
  ```

4. defaultOption标示该图表的默认配置参数，主要是在部分配置参数不全的时候，能够正确初始化图形

  ```javascript
  // 绘图部分的默认配置参数
  this._defaultOption = {};
  ```

5. 合并默认配置参数到option

  ```javascript
  // 合并用户传入的参数和默认参数，避免参数缺失的情况
  this.option = baseUtil.merge(this.option, this._defaultOption);
  ```

6. 生成依赖组件model

  ```javascript
  // 该部分的操作主要是根据组件依赖的关系，将对应的依赖组件的model保存在数组中
  this.dependenciesModel = null  

  // 该方法是SeriesModel类中的方法
  this.createDependencesModel()
  ```

#### 实例

这里提供绘制柱状图的M层的例子；

```javascript
  import SeriesModel from '../../model/SeriesModel';
  import baseUtil from '../../utils/baseUtil';

  export default class XxxModel extends SeriesModel {  
    constructor(opt, parentModel, globalModel, id) {
     // 调用父类的构造器函数
     super(opt, parentModel, globalModel, id);

     // 指定图表的类别
     this.type = 'xxx';

     // 指定series的组件依赖关系
     this.dependencies = ['grid', 'xAxis', 'yAxis'];

     // 指定series的默认配置参数
     this._defaultOption = {
       barWidth: 10
     };

     //合并默认配置到option,
     this.option = baseUtil.merge(this.option, this._defaultOption);

     //生成依赖组件model
     this.dependenciesModel = null;
     this.createDependencesModel();
    }
  }
```

### 图表的V层

图表的V层都继承自Chart类

```javascript
  // 从src/view/chart.js引入Chart类
  import Chart from '../../view/chart';

  // 图表继承自Chart类
  export default class Bar extends Chart {

  }
```

1. 图表构造器中调用父类构造器

  ```javascript
  /*
  * @param {Object} model 对应图表绘图部分的model,其中保存了各种配置参数和数据
  */
  constructor(model) {
    super(model);

    // 创建labels的容器元素
    this.labels = this._createSubWrapElement(['labels']);
  }
  ```

2. 构造器初始化需要的参数

  - 调用父组件Chart类中的方法_createSubWrapElement,该方法主要用于为不同的svg元素分组创建容器元素,在svg中就是g元素,目的是为了将不同的svg元素分类,便于管理;<br>
    ![柱状图的各种元素容器](./images/assets/元素容器的图示.png)

    ```javascript
    // 分别为连接和节点创建容器元素
    this.linkItems = this._createSubWrapElement(['items', 'link', 'line'])
    this.nodesItems = this._createSubWrapElement(['items', 'nodes', 'circle'])

    // 为label创建容器元素
    this.labels = this._createSubWrapElement(['labels']);
    ```

  - 初始化组件绘制需要属性

3. 覆盖render方法

  render方法是绘制图表的核心,将最终决定图表的外观和部分行为;

  ```javascript
  /**
  * [render description]
  * @param  {ChartModel} model          当前series的Model
  * @param  {InstanceModel} globalModel 图表元素的Model
  * @param  {d3selection} wrap          d3对象, 通常是g元素, series在该节点下面绘制；
  * @param  {Object} instanceApi        获取最外层container元素的尺寸信息API；
  * @return {null}             [description]
  */
  render(model, globalModel, wrap, instanceApi) {

  }
  ```

4. render方法的编写原则

render方法在实际编写过程中, 其代码量相对较多, 因此我们提倡的方式将render的代码分割成不同的私有方法来完成,通常会定义以下方法:

  - print方法, 该方法主要绘制图表的主要部分;
  - calcLabelsLayout方法, 该方法主要计算图表上的label的位置信心;
  - 图表的制作这可以在根据实际需求创建新的方法;

  ```javascript
  render(model, globalModel, wrap, instanceApi) {
  // model获取等绘制前的准备

  // 调用print方法
  print();
  }

  // 图表的绘制方法
  print(subwrap) {

  const labelLayout = calcLabelsLayout();
  // 根据计算的layout信息绘制label
  }

  // 计算lable的布局信息
  calcLabelsLayout() {
  // 计算label的位置和标签信息
  return layout;
  }
  ```

5. render方法中如何访问不同的model

render在绘制过程需要取得自己和组件的model, 在globalModel中保存了图表的所有的model,但是好的实践是尽量不要在globalModel中获取组件的model;model的获取有如下规范:

- 自己的model在render方法的参数model中就可以获取, 该model中包含了图表绘制部分的数据和配置等信息;
- 依赖组件的model可以在自己的model的dependenciesModel数组中获取, 因此必须正确定义图表的组件依赖;
- 在上述两种情况都不能满足的时候, 需要考虑组件的依赖是否配置正确;

  ```javascript
   render(model, globalModel, wrap) {

     //访问xAxisModel
     const xAxisModel = model.dependenciesModel['xAxis'];

     //访问seriesIndex
     const seriesIndex = model.seriesIndex;

     //访问label组件v
     this._label.render(labelG, dataset, labelLayouts, outside);
   }
  ```

- render方法需要完成那些基本功能

  - 绘制所需要的图表, 在基本绘制完成的基础上尽量做到代码重用;
  - 计算label的位置, 在调用label的函数时作为参数传入;
  - 计算tooltip的位置;

6. render方法中如何调用绘制不同图形的接口

在render方法中,我们通常需要绘制各种基本图形, 比如rect, circle, path等, 为了避免重复的编写相同的代码, 在可视化库中已经做好了相应的绘制接口Graph; Graph的代码位于src/graph目录, 目录下的每种图形的调用接口都有如下的形式;

```javascript
/*
 * @param {Object} wrap  d3对象,g容器元素
 * @param {Array} dataset  tooltip显示的信息
 * @param {Array} shapeset label显示的信息
 * @param {Object} style  基本图形的样式
 */
Graph.Rect(wrap, dataset, shapeset, style, selector = 'series-item');
```
  - dataset的形式:

    ```javascript
        dataset = [{
            seriesIndex,
            dataIndex,
            data: Object,
            value: string || number,
            dataType: 'nodesData|linksData' //特殊图形是有意义
    }]
    ```
  - shapeset形式:

  ``` javascript
    shapeset = [{
      x,
      y,
      value: 数据值
    }...]
  ```

7. ```_createSubWrapElement```

``` javascript
   /*
  * @param  {Array} classes    容器要具有的类名：
                             如果是item元素类名： ['items', 'rect'] | ['items', 'circle'] |
   */
  _createSubWrapElement(classes);
```

8. 获取图表的labels容器元素

在图表的view上面有labels字段, 直接使用this.lables;

9. updateModel(父组件view/chart已经写了此方法作为共有方法、如果有比它特殊的方法,开发人员覆盖此方法)
10. dispose(销毁当前组件中的元素、当此组件被删除的时候调用)

#### 迪卡尔坐标系

迪卡尔坐标系的实例参加src/charts/barView.js文件;

#### 地图类型图表

地图类型的图表独立于二维迪卡尔坐标系, 通常在设计地图类型的图表的时候, 图表的组件要依赖于geo组件;

##### geo组件的说明

geo组件类似于迪卡尔坐标系下的坐标轴组件, 其主要的目的有两个:

1. 为绘制地图类型的图表提供指定的地图场景,比如中国地图;
2. 为绘制地图类型的图表提供比例尺, 能够将经纬度的信息转换成二维坐标信息;

![地图组件](./images/assets/地图组件.png)

地图组件依赖visualMap组件,visualMap组件主要提供数据到视觉实体之间转换的比例尺,比如将数值类型的数据映射为地图上面的circle的半径的大小;

visualMap组件提供能以下比例尺:

1. symbolSize比例尺: 将数据信息转换为地图上面symbol的大小;
2. color比例尺: 将数据信息转换为地图上面的symbol的颜色;
3. opacity比例尺: 将数据信息转换为地图上面的symbol的透明度;

#### 绘制地图类型的图表

在绘制地图类型的图表的时候, 可以通过组件依赖链取得visualMap取得;

``` javascript
// 取得当前的seriesIndex
const seriesIndex = model.seriesIndex;

// 取得geo组件的model
const geoModel = this.dependenciesModel['geo'];

// 取得visualMap的组建的model
const visualMapModel = this.dependenciesModel['visualMapModel'];

// 根据seriesIndex取得scaleMap
const scaleMap = visualMapModel.scaleMap[seriesIndex];
```

在绘制地图的时候,经常需要根据数据值的大小来改变地图上的symbol的尺寸大小,颜色和透明度等信息,这就需要使用visualMap组件中提供的不同的比例尺;

``` javascript
// 获取所有的比例尺
const scaleMap = visualMapModel.getScale();

// 分别取得尺寸,颜色和透明度比例尺
const symbolScale = scaleMap['symbolScale'];
const colorScale = scaleMap['color'];
const opacityScale = scaleMap['opacity']

// 使用比例尺
rectStyle.normal.fill = d => {
  return colorScale(d.value);
}
```

地图的绘制还需要投影转换的函数,也就是在geoModel中取得projection;至于如何使用projection, 参考
[d3的参考文档](https://github.com/d3/d3/wiki/Geo-Projections#projection):

```javascript
const projection = geoModel.projection;
```

#### 添加测试用例

新建图表后，都要为其提供完善的测试用例，主要的目的是有以下几个方面：

1. 测试新图表是否能够通过配置参数，完成图表的绘制，以及是否有bug；
2. Ycharts/Test/container下新建测试用例，然后在该文件夹下的index.js中引入该测试用例；
3. 在Ycharts/routes.js中添加路由规则;
4. 在Ycharts/Test/components/Nav/Nav.js添加；

测试的目录结构：

```
├── components
│   └── Nav
│       ├── Nav.css
│       └── Nav.js
└── container
    ├── index.js
    ├── App
    │   └── App.js
    ├── Bar
    │   └── Bar.js
    ├── Force
    │   └── Force.js
```
