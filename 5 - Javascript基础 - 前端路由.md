# 前端路由

## 背景

远古时期，当时前后端还是不分离的，路由都由服务端控制。

客户端 -> http 请求 -> 服务端 -> 根据 url 路径的不同，返回不同的 html/数据

这种方式的缺点和优点都非常明显

优点：SEO 效果好， 用户看到首屏的耗时会非常小
缺点：服务器压力大，代码融合在一起不好维护，协作流程不清晰

后来
后来
后来

随之 ajax 的流行，异步数据请求可以在浏览器不刷新的情况下进行。

后来
后来
后来

出现了更高级的体验 - 单页应用。
在单页应用中，不仅在页面中的交互是不刷新页面的，就连页面跳转也都是不刷新页面的。
而支持起单页应用这种特性的，就是前端路由。

## 前端路由特性

 前端路由的需求是什么？

1. 根据不同的 url 渲染不同内容
2. 不刷新页面

也就是可以在改变 url 的前提下，保证页面不刷新。

## Hash 原理及实现

hash 的出现满足了这个需求，他有以下几种特性：

1. url 中的 hash 值只是客户端/浏览器端的一种状态，向服务器发送请求的时候，hash 部分的值不会携带。
2. hash 值的更改，并不会导致页面的刷新
3. hash 值的更改，会在浏览器的访问历史中增加记录，所以我们可以通过浏览器的回退、前进按钮控制 hash 的切换
4. hash 值的更改，会触发 hashchange 事件

比如https://www.baidu.com/#/hash1, 改变#后面的内容并不会导致页面刷新，而且会触发 hashchange 事件。

我们同样有两种方式来控制 hash 的变化：

1. 通过 a 标签，设置 href 属性，当用户点击 a 标签的时候，Url 中的 hash 就会改变为 href 属性值。

比如 <a href="#hash-change">点击更改 hash</a>

2. 通过 js

`location.hash = '#hash-change'`

### 手动实现一个基于 hash 的路由

```html
<!DOCTYPE html>
<html>
  <body>
    <div class="container">
      <a href="#gray">灰色</a>
      <a href="#green">绿色</a>
      <a href="#">白色</a>
      <button onclick="window.history.go(-1)">返回</button>
    </div>
  </body>
  <script type="text/javascript" src="index.js"></script>
  <style>
    .container {
      width: 100%;
      height: 60px;
      display: flex;
      justify-content: space-around;
      align-items: center;

      font-size: 18px;
      font-weight: bold;

      background: black;
      color: white;
    }

    a:link,
    a:hover,
    a:active,
    a:visited {
      text-decoration: none;
      color: white;
    }
  </style>
</html>
```

```js
class BaseRouter {
  constructor() {
    this.routes = {};
    this.currentUrl = "";
    this.refresh = this.refresh.bind(this);
    window.addEventListener("load", this.refresh, false);
    window.addEventListener("hashchange", this.refresh, false);
  }

  route(path, callback) {
    this.routes[path] = callback || function () {};
  }

  refresh() {
    console.log("触发一次 hashchange，hash 值为", location.hash);
    this.currentUrl = `/${location.hash.slice(1) || ""}`;
    this.routes[this.currentUrl]();
  }
}

const content = document.querySelector("body");

function changeBgColor(color) {
  content.style.backgroundColor = color;
}

const Router = new BaseRouter();

Router.route("/", function () {
  changeBgColor("white");
});
Router.route("/green", function () {
  changeBgColor("green");
});
Router.route("/gray", function () {
  changeBgColor("gray");
});
```

## History 原理及实现

hash 虽然能解决问题，但是带有#很不美观。

历史的车轮无情撵过 hash，到了 html5 时代，推出了 history api。

```js
window.history.back(); // 后退
window.history.forward(); // 前进
window.history.go(-3); // 后退三个页面
window.history.pushState(null, null, path);
window.history.replaceState(null, null, path);
```

其中最主要的两个 api 是`pushState`和`replaceState`;

这两个 api 都可以在不刷新页面的情况下，操作浏览器历史记录。

不同的是，pushState 会增加历史记录，replaceState 会直接替换当前历史记录。

History Api 有以下几种特性：

1. history.pushState() 或 history.replaceState() 不会触发 popstate 事件，这时我们需要手动触发页面渲染；
2. 可以使用 popstate 事件来监听 url 的变化
3. 只有用户点击浏览器倒退按钮和前进按钮，或者使用 JavaScript 调用 back、forward、go 方法时才会触发 popstate。

他们的参数是一样的，三个参数分别是：

1. state:一个与指定网址相关的状态对象，popstate 事件触发时，该对象会传入回调函数。如果不需要这个对象，此处可以填 null。
2. title：新页面的标题，但是所有浏览器目前都忽略这个值，因此这里可以填 null。
3. url：新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。

### 手动实现一个基于 History 的路由

```html
<!DOCTYPE html>
<html>
  <body>
    <div class="container">
      <a href="/gray">灰色</a>
      <a href="/green">绿色</a>
      <a href="/">白色</a>
      <button onclick="window.history.go(-1)">返回</button>
    </div>
  </body>
  <script type="text/javascript" src="index.js"></script>
  <style>
    .container {
      width: 100%;
      height: 60px;
      display: flex;
      justify-content: space-around;
      align-items: center;

      font-size: 18px;
      font-weight: bold;

      background: black;
      color: white;
    }

    a:link,
    a:hover,
    a:active,
    a:visited {
      text-decoration: none;
      color: white;
    }
  </style>
</html>
```

```js
class BaseRouter {
  constructor() {
    this.routes = {};
    this._bindPopState();
    this.init(location.pathname);
  }
  init(path) {
    history.replaceState(
      {
        path: path,
      },
      null,
      path
    );
    this.routes[path] && this.routes[path]();
  }

  route(path, callback) {
    this.routes[path] = callback || function () {};
  }

  go(path) {
    history.pushState(
      {
        path: path,
      },
      null,
      path
    );
    this.routes[path] && this.routes[path]();
  }
  _bindPopState() {
    window.addEventListener("popstate", (e) => {
      const path = e.state && e.state.path;
      console.log(path);
      this.routes[path] && this.routes[path]();
    });
  }
}

const Router = new BaseRouter();

const body = document.querySelector("body");
const container = document.querySelector(".container");

function changeBgColor(color) {
  body.style.backgroundColor = color;
}

Router.route("/", function () {
  changeBgColor("white");
});
Router.route("/gray", function () {
  changeBgColor("gray");
});
Router.route("/green", function () {
  changeBgColor("green");
});

container.addEventListener("click", (e) => {
  if (e.target.tagName === "A") {
    e.preventDefault();
    Router.go(e.target.getAttribute("href"));
  }
});
```
