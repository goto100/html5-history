# Introduce

history.pushState support;

window.addEventListener('popstate', func, false) support in Standard browser;

window.onpopstate support in All support browser(include IE6/7);

do NOT support history state object yet, pushState() first argument was ignored;

if you need multiple events bind in IE6/7, you should setting up the initialize method, see below.

# Setup

## Step 1:

use script: 
```
<script src="http://html5-history.googlecode.com/svn/trunk/src/html5-history.js"></script>
```

## Step 2:

Initializial it:

```
HTML5History.bind(window.history);
```

if you want use events binding in Internet Explorer, you need setting the fireEvent option to bind method arguments.

```
HTML5History.bind(window.history, {
    fireEvent: function(element, type) {
        jQuery(element).trigger(type);
    }
});
```

After that, you can use jQuery(window).bind('popstate', function() {}) with IE6/7.

## Step 3:

If you are in a Cross Domain environment(document.domain rewrited), you should place a 'blank.html' to you base domain server like : http://example.com/blank.html to support IE6/7.

# How to use

```
history.pushState(state, title, url);
```

state/title argument is not worked, url argument is limited for only hash(#) string, example:

```
history.pushState(null, null, '#one'); //worked
history.pushState({}, 'my title', '#one'); // same as 1
history.pushState(null, null, 'http://www.renren.com/'); // throw an error
```

window.onpopstate event will fire while user click the back/forward button.

```
window.addEventListener('popstate', function() {
    // you can do something in here
}, false); 
```

# Links

* https://developer.mozilla.org/en/DOM/Manipulating_the_browser_history
* http://www.jsmix.com/html5/introduction-to-history.html (中文)
