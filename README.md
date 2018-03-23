<a href="https://github.com/easwk" target="_blank">
　　<img style="position: fixed; top: 0; right: 0; border: 0; z-index: 1;" src="http://images.cnblogs.com/cnblogs_com/jackson0714/779808/o_github.png" >
</a> 

## [1、redux数据流的4个步骤与技巧](https://github.com/Easwk/easwk.github.io/tree/redux--4stepsforstate)

## [2、react-connect()方法生成容器组件](https://github.com/Easwk/easwk.github.io/tree/redux-connect) 

## [3、react组件生命周期 ](https://github.com/Easwk/easwk.github.io/tree/react-componetn-lifecycel)

## [4、react-解密setState](https://github.com/Easwk/easwk.github.io/tree/react-setState)

## [5、react组件数据流&&组件沟通](https://github.com/Easwk/easwk.github.io/tree/react-shujuliu-zhujiangtongxing)

## [6、react中context实现越级传递](https://github.com/Easwk/easwk.github.io/tree/react-context)

## [7、react中state、props、context对比](https://github.com/Easwk/easwk.github.io/tree/react-duibi)

## [8、react生命周期、数据流与事件](https://github.com/Easwk/easwk.github.io/tree/react-shijian)

## [9、react-dangerouslySetInnerHTML](https://github.com/Easwk/easwk.github.io/tree/react-dangerouslySetInnerHTML)

## [10、react三种组件创建方式对比](https://github.com/Easwk/easwk.github.io/tree/react-three-way-to-create-components)
----
## 技巧
 
---
####浏览器检查
···
 // Browser environment sniffing
var inBrowser = typeof window !== 'undefined';
var inWeex = typeof WXEnvironment !== 'undefined' && !!WXEnvironment.platform;
var weexPlatform = inWeex && WXEnvironment.platform.toLowerCase();
var UA = inBrowser && window.navigator.userAgent.toLowerCase();
var isIE = UA && /msie|trident/.test(UA);
var isIE9 = UA && UA.indexOf('msie 9.0') > 0;
var isEdge = UA && UA.indexOf('edge/') > 0;
var isAndroid = (UA && UA.indexOf('android') > 0) || (weexPlatform === 'android');
var isIOS = (UA && /iphone|ipad|ipod|ios/.test(UA)) || (weexPlatform === 'ios');
var isChrome = UA && /chrome\/\d+/.test(UA) && !isEdge;

// Firefox has a "watch" function on Object.prototype...
var nativeWatch = ({}).watch;
···
