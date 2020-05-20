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
