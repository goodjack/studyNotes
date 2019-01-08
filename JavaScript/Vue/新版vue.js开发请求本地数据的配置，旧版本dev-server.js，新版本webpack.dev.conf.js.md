### 新版vue.js开发请求本地数据的配置，旧版本dev-server.js，新版本webpack.dev.conf.js

旧版本dev-server.js配置本地数据访问：

在const app = express()后，const compiler = webpack(webpackConfig)前配置，

``` javascript
var appData = require('../data.json')
var seller = appData.seller
var goods = appData.goods
var ratings = appData.ratings
var foods = appData.foods
var pice = appData.pice
var apiRoutes = express.Router()

apiRoutes.post('/foods', function (req, res) {
  res.json({
    errno: 0,
    data: foods
  });
})

apiRoutes.get('/seller', function (req, res) {
  res.json({
    errno: 0,
    data: seller
  });
})

apiRoutes.get('/goods', function (req, res) {
  res.json({
    errno: 0,
    data: goods
  })
})

apiRoutes.get('/ratings', function (req, res) {
  res.json({
    errno: 0,
    data: ratings
  });
})

apiRoutes.get('/pice', function (req, res) {
  res.json({
    errno: 0,
    data: pice
  });
})
app.use('/api',apiRoutes)
```

新版本webpack.dev.conf.js配置本地数据访问

在const portfinder = require('portfinder')后添加

```javascript
var appData = require('../data.json')//加载本地数据文件
var seller = appData.seller//获取对应的本地数据
var goods = appData.goods
var ratings = appData.ratings
```

```javascript
//然后找到devServer,在里面添加
before(app) {
  app.get('/api/seller', (req, res) => {
    res.json({
      errno: 0,
      data: seller
    })//接口返回json数据，上面配置的数据seller就赋值给data请求后调用
  }),
  app.get('/api/goods', (req, res) => {
    res.json({
      errno: 0,
      data: goods
    })
  }),
  app.get('/api/ratings', (req, res) => {
    res.json({
      errno: 0,
      data: ratings
    })
  })
} 
```

修改配置后重新启动运行命令：npm run dev，data.json数据不能少，根据data.json的位置修改上面的URL，通过localhost:8080/api/seller