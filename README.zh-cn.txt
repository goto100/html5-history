html5规范给我们带来了全新的History API，为Ajax开发中常见的后退、前进功能模拟提供了参考，规范内容参照：

    https://developer.mozilla.org/en/DOM/Manipulating_the_browser_history
    http://www.jsmix.com/html5/introduction-to-history.html (中文)

在目前的浏览器环境中，为了实现Ajax导航功能，已经有很多类库供我们使用了，（rsh/yui/history.js等）即便是自己实现，了解原理的话，也并不复杂。

模拟方法无非是在标准浏览器下通过修改hash触发历史，再通过onhashchange事件捕获用户操作，在IE6/7下则比较复杂，需要用一个iframe触发历史，在常用框架中，为了支持IE6/7，一般都需要在页面中引用一个特殊的iframe文件来实现，有的还需要创建一个interval不停的检测用户是否进行了操作。

这些类库中，history.js提供了类似于html5规范的api，但经过我的试用，实现效果并不一致，可能是作者对html5 history api规范的理解有误。

所以，我决定自己动手，开发了 html5-history 这个小脚本。经本人测试，兼容 IE6/IE7/IE8/Firefox 3。

优点：

    api完全参照html5，在html5浏览器中是直接使用系统api的；
    如果不是跨域操作，完全不需要设置任何iframe，只需要引入一个js就可以了；
    如果是跨域操作，只需要服务器上存在一个空白的页面即可，不需要放置一个特殊内容的页面；
    没有任何定时器 （setInterval），保证运行效率。

现有问题：

    由于浏览器限制，并不能完整实现html5 history api的全部特性；
    目前没有实现保存一个state json object功能，即pushState的第一个参数是被忽略的，当然这个特性将来会支持；
    由于IE没有给node fire自定义事件的机制，如果需要在IE6/7下绑定 popstate 事件，需要进行一个代理配置；

使用方法：

1 引入js：

<script src=”http://html5-history.googlecode.com/svn/trunk/src/html5-history.js”></script>

2 初始化：
	
HTML5History.bind(window.history);

如果需要在IE中使用事件绑定，需要配置一下代理方法：
	
HTML5History.bind(window.history, {
     fireEvent: function(element, type) {
         jQuery(element).trigger(type); // 这里使用jQuery的例子
     }
 });

然后，你就可以用 jQuery(window).bind(‘popstate’, function() {}) 绑定 popstate 事件了。

3 跨域

如果你在一个跨域环境下，document.domain 被设置成了同 location.hostname 不一样，则需要在 document.domain 那个域下放置一个 blank.html ，内容留空即可。

使用方法：
	
history.pushState(state, title, url);

在这里，state，title参数只是为了兼容html5 api设置的，使用时请留空；

url参数也只能限制成一个url hash，即 #xxx 格式，比如：

history.pushState(null, null, '#one'); // 成功
history.pushState({}, 'my title', '#one'); // 同上一条一样，前两个参数被忽略了
history.pushState(null, null, 'http://www.renren.com/'); // url不是hash结构，报错。

window.onpopstate 事件将会在用户点击浏览器后退、前进按钮时触发：
	
window.addEventListener('popstate', function() {
    // you can do something in here
}, false);

