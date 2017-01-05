---
title: Constructing-SPA-application-based-on-React-isomorphism
date: 2017-01-03 10:52:02
tags:
  - react
---
本文将讲解如何构建基于React同构的SPA应用，涉及到的技术或知识点有如下若干(按字母排序，部分可选)：

> babel

> fetch

> http2

> intl

> isomorphic

> koa

> material-ui

> postcss

> pwa

> react

> redux

> rxjs

> socket.io

> webpack

> ...

### 项目地址

> https://github.com/devlee/react-isomorphic

### Step 1 创建项目并安装依赖包
新建文件夹myapp, 在此文件夹内建立如下项目结构：

    |-- config                      // *项目配置*
    |   |-- asset                   // 静态资源配置
    |   |-- intl                    // 语言包配置
    |   |-- server                  // 服务端配置
    |   |-- webpack                 // 前端工程配置
    |-- src                         // *项目源文件*
    |   |-- client                  // 前端代码
    |   |   |-- action              // redux.action
    |   |   |-- component           // 组件
    |   |   |-- container           // 页面
    |   |   |-- css                 // 样式
    |   |   |-- decorator           // 装饰器
    |   |   |-- epic                // redux-observable.epic
    |   |   |-- reducer             // redux.reducer
    |   |   |-- route               // 页面路由
    |   |   |-- selector            // reselect
    |   |   |-- service-worker      // pwa.sw
    |   |   |-- socket              // socket功能
    |   |   |-- store               // redux.store
    |   |-- server                  // 服务端代码
    |   |   |-- middleware          // 中间件
    |   |   |-- route               // api路由
    |   |   |-- socket              // socket功能
    |   |   |-- view                // 视图模板
    |   |-- universal               // 通用代码
    |-- static                      // *静态资源*

> 由于npm的普适性，本文讲述中不采用yarn，本文所有命令均在git bash中执行

执行以下命令初始化项目相关信息：

    npm init

执行以下命令安装项目依赖包：

    npm install babel-cli babel-core babel-loader babel-plugin-transform-class-properties babel-plugin-transform-decorators-legacy babel-plugin-transform-runtime babel-preset-latest babel-preset-react babel-preset-stage-0 classnames co-views copy-webpack-plugin core-decorators css-loader extract-text-webpack-plugin file-loader ip isomorphic-fetch isomorphic-style-loader json-loader koa@next koa-favicon@next koa-locale koa-router@next koa-static@next material-ui normalize.css offline-plugin postcss postcss-cssnext postcss-import@8.1.2 postcss-loader postcss-nested react react-dom react-hot-loader react-intl react-intl-redux react-motion react-redux react-router react-tap-event-plugin redux redux-logger redux-observable reselect rxjs socket.io socket.io-client spdy style-loader swig universal-webpack url-loader webpack webpack-node-externals --save

查看myapp下package.json文件可见(版本号可能不同)：

    ...
    "dependencies": {
      "babel-cli": "^6.18.0",
      "babel-core": "^6.0.0",
      "babel-loader": "^6.2.7",
      ...
      "url-loader": "^0.5.7",
      "webpack": "^1.13.3",
      "webpack-node-externals": "^1.5.4"
    },
    ...

依赖包详解

> [babel-cli](https://babeljs.io/docs/usage/cli/) babel脚手架可从命令行直接编译文件

> [babel-core](https://github.com/babel/babel/tree/master/packages/babel-core) babel核心包

> [babel-loader](https://github.com/babel/babel-loader) webpack插件，用于编译js

> [babel-plugin-transform-class-properties](https://babeljs.io/docs/plugins/transform-class-properties/) 用于格式化类的属性，比如react组件中写class，要写propTypes，该插件可以把这些多余的代码删除（确切是格式化，但效果就是删除）。

> [babel-plugin-transform-decorators-legacy](https://github.com/loganfsmyth/babel-plugin-transform-decorators-legacy) 这是一个用于babel6的插件，用于复制babel5的旧装饰器行为，以便允许人们更容易地转换到babel6，避免在更新装饰器出问题，或者为babel6重新实现一个装饰器。

> [babel-plugin-transform-runtime](https://babeljs.io/docs/plugins/transform-runtime/) 提供帮助类和内置类函数的外部引用，并自动修补代码且不污染全局环境。简而言之就是可以随意使用es6的各种特性，你不必关心用到了哪个，而从polyfill中去import相应的函数，该插件会自动处理好所有兼容问题。

> [babel-preset-latest](https://babeljs.io/docs/plugins/preset-latest/) 包含了es2015/es2016/es2017...所有特性

> [babel-preset-react](https://babeljs.io/docs/plugins/preset-react/) 用于转换jsx语法

> [babel-preset-stage-0](https://babeljs.io/docs/plugins/preset-stage-0/) 特性插件大集合，包含了stage-1/2/3...

> [classnames](https://github.com/JedWatson/classnames) 以js表达式的方式生成类名，用于jsx中写dom的class，当然某些class需要混合js逻辑的时候就很有用。

> [co-views](https://github.com/tj/co-views) koa模板引擎渲染平台，是对express通用模板引擎渲染平台 [consolidate](https://github.com/tj/consolidate.js) 的封装

> [copy-webpack-plugin](https://github.com/kevlened/copy-webpack-plugin) webpack插件，用于拷贝文件或目录

> [core-decorators](https://github.com/jayphelps/core-decorators.js) 基于stage0语法的未被js内部实现的装饰器，可用于react/angular。

> [css-loader](https://github.com/webpack/css-loader) webpack插件，用于打包css文件。

> [extract-text-webpack-plugin](https://github.com/webpack/extract-text-webpack-plugin) webpack插件，用于提取css代码到独立的文件中，而不是内嵌到html文件中。

> [file-loader](https://github.com/webpack/file-loader) webpack插件，用于将文件转换为路径。

> [ip](https://github.com/indutny/node-ip) 生成ip地址，主要用于开发环境时webpack的静态资源路径。

> [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) 同时用于node和browserify的fetch API。

> [isomorphic-style-loader](https://github.com/kriasoft/isomorphic-style-loader) webpack插件，用于同构应用处理css样式部分。

> [json-loader](https://github.com/webpack/json-loader) webpack插件，用于处理json文件，转换为object。

> [koa](http://koajs.com/) 下一代node框架，由原express团队打造，本身不包含任何中间件。

> [koa-favicon](https://github.com/koajs/favicon) koa中间件，用于设置favicon。

> [koa-locale](https://github.com/koa-modules/locale) koa中间件，用于从请求中获取locale信息以便设置intl语言代码。

> [koa-router](https://github.com/alexmingoia/koa-router/tree/master/) koa中间件，服务端路由设置。

> [koa-socket](https://github.com/mattstyles/koa-socket) koa中间件，封装了socket.io，实现了基于http服务的ws。

> [koa-sslify](https://github.com/turboMaCk/koa-sslify) koa中间件，对于http请求强制跳转https。

> [koa-static](https://github.com/koajs/static) koa中间件，提供静态文件服务。

> [material-ui](http://www.material-ui.com/) 实现了Google Material Design的react组件库。

> [normalize.css](https://necolas.github.io/normalize.css/) css resets。

> [offline-plugin](https://github.com/NekR/offline-plugin) webpack插件，基于service-worker等技术实现离线功能。

> [postcss](https://github.com/postcss/postcss) 使用js插件来处理css样式代码的工具。

> [postcss-cssnext](https://github.com/MoOx/postcss-cssnext) postcss插件，可以使用最新的css语法。

> [postcss-import](https://github.com/postcss/postcss-import) 用于处理@import，选用8.1.2的是因为更高版本新增了一个warning功能，这个待解决，但不影响功能。

> [postcss-loader](https://github.com/postcss/postcss-loader) webpack插件，用于使用postcss插件处理css。

> [postcss-nested](https://github.com/postcss/postcss-nested) postcss插件，使得css可以像sass一样进行嵌套书写。

> [react](https://facebook.github.io/react/) js框架。

> [react-dom](https://www.npmjs.com/package/react-dom) react部分api被拆分至此库。

> [react-hot-loader](https://github.com/gaearon/react-hot-loader) webpack插件，react热更新。

> [react-intl](https://github.com/yahoo/react-intl) react国际化库。

> [react-intl-redux](https://github.com/ratson/react-intl-redux) 封装了react-intl/react-redux等库。

> [react-motion](https://github.com/chenglou/react-motion) react动效解决方案。

> [react-redux](https://github.com/reactjs/react-redux) 官方的封装了react/redux的库。

> [react-router](https://github.com/ReactTraining/react-router) react路由。

> [react-tap-event-plugin](https://github.com/zilverline/react-tap-event-plugin) react插件，提供tap event支持。

> [redux](https://github.com/reactjs/redux) react状态容器。

> [redux-logger](https://github.com/evgenyrodionov/redux-logger) redux日志插件。

> [redux-observable](https://github.com/redux-observable/redux-observable) 用于redux里action的rxjs中间件。

> [reselect](https://github.com/reactjs/reselect) redux状态选择器，为不同的组件选择不同的状态。

> [rxjs](https://github.com/ReactiveX/rxjs) js响应式扩展，适用场景：1.异步操作重，2.同时处理多个数据源。

> [socket.io](http://socket.io/) 基于websocket的实时应用框架

> [socket.io-client](https://github.com/socketio/socket.io-client) socket.io的客户端

> [spdy](https://github.com/indutny/node-spdy) Google 开发的基于传输控制协议 (TCP) 的应用层协议 ，开发组正在推动 SPDY 成为正式标准（现为互联网草案）。SPDY协议旨在通过压缩、多路复用和优先级来缩短网页的加载时间和提高安全性。（SPDY 是 Speedy 的昵音，意思是更快）

> [style-loader](https://github.com/webpack/style-loader) webpack插件，将css添加到dom中。

> [swig](https://github.com/paularmstrong/swig) 模板引擎

> [universal-webpack](https://github.com/halt-hammerzeit/universal-webpack) webpack同构工具

> [url-loader](https://github.com/webpack/url-loader) webpack插件，对于需要处理的文件，会以url的方式来请求，可设置文件大小阀值，小于阀值则使用file-loader处理。

> [webpack](https://webpack.github.io/) 模块加载器

> [webpack-node-externals](https://github.com/liady/webpack-node-externals) webpack插件，用以node端不打包node_modules里的库

### Step 2 创建项目配置、通用代码、静态资源

项目配置 ./config/index.js

    const asset = require('./asset');
    
    const intl = require('./intl');
    
    const server = require('./server');
    
    const siteName = 'devlee.io';
    
    module.exports = {
      asset: asset,
      intl: intl,
      server: server,
      siteName: siteName
    };

输出各分类配置

客户端打包文件配置： ./config/asset/index.js

    module.exports = {
      development: {
        port: 8066,
        prefix: '/'
      },
      production: {
        port: 8066
      }
    };

输出不同开发环境下所需变量，这里有端口和路径相关的变量。

服务端应用程序配置： ./config/server/index.js

    module.exports = {
      development: {
        port: 80,
        ports: 443
      },
      production: {
        port: 80,
        ports: 443
      }
    };

> ./config/server/ssl下的devlee.io.crt和devlee.io.key为证书

intl国际化配置： ./config/intl/index.js

    const en = require('./en');
    const zh = require('./zh');
    
    module.exports = {
      en: en,
      zh: zh
    };

这里直接输出具体的语言包

英语： ./config/intl/en.js

    module.exports = {
      locale: 'en',
      messages: {
        nav: {
          home: 'Home',
          about: 'About',
          demo: 'Demo'
        }
      }
    };

中文： ./config/intl/zh.js

    module.exports = {
      locale: 'zh',
      messages: {
        nav: {
          home: '首页',
          about: '关于'
        }
      }
    };

> 中文代码最好区分zh-CN，zh-TW

webpack: ./config/webpack/index.js

    ...
    
    const webpackConfig = {
      context: rootFolder,
      resolve: {
        extensions: [
          '',
          '.js',
          '.jsx',
          '.json'
        ]
      },
      entry: {
        common: [
          'normalize.css',
          './src/client/css/font-icons/style.css',
          './src/client/css/common.pcss'
        ],
        app: [
          './src/client/index.jsx'
        ]
      },
      output: {
        publicPath: assetPath,
        path: path.resolve(rootFolder, './build'),
        filename: '[name].js',
        chunkFilename: '[name].js'
      },
      module: {
        loaders: [
          {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            loader: 'babel'
          },
          {
            test: /\.json$/,
            loader: 'json'
          },
          {
            test: /\.css$/,
            loader: extractTextWebpackPlugin.extract(
              'style',
              'css?-autoprefixer'
            )
          },
          {
            test: /\.pcss$/,
            loader: extractTextWebpackPlugin.extract(
              'isomorphic-style',
              'css?modules&localIdentName=[hash:base64:5]&-autoprefixer&importLoaders=1!postcss'
            )
          },
          {
            test: /\.(jpg|jpeg)$/i,
            loader: 'file'
          },
          {
            test: /\.(ico|gif|png|woff|woff2|eot|ttf|svg)$/i,
            loader: 'url'
          }
        ]
      },
      plugins: [
        new webpack.optimize.CommonsChunkPlugin({
          name: 'common'
        }),
        /* eslint-disable new-cap */
        new extractTextWebpackPlugin('[name].css'),
        new webpack.DefinePlugin({
          'process.env': {
            NODE_ENV: JSON.stringify(env),
            PWA: JSON.stringify(pwa)
          }
        })
      ],
      devServer: {
        host: '0.0.0.0',
        port: assetConfigPort
      },
      externals: {
        react: 'React',
        'react-dom': 'ReactDOM',
        'react-router': 'ReactRouter'
      },
      regular_expressions: {
        javascript: /\.(js|jsx)$/,
        styles: /\.(css|pcss)$/
      },
      postcss: () => {
        return [
          postcssImport(),
          postcssCssNext({ browsers: ['> 0%'] }),
          postcssNested()
        ];
      }
    };
    
    module.exports = webpackConfig;

webpackConfig属性详解：

> context: 编译的文件相对本文件所在目录的相对路径，简单来说相对本配置文件将其设置为项目根目录就可了。本文件路径 ./config/webpack/index.js，根目录相对本文件的目录就是 path.resolve(__dirname, '../..')

> resolve: 需要解决的模块，就是需要编译处理的文件类型。

> entry: 入口文件

> output: 编译输出文件

> module.loaders: 各种模块loader集合

> plugins： 插件，这里依此使用了提取公共代码的插件、 提取style到css文件中的插件、 全局变量插件

> devServer: 开发服务器配置

> externals: 这部分库不进行编译打包

> regular_expressions: UniversalWebpack配置需要

> postcss: postcss插件配置

webpack.setting: ./config/webpack/setting.js

    module.exports = {
      server: {
        input: './src/server/index.js',
        output: './build/server/index.js'
      }
    };

该文件配置服务端代码编译入口及出口文件路径。

webpack.client: ./config/webpack/client.js

    ...
    
    delete webpackConfig.externals;
    
    webpackConfig.plugins = webpackConfig.plugins || [];
    
    webpackConfig.plugins.push(
      new webpack.DefinePlugin({
        'process.env': {
          CLIENT: JSON.stringify(true)
        }
      })
    );
    
    if (pwa) {
      webpackConfig.plugins.unshift(
        new CopyWebpackPlugin([{
          from: path.resolve(rootFolder, './static'),
          to: path.resolve(rootFolder, './build')
        }])
      );
      webpackConfig.plugins.push(
        new OfflinePlugin({
          ServiceWorker: {
            navigateFallbackURL: '/'
          }
        })
      );
    }
    
    module.exports = webpackConfig;

客户端webpack配置在基础配置之上做一些处理：
（可选）这里删除externals是为了开发所需，实际上不删除也可以，只需要在html中加入相应库的cdn地址就可以了；
（必须）新增全局变量process.env.CLIENT为true
（可选）PWA模式下需要把static下的文件拷贝至build目录下，因为该模式下服务端会把build目录作为静态资源服务的目录；
（可选）PWA模式下需要设置OfflinePlugin，对于未命中sw的路径会跳转至根路径。

webpack.server: ./config/webpack/server.js

    delete webpackConfig.externals;
    
    webpackConfig = universalWebpack.serverConfiguration(webpackConfig, settings);
    
    webpackConfig.node = {
      __dirname: true,
      __filename: true
    };
    
    webpackConfig.externals = [webpackNodeExternals()];
    
    module.exports = webpackConfig;

服务端webpack配置在基础配置之上做一些处理：
（可选）这里删除externals其实没必要，下面会重写
（必须）利用UniversalWebpack生成新的服务端配置
（必须）UniversalWebpack默认的node属性里的属性值为false，我们这按需要必须要要设置为true，这样不会导致路径出问题。
（必须）利用webpackNodeExternals插件过滤掉node_modules里的库，避免打包

通用代码： ./src/universal

cookie处理: ./src/universal/cookie.js

    ...
    
    export function setCookie(name, value, option, ctx) {
      // server
      if (ctx) {
        return ctx.cookies.set(name, value, option);
      }
    
      // client
      ...
    
      document.cookie = str;
    }
    
    export function getCookie(name, ctx) {
      // server
      if (ctx) {
        if (!name) {
          return ctx.cookies;
        }
    
        return ctx.cookies.get(name);
      }
    
      // client
      if (!name) {
        return parse(document.cookie);
      }
    
      return parse(document.cookie)[name];
    }
    
    export function clearCookie(name, option, ctx) {
      // server
      if (ctx) {
        return ctx.cookies.set(name, null, option);
      }
    
      // client
      setCookie(name, null, option);
    }

输出三个函数分别为设置、获取、清除cookie，最后一个参数适用于服务端koa

域名设置: ./src/universal/domain.js

    import { env } from './env';
    
    let defDomain = 'localhost';
    
    defDomain = (env === 'production' ? 'devlee.io' : defDomain);
    
    const domain = defDomain;
    
    export default domain;

根据环境输出当前域名

环境变量: ./src/universal/env.js

    const pwa = process.env.PWA;
    
    export const env = process.env.NODE_ENV || 'development';
    
    export const isPwa = String(pwa) === 'true';
    
    export const isDev = String(env) === 'development';
    
    export const isClient = String(process.env.CLIENT) === 'true';

输出当前环境变量名、是否为PWA模式、是否为开发模式、是否是客户端环境

intl国际化相关变量: ./src/universal/intl.js

    import en from 'react-intl/locale-data/en';
    
    import zh from 'react-intl/locale-data/zh';
    
    import config from '../../config';
    
    const intlConfig = config.intl;
    
    export const intlList = ['en', 'zh'];
    
    export const intlPack = intlConfig;
    
    export const intlData = {
      en,
      zh
    };

输出国际化语言列表，语言包列表、react-intl提供的相关data数据

socket日志记录: ./src/universal/socket-log.js

    import { isClient } from './env';
    
    const env = isClient ? 'client' : 'server';
    
    const prefix = `[socket.io-${env}] `;
    
    const log = (ctx) => {
      if (ctx) {
        console.log('>>>');
        console.log(`${prefix}EventType: ${ctx.type}`);
        console.log(`${prefix}EventName: ${ctx.event}`);
        console.log(`${prefix}Data Begin:`);
        console.log(' ');
        console.log(ctx.data);
        console.log(' ');
        console.log(`${prefix}Data End.`);
        console.log('<<<');
      }
    };
    
    export default log;

输出socket日志函数，用于on/emit时的记录

静态资源： ./static

服务端用favicon.ico
PWA用favicon.png
PWA用index.html

    <!DOCTYPE html>
    <html>
      <head>
        <title>React Isomorphic Seed</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta name="theme-color" content="#00bcd4">
        <link rel="icon" href="/favicon.png">
        <link rel="apple-touch-icon" href="/favicon.png">
        <link rel="manifest" href="/manifest.json">
        <link rel="canonical" href="https://devlee.io" />
        <link rel="stylesheet" href="/common.css">
        <link rel="stylesheet" href="/app.css">
      </head>
      <body>
        <div id="app"></div>
        <noscript>
          devlee.io
        </noscript>
        <script src="/common.js"></script>
        <script src="/app.js"></script>
      </body>
    </html>

PWA用manifest.json

    {
      "name": "React Isomorphic PWA",
      "short_name": "react pwa",
      "icons": [{
        "src": "favicon.png",
        "type": "image/png",
        "sizes": "192x192"
      }],
      "start_url": "/?utm_source=homescreen",
      "display": "standalone",
      "background_color": "#ffffff",
      "theme_color": "#00bcd4"
    }

### Step 3 创建前后端入口文件

client: ./src/client/index.jsx

    ...
    
    window.onload = () => {
      /* eslint-disable no-underscore-dangle */
      if (isPwa) {
        sw.init();
      }
    
      socket.init();
    
      const store = configureStore(
        reducer,
        window.__INITIAL_STATE__
      );
    
      const state = store.getState();
    
      injectTapEventPlugin();
    
      render(
        <Provider store={store}>
          <Router history={browserHistory}>
            {route(state)}
          </Router>
        </Provider>,
        document.getElementById('app')
      );
    };

前端入口文件，我们需要对相关功能作初始化操作，包括service-worker(PWA mode)，socket，redux store, material-ui tapEvent, react render。
（可选）如果当前是PWA模式，那么需要执行sw的init，来初始化service worker;
（可选）socket直接执行init方法即可；
（可选）如果使用redux，则需要store配置，需要reducer和初始状态集window.__INITIAL_STATE__，该值由服务端渲染到页面的script中；
（可选）如果使用redux，则需要获取state，利用store的getSate方法获得；
（可选）如果使用material-ui，则必须执行injectTapEventPlugin方法来注入tagEvent；
（可选）如果使用intl，Provider从react-intl-redux引入，否则从react-redux引入。
（可选）如果使用react-router，则需配置history和route
（必须）render方法必须，第一个参数是组件，第二个参数是挂载的dom对象

> PWA模式：[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)

server: ./index.js

    const UniversalWebpack = require('universal-webpack');
    
    const config = require('./config/webpack');
    
    const settings = require('./config/webpack/setting');
    
    UniversalWebpack.server(config, settings);

该文件为服务端启动文件，即可通过node命令直接运行的。UniversalWebpack会通过webpack的配置config和服务端的设置settings来启动服务。

server: ./src/server/index.js

    ...
    
    export default () => {
      let httpsApp;
      const httpApp = new Koa();
      const serverConfig = config.server;
      let app;
      let options;
    
      if (isPwa) {
        httpsApp = new Koa();
        app = httpsApp;
        options = {
          key: fs.readFileSync(path.resolve(rootFolder, './config/server/ssl/devlee.io.key')),
          cert: fs.readFileSync(path.resolve(rootFolder, './config/server/ssl/devlee.io.crt'))
        };
        httpApp.use(ctx => {
          ctx.status = 301;
          ctx.redirect(`https://${ctx.hostname}:${serverConfig[env].ports}${ctx.path}${ctx.search}`);
          ctx.body = 'Redirecting to https';
        });
      } else {
        app = httpApp;
      }
    
      middleware.intl(app);
    
      app.use(middleware.error)
         .use(middleware.ssl)
         .use(middleware.favicon)
         .use(middleware.static)
         .use(middleware.helper)
         .use(middleware.navigator)
         .use(middleware.view)
         .use(router.routes())
         .use(router.allowedMethods());
    
      if (isPwa) {
        http.createServer(httpApp.callback()).listen(serverConfig[env].port, () => {
          console.log(`http app start at port ${serverConfig[env].port}`);
        });
        const httpsServer = spdy.createServer(
          options,
          httpsApp.callback()
        );
        middleware.io(httpsServer);
        httpsServer.listen(serverConfig[env].ports, () => {
          console.log(`https app start at port ${serverConfig[env].ports}`);
        });
      } else {
        middleware.io(app);
        app.listen(serverConfig[env].port, () => {
          console.log(`http app start at port ${serverConfig[env].port}`);
        });
      }
    };

该文件为主要入口文件，由于需要被UniversalWebpack所使用，所以必须export一个function，在function中写相关代码。
PWA模式下会启动http和spdy两种服务，spdy（强制https，支持http2），这里的http服务只做强制跳转https作用；
普通开发模式下只创建http服务；
两种模式下的koa实例都会加载中间件实现相应的功能，例如route，socket等。

