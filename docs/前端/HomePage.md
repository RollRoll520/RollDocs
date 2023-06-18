# Roll's Home

!!! abstract "开启我的个人主页之旅！"
    - :material-clock-start:开始搭建自己的主页2023.6.9
    - :material-note-edit:这里记录在搭建主页时遇到问题
    - 目前仍打算用cra:simple-react:和antd:simple-antdesign:还有其他组件来实现
    - :material-cloud-sync:这就意味着我需要对我的云服务器:material-silverware-clean:清理一番
    - :material-alarm-check:加油吧！坚持下来!
    - 放上仓库[:material-github:github](https://github.com/RollRoll520/Roll-Page)

## Steps

- 页面通过useScroll设计
- 配置一个后台写博客
- 设计数据库记录博客（考虑是否通过markdown形式）
- 展示博客
- 自我介绍

---

## design

- 主页背景（我喜欢的风格）
- 主页先给出几个牌牌，牌牌可以
- [图片翻转](https://codesandbox.io/s/cju2d?file=/src/App.tsx)
- [文字翻转](https://codesandbox.io/s/90qj1i)
- 翻转到Roll的时候给一个特效

- > [参考1](https://sjostrandcoffee.com/)
  > 可以学习顶部底下的滚动的tag，用来展示自己
  > 还可以学习卡片的布局

- > [参考2](https://greennavis.com/)
  > 可以学习顶部菜单栏的button

- > [参考3](https://olve.tech/en)
  > 可以学习顶部的双色设计

- > [参考4](https://www.cssdesignawards.com/sites/wrangle/43488/)
  > 可以学习滚动的交互

- > [coolors](https://coolors.co/)
  > 配色网站

---

## record

>- 了解到了一个很有风格的设计师（前端开发者）[302chanwoo](http://302chanwoo.com/),同样使用React库，并且使用了threeJS开发。`发现了他的github仓库！`[这里](https://github.com/302chanwoo)
>- [lottiefiles](https://lottiefiles.com/)这是一个比gif更**轻量**的animation格式，在这个网页上能够找到很多免费资源
> 使用 `Lottie JSON` 或 `dotLottie` 动画作为图标、缩略图、页面背景、页面加载器、自定义动画光标等等。
>- [WebFlow](https://webflow.com/):`Build with the power of code — without writing any`(**坑，要付费才能导出代码，作用不大**)

## failure

!!! Failure ":cry:踩过的坑"
    >- 使用Link进行图标的跳转时间时出现了错误，可能会改变图标的样式
    >- 采用滚动动画时应该关闭用户滚轮输入，否则会出现卡顿
    >- 打包react文件时没有在package.json中指定homePage
