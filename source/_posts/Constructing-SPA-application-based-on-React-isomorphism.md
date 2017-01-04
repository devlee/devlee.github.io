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
      "babel-plugin-transform-class-properties": "^6.19.0",
      "babel-plugin-transform-decorators-legacy": "^1.3.4",
      "babel-plugin-transform-runtime": "^6.15.0",
      "babel-preset-latest": "^6.16.0",
      "babel-preset-react": "^6.16.0",
      "babel-preset-stage-0": "^6.16.0",
      "classnames": "^2.2.5",
      "co-views": "^2.1.0",
      "copy-webpack-plugin": "^4.0.1",
      "core-decorators": "^0.14.0",
      "css-loader": "^0.25.0",
      "extract-text-webpack-plugin": "^1.0.1",
      "file-loader": "^0.9.0",
      "ip": "^1.1.4",
      "isomorphic-fetch": "^2.2.1",
      "isomorphic-style-loader": "^1.1.0",
      "json-loader": "^0.5.4",
      "koa": "next",
      "koa-favicon": "next",
      "koa-locale": "^1.2.0",
      "koa-router": "next",
      "koa-socket": "^4.4.0",
      "koa-sslify": "next",
      "koa-static": "next",
      "material-ui": "^0.16.4",
      "normalize.css": "^5.0.0",
      "offline-plugin": "^4.5.3",
      "postcss": "^5.2.5",
      "postcss-cssnext": "^2.8.0",
      "postcss-import": "8.1.2",
      "postcss-loader": "^1.1.1",
      "postcss-nested": "^1.0.0",
      "react": "^15.4.1",
      "react-dom": "^15.4.0",
      "react-hot-loader": "^3.0.0-beta.6",
      "react-intl": "^2.1.5",
      "react-intl-redux": "^0.1.0",
      "react-motion": "^0.4.5",
      "react-redux": "^4.4.6",
      "react-router": "^3.0.0",
      "react-tap-event-plugin": "^2.0.1",
      "redux": "^3.6.0",
      "redux-logger": "^2.7.4",
      "redux-observable": "^0.12.2",
      "reselect": "^2.5.4",
      "rxjs": "^5.0.0-rc.5",
      "socket.io": "^1.7.2",
      "socket.io-client": "^1.7.1",
      "spdy": "^3.4.4",
      "style-loader": "^0.13.1",
      "swig": "^1.4.2",
      "universal-webpack": "^0.1.41",
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

### Step 2 创建前后端入口文件及webpack配置

client: /src/client/index.jsx

    import React from 'react';
    
    import { render } from 'react-dom';
    
    import { Router, browserHistory } from 'react-router';
    
    import { Provider } from 'react-intl-redux';
    
    import injectTapEventPlugin from 'react-tap-event-plugin';
    
    import configureStore from './store';
    
    import route from './route';
    
    import reducer from './reducer';
    
    import socket from './socket';
    
    import sw from './service-worker';
    
    import { isPwa } from '../universal/env';
    
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

server: /index.js

    const UniversalWebpack = require('universal-webpack');
    
    const config = require('./config/webpack');
    
    const settings = require('./config/webpack/setting');
    
    UniversalWebpack.server(config, settings);

该文件为服务端启动文件，即可通过node命令直接运行的。UniversalWebpack会通过webpack的配置config和服务端的设置settings来启动服务。

server: /src/server/index.js

    import Koa from 'koa';
    
    import fs from 'fs';
    
    import http from 'http';
    
    import spdy from 'spdy';
    
    import path from 'path';
    
    import middleware from './middleware';
    
    import router from './route';
    
    import config from '../../config';
    
    import { env, isPwa } from '../universal/env';
    
    const rootFolder = path.resolve(__dirname, '../..');
    
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
两种模式下的koa实例都会加载中间件实现响应的功能，例如route，socket等。

webpack: /config/webpack/index.js

    const path = require('path');
    
    const webpack = require('webpack');
    
    const ip = require('ip');
    
    const postcssImport = require('postcss-import');
    
    const postcssNested = require('postcss-nested');
    
    const postcssCssNext = require('postcss-cssnext');
    
    const extractTextWebpackPlugin = require('extract-text-webpack-plugin');
    
    const config = require('../../config');
    
    const rootFolder = path.resolve(__dirname, '../..');
    
    const env = process.env.NODE_ENV || 'development';
    
    const pwa = Boolean(process.env.PWA);
    
    const assetConfig = config.asset;
    
    const assetConfigPort = assetConfig[env].port;
    
    const assetConfigPrefix = assetConfig[env].prefix;
    
    const assetPath = pwa ? `${assetConfigPrefix}` : `//${ip.address()}:${assetConfigPort}${assetConfigPrefix}`;
    
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