# \<script> 元素

## 属性
将 JavaScript 插入 HTML 的主要方法是使用 `<script>` 元素，其有8个属性
* **async** (可选)
表示应该立即开始下载脚本，但不能阻止页面的其他动作，比如下载资源或等待其他脚本加载。该属性只对外部脚本文件有效。
* **charset** (可选)
使用 `src` 属性指定的代码字符集。这个属性很少使用，因为大多数浏览器不在乎它的值。
* **crossorigin** (可选)
配置相关请求的 CORS (跨源资源共享) 设置。默认不使用 CQRS
`crossorigin="anonymous"` 配置文件请求不必设置凭据标志
`crossorigin="use-credentials"` 设置平局标志，意味着出站请求会包含凭据
* **defer** (可选)
表示在文档解析和显示完成后再执行脚本是没有问题的。`只对外部脚本文件有效。` 
在 IE7 及更早的版本中，对行内脚本也可以指定这个属性。
* **integrity** (可选)
允许比对接收到的资源和指定的加密签名以验证子资源完整性 ( SRI, Subresource Intergrity )。如果接收到的资源的签名与这个属性指定的签名不匹配，则页面会报错，脚本不会执行。这个属性可以用于确保内容分发网络 ( CDN, Content Delivery Network ) 不会提供恶意内容。
* ~~language~~ (废弃)
最初用于标识代码块中的脚本语言( JavaScript、JavaScript 1.2 ... )
* **src** (可选)
表示包含要执行的代码的外部文件
* **type** (可选)
	代替 language，表示代码块中脚本语言的内容类型 ( MIME )。
	* application/x-javascript  (给定这个值可能导致脚本被忽略)
	* application/javascript (非IE中有效)
	* application/ecmascript (非IE中有效)
	* module (代码中才可以出现 `import ` 和 `export`)

## 位置
一种做法是将外部的 CSS 和 JavaScript 文件都`放在头部`。
```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example HTML Page</title>
		<script src="example1.js"></script>
		<script src="example2.js"></script>
	</head>
	<body>
		<!-- 这是页面内容 -->
	</body>
</html>
```
这样做的结果是需要等所有的 JavaScript 代码都下载、解析和解释完成后才开始渲染页面，而这可能会导致页面渲染明显延迟，在此期间浏览器窗口会完全空白。

针对这个问题的解决方法也很简单，既然放前面会阻塞，那我`放后面`好了
```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example HTML Page</title>
	</head>
	<body>
		<!-- 这是页面内容 -->
		<script src="example1.js"></script>
		<script src="example2.js"></script>
	</body>
</html>
```

但是如果就是头铁要放前面同时不让页面加载明显延迟，我们可以使用 `defer` 属性

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example HTML Page</title>
		<script defer src="example1.js"></script>
		<script defer src="example2.js"></script>
	</head>
	<body>
		<!-- 这是页面内容 -->
	</body>
</html>
```
该属性会告诉浏览器应该在一开始就立即开始下载但是执行应该要推迟。

需要注意的是，根据 HTML5 规范，使用 defer 会让 js 脚本按出现顺序执行，并且会在 DOMContentLoaded 事件之前执行。

但是在实际中，推迟脚本不一定总会按顺序执行或者在 DOMContentLoaded 事件之前执行，因此最好只有一个这样的脚本，或者我们在编码时就不应该对脚本的加载顺序做任何假设。

**动态加载脚本**

我们也可以通过 DOM API 来动态地添加脚本：
```js
const script = document.createElement('script');
script.src = 'gibberish.js';
document.head.appendChild(script);
```

默认情况下通过这种方式创建的 \<script> 元素是以异步方式加载的，相当于添加了 `async` 属性。不过这样做可能有问题，因为不是所有浏览器都支持 `async` 属性，因此要统一动态脚本的加载行为，可以明确地将其设置为同步加载：
```js
const script = document.createElement('script');
script.src = 'gibberish.js';
script.async = false;
document.head.appendChild(script);
```
值得注意的是，以这种方式获取的资源对浏览器预加载器来说是不可见的，因此也会严重影响它们在资源获取队列中的优先级，从而严重影响性能。我们可以在文档头部显式声明它们来让预加载器知道这些动态请求文件的存在：
```html
<link rel="preload" href="gibberish.js">
```

---

# 废弃语法导致的坑

`MIME`  类型并没有跨浏览器标准化，因此在某些情况下，若出现某个无效或无法识别的 `MIME` 类型就可能导致浏览器跳过(不执行)相关代码。因此除非你使用 XHTML 或 \<script> 标签要求或包含非 JavaScript 代码，最佳做法是 `不指定 type 属性`。

---

# 外部文件

使用外部文件而非行内代码有以下优势：

* **可维护性**
* **缓存**
浏览器会根据特定的设置缓存所有外部链接的 JavaScript 文件，这意味着如果两个页面都用到同一个文件，则该文件只需下载一次，从而加快页面加载速度。
在初次请求时，如果浏览器支持 SPDY/HTTP2，就可以从同一个地方取得同一批文件，并将它们逐个放在浏览器缓存中。
* **适应未来**
外部文件的 js 语法在 XHTML 和 HTML 中是一样的，这样就不比考虑 XHTML 和相关的注释黑科技了

---

# 文档模式

IE5.5 发明了文档模式概念，通过 `doctype` 就可以切换文档模式。当今文档模式有以下三种：
* **混杂模式 ( quirks mode )**
* **标准模式 ( standards mode )**
* **准标准模式 ( almost standards mode )**

**混杂模式**
在浏览器中，当省略文档开头的 `doctype` 即开启混杂模式。

**标准模式**
```html
<!-- HTML 4.01 strict -->
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
"http://www.w3.org/TR/html4/strict.dtd">

<!-- XHTML 1.0 Strict -->
<!DOCTYPE HTML PUBLIC 
"-//W3C//DTD XHTML 1.0 Strict//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">

<!-- HTML5 -->
<!DOCTYPE html>
```
**准标准模式**

通过`过渡性文档类型 ( Transitional )`和 `框架集文档类型( Frameset )` 来触发

```html
<!-- HTML 4.01 Transitional -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">

<!-- HTML 4.01 Frameset -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD HTML 4.01 Frameset//EN"
"http://www.w3.org/TR/html4/frameset.dtd">

<!-- XHTML 1.0 Transitional -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<!-- XHTML 1.0 Frameset -->
<!DOCTYPE HTML PUBLIC
"-//W3C//DTD XHTML 1.0 Frameset//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```

---

# \<noscript>

`<noscript>` 原来是作为早起浏览器不支持 JS 的一种优雅降级方案，而对于如今均百分百支持 JS 的浏览器来说这个元素还可以用于禁用 JS 的浏览器。

当存在：
* 浏览器不支持脚本
* 浏览器对脚本的支持被关闭

这两种情况时，\<noscript> 中的内容就会被渲染。否则并不会渲染 \<noscript> 中的内容。

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example HTML Page</title>
		<script defer src="example.js"></script>
	</head>
	<body>
		<noscript>
			<p>This page requires a JavaScript-enabled browser.</p>
		</noscript>
	</body>
</html>
```