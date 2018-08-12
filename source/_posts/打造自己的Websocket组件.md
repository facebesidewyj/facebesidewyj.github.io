---
title: 打造自己的Websocket组件
date: 2018-07-15 22:01:24
categories: JS实战
tags:
     - JavaScript
description: 打造自己的Websocket组件
---
# Websocket详解

## 定义

WebSocket一种在单个 TCP 连接上进行全双工通讯的协议，它由通信协议和编程API组成，WebSocket能够在浏览器和服务器之间建立双向连接，以基于事件的方式，赋予浏览器实时通信能力。既然是双向通信，就意味着服务器端和客户端可以同时发送并响应请求，而不再像HTTP的请求和响应。

## 优点及适用场景

1. 优点：

   * 跨域
   * 更好的二进制支持。Websocket定义了二进制帧，可以更轻松地处理二进制内容
   * 实时性
   * 长连接

2. 适用场景：

   * 聊天
   * 文字直播
   * 实时消息推送
   * 数据实时更新

## API

1. 创建Websocket对象：`let ws = new Websocket('ws://localhost:8080')`
2. 4个事件对象：

	```javascript
	  // 当websocket连接建立成功时
	  ws.addEventListener('open', () => {
	      console.log('websocket 建立连接')
	  })
	
	  // 当收到服务端的消息时
	  ws.addEventListener('message', (event) => {
	      console.log('获得数据', event.data)
	  })
	
	  // 当websocket关闭时
	  ws.addEventListener('close', (event) => {
	      console.log("websocket 连接关闭")
	  })
	
	  // 当出现错误时
	  ws.addEventListener('error', (event) => {
	      console.error("websocket 出现错误")
	  })
	```
3. 2个方法：
  
	```javascript
	 // 发送消息 
	 ws.send('hello websocket')
	
	 // 关闭socket
	 ws.close()
	```
## 封装一个socket组件

```javascript
'use strict'
/**
 * websocket请求封装
 * @class
 */
class Socket {
  /**
   * @constructs Socket
   * @param {String} url 创建地址
   * @param {Boolean} isNeedReconnect 是否需要断开重连机制
   * @param {Number} reconnectLimit 重连次数上限
   * @param {Function} openCallback 连接建立成功回调函数
   * @param {Function} messageCallback 监听消息回调函数
   * @param {Function} closeCallback 关闭连接回调函数
   * @param {Function} errorCallback 异常回调函数
   */
  constructor({ url, isNeedReconnect, reconnectLimit, openCallback, messageCallback, closeCallback, errorCallback }) {
    this.url = url
    this.isNeedReconnect = isNeedReconnect
    this.reconnectLimit = reconnectLimit || 7
    this.openCallback = openCallback
    this.messageCallback = messageCallback
    this.closeCallback = closeCallback
    this.errorCallback = errorCallback
    this.socket
    // 重连标识，避免重复重连
    this.isInReconnect = false
    // 重连次数
    this.reconnectCount = 0
    this.createSocket({ url: this.url })
  }
  /**
   * 初始化websocket
   * @param {String} url 创建地址
   */
  createSocket({ url }) {
    try {
      this.initListeners()
      this.socket = new WebSocket(url)
      this.initEventMonitor()
    } catch (e) {
      console.error('WebSocket创建连接失败')
    }
  }
  /**
   * 初始化ws事件监听
   */
  initEventMonitor() {
    this.onOpen()
    this.onMessage()
    this.onClose()
    this.onError()
  }
  /**
   * 建立连接函数
   */
  onOpen() {
    this.socket.addEventListener('open', () => {
      // 一旦建立成功，重置重连次数
      this.reconnectCount = 0
      this.stopHeartBeat()
      this.startHeartBeat()
      this.clearAll()
      // 建立成功标识
      this.trigger('websocketReady')
      if (this.openCallback) {
        this.openCallback(event)
      }
    })
  }
  /**
   * 接收数据函数
   */
  onMessage() {
    this.socket.addEventListener('message', (event) => {
      this.refreshServerTimer()
      if (this.messageCallback) {
        this.messageCallback(event.data)
      }
    })
  }
  /**
   * 关闭处理函数
   */
  onClose() {
    this.socket.addEventListener('close', (event) => {
      console.error('WebSocket连接关闭，状态码：' + event.code)
      if (this.closeCallback) {
        this.closeCallback(event)
      }
      // 需要重连并且重连次数小于等于重连上限
      if (this.isNeedReconnect && this.reconnectCount <= this.reconnectLimit) {
        this.reconnect()
      }
    })
  }
  /**
   * 错误处理函数
   */
  onError() {
    this.socket.addEventListener('error', (event) => {
      console.error('WebSocket in error')
      if (this.errorCallback) {
        this.errorCallback(event)
      }
      this.doClose({ isNeedClose: false })
    })
  }
  /**
   * 发送消息
   * @param {Object} params 参数对象
   */
  sendMessage(params) {
    // 发送时先判断连接是否关闭
    if (this.isSocketCloseing() || this.isSocketClose()) {
      // 需要重连，并且连接次数已经超过了上限（之前就触发过自动重连，并且在上限之内没有连接上）
      if (this.reconnectCount > this.reconnectLimit && this.isNeedReconnect) {
        // 重置连接次数
        this.reconnectCount = 0
        this.reconnect()
      }
    }

    // 如果已经成功的建立了链接，则直接发送，否则监听websocketReady成功再发送
    if (this.isSocketOpen()) {
      this.socket.send(JSON.stringify(params))
      console.log('发送参数', params)
    } else {
      // 监听websocketReady事件，发送消息
      this.on('websocketReady', () => {
        this.socket.send(JSON.stringify(params))
        console.log('发送参数', params)
      })
    }
  }
  /**
   * 重连机制
   */
  reconnect() {
    if (this.isInReconnect) {
      return
    }
    this.isInReconnect = true
    setTimeout(() => {
      // url生成一个参数标识告知服务端是重连的请求（服务端可以不处理）
      this.createSocket({ url: this.urlAddParams({ url: this.url, key: 'reconnect', val: 'true' }) })
      // 重连次数加1
      this.reconnectCount++
      this.isInReconnect = false
    }, 2000 * this.reconnectCount)
  }
  /**
   * 关闭socket
   * @param {Boolean} isNeedClose 是否需要真的关闭socket
   */
  doClose({ isNeedClose }) {
    // 判断是否关闭标识来决定是否需要重连
    this.isNeedReconnect = isNeedClose ? false : true
    clearTimeout(this.serverHeartBeatTimer)
    this.stopHeartBeat()
    this.socket.close()
  }
  /**
   * 开始心跳机制(15秒发送一次ping)
   */
  startHeartBeat() {
    this.heartBeatTimer = setInterval(() => {
      this.socket.send('ping')
    }, 15000)
  }
  /**
   * 停止心跳机制
   */
  stopHeartBeat() {
    clearInterval(this.heartBeatTimer)
  }
  /**
   * 超时掉线计时（1分钟超时）
   */
  refreshServerTimer() {
    clearTimeout(this.serverHeartBeatTimer)
    this.serverHeartBeatTimer = setTimeout(() => {
      this.doClose({ isNeedClose: false })
    }, 60000)
  }
  /**
   * 检查socket是否正在连接
   * @return {Boolean} 是否正在连接
   */
  isSocketConnecting() {
    return this.socket.readyState === WebSocket.CONNECTING
  }
  /**
   * 检查socket是否连接成功
   * @return {Boolean} 是否建立连接成功
   */
  isSocketOpen() {
    return this.socket.readyState === WebSocket.OPEN
  }
  /**
   * 检查socket是否正在关闭
   * @return {Boolean} 是否正在关闭
   */
  isSocketCloseing() {
    return this.socket.readyState === WebSocket.CLOSING
  }
  /**
   * 检查socket是否已经关闭
   * @return {Boolean} 是否已经关闭
   */
  isSocketClose() {
    return this.socket.readyState === WebSocket.CLOSED
  }
  /**
   * 动态拼接参数
   * @param {String} url 地址
   * @param {String} key 参数名
   * @param {String} val 参数值
   * @returns {String} 地址
   */
  urlAddParams({ url, key, val }) {
    return url.indexOf('?') !== -1 ? url + `&${key}=${val}` : url + `?${key}=${val}`
  }
  /**
   * 初始化监听器
   */
  initListeners() {
    this.listeners = {}
  }
  /**
   * 事件订阅
   * @param {Sting} eventName 事件名
   * @param {Function} callback 回调函数
   */
  on(eventName, callback) {
    if (this.listeners[eventName] && this.listeners[eventName].length) {
      if (this.listeners[eventName].indexOf(callback) === -1) {
        this.listeners[eventName].push(callback)
      }
    } else {
      this.listeners[eventName] = [callback]
    }
  }
  /**
   * 发布事件
   * @param {Sting} eventName 事件名
   */
  trigger(eventName) {
    if (this.listeners[eventName]) {
      for (let i = 0; i < this.listeners[eventName].length; i++) {
        // [].slice.call(arguments, 1) => 将arguments转换成数组
        this.listeners[eventName][i].apply(this, [].slice.call(arguments, 1))
      }
    }
  }
  /**
   * 重置监听器
   */
  clearAll() {
    let tmp = this.listeners['ready']
    this.listeners = {}
    this.listeners['ready'] = tmp
  }
}

export default Socket

```

