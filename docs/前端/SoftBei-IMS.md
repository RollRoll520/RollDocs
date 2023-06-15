# SoftBei-IMS

!!! abstract "基于机器学习的分布式系统故障诊断系统--[大赛官网](http://www.cnsoftbei.com/plus/view.php?aid=824)"

    === "简介"
        记录诊断系统前后端的实现
    === "系统要求"
        - [ ] 该平台支持用户上传训练数据并在线训练，训练完成后可下载训练模型
        - [ ] 该平台支持单条或者批量测试样本上传，并可视化分类结果，同时支持下载分类结果
        - [ ] 系统运行流畅
        - [ ] WEB的UI界面美观简洁、人机交互友好
    === "技术栈"
        - :simple-antdesign: React+Antd
          负责前端整体布局
        - :simple-react: React-Spring
          实现更加美观的动画，美化前端
        - :simple-antdesign: Antv
          用于数据可视化
        - :simple-koa: Koa2
          负责后端接口
    === "官方文档链接"
        - [:simple-antdesign:  Antd](https://ant.design/index-cn)
        - [:simple-antdesign:  Antv](https://antv.vision/)
        - [:simple-react: React-Spring](https://www.react-spring.dev/)
---

## 记录

!!! warning "Landing编辑器"
    使用[`Landing`](https://landing.ant.design/index-cn)编辑器设计网页，导出后发现与antd5不兼容！
    
    确实挺好的，而且感觉比[`WebFlow`](https://webflow.com/)良心很多，只可惜不兼容antd5，并且是jsx项目
