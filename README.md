# vue-map

> A Vue.js project
> npm版本1.0.2

## 1.配置

在index.html中引入
```javascript
<script type="text/javascript" src="http://webapi.amap.com/maps?v=1.4.7&key=e7e6678466c0f99679aa8fe87f1d9783"></script>
```

在webpack.base.conf.js中加入
```javascript
externals: {
  'AMap': 'AMap'
}
```

然后执行

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```

页面路由
http://localhost:8080/#/map

## 2.使用API

### 1.属性
| 属性名     |  说明 | 类型   |  默认  |
| --------   | -----:  | -----:  | :----:  |
| center     | 地图初始化中心点 | array |   null     |
| position   | 地图地理位置（如：杭州市余杭区，杭州市黄龙） |   string   |   ''   |

### 2.方法
| 方法名        | 说明   |  回调参数  |
| --------   | -----:  | :----:  |
| cityChange     | 点击地图进行标点 |   点击地图后返回对应标点的信息（如：省，市，区，街道等等）    |
| position        |   点击地图进行标点   |   点击地图后返回对应标点的经纬度   |
