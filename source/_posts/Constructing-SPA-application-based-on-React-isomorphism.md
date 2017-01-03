---
title: Constructing-SPA-application-based-on-React-isomorphism
date: 2017-01-03 10:52:02
tags:
  - react
---
本文将讲解如何构建基于React同构的SPA应用，涉及到的技术或知识点有如下若干(按字母排序)：

> babel

> fetch

> intl

> isomorphic

> koa

> material-ui

> postcss

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

    npm install babel-cli babel-core babel-loader babel-plugin-transform-class-properties babel-plugin-transform-decorators-legacy babel-plugin-transform-runtime babel-preset-latest babel-preset-react babel-preset-stage-0 classnames co-views copy-webpack-plugin core-decorators css-loader extract-text-webpack-plugin file-loader ip isomorphic-fetch isomorphic-style-loader json-loader koa@next koa-favicon@next koa-locale koa-router@next koa-static@next material-ui normalize.css offline-plugin postcss postcss-cssnext postcss-import postcss-loader postcss-nested react react-dom react-hot-loader react-intl react-intl-redux react-motion react-redux react-router react-tap-event-plugin redux redux-logger redux-observable reselect rxjs socket.io socket.io-client spdy style-loader swig universal-webpack url-loader webpack webpack-node-externals --save

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
      "postcss-import": "^8.2.0",
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

