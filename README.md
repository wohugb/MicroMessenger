MicroMessenger
==============

微信内置浏览器那些坑爹的事

## WeixinApi

微信内置浏览器常用接口

[zxlie/WeixinApi](https://github.com/zxlie/WeixinApi)

## angularjs的services

```js
'use strict';

angular.module('worldApp')
.factory('Wechat',function($document,$location,$window) {
    var Wechat = function(data){
      var data  = data || {};
      var url   = data.url ?  data.url  :$location.absUrl();
      var title = data.title? data.title:$document[0].title;
      var desc  = data.desc?  data.desc : url;
      var img   = data.img ?  data.img  : 'http://img.***.com/images/logo.png';
      var onBridgeReady = function(){
        WeixinJSBridge.on('menu:share:appmessage', function () {
          WeixinJSBridge.invoke('sendAppMessage', {
            title: title,
            img_url: img,
            img_width: '120',
            img_height: '120',
            desc: desc,
            link: url
          }, function (e) {WeixinJSBridge.log(e.err_msg);});
        });
        WeixinJSBridge.on('menu:share:timeline', function () {
          WeixinJSBridge.invoke('shareTimeline', {
            title: title,
            img_url: img,
            img_width: '120',
            img_height: '120',
            desc: title,
            link: url
          }, function (e) {WeixinJSBridge.log(e.err_msg);});
        });
      };
      if (typeof WeixinJSBridge == "undefined"){
        $document.bind('WeixinJSBridgeReady',onBridgeReady);
      }else{
        onBridgeReady();
      }
    };
    return Wechat;
  })
.factory('userAgent',function(){
    this.isAndroid =  function() {
      return navigator.userAgent.match(/Android/i);
    };
    this.isIOS = function() {
      return navigator.userAgent.match(/iPhone|iPad|iPod/i);
    };
    this.isBlackBerry = function(){
      return navigator.userAgent.match(/BlackBerry/i);
    };
    this.isOpera = function(){
      return navigator.userAgent.match(/Opera Mini/i);
    };
    this.isWindows = function(){
      return navigator.userAgent.match(/IEMobile/i);
    };
    this.mobile = function(){
      return (this.isAndroid || this.isIOS || this.isBlackBerry || this.isOpera || this.isWindows)
    };
    return this;
  })
  ```

## 关于分享按钮问题

如果使用单页js框架，在android的微信内置浏览器中，第一次加载可以用分享按钮，第二页就失效，暂时没找到问题所在，只能用暴力刷新的方式了。
```js
'use strict';

angular.module('worldApp')
.controller('GroupCtrlInfo',function($scope,$routeParams,$location,$cookies,Wechat,userAgent) {
	if($cookies.groupId !== $routeParams.groupId&&userAgent.isAndroid()){
		$cookies.groupId = $routeParams.groupId;
		location.reload();
	})
```
