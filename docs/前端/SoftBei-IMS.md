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
    > 使用[`Landing`](https://landing.ant.design/index-cn)编辑器设计网页，导出后发现与antd5不兼容！
    >
    > 确实挺好的，而且感觉比[`WebFlow`](https://webflow.com/)良心很多，只可惜不兼容antd5，并且是jsx项目
    >
    > 虽然无法正常使用，但可以从下载的代码中进行学习

### 0622record

!!! abstract "尝试修改lottieFile的加载方式"
    === "origin:通过assets中加载"
        - 通过`MyLottie`组件对`assets`中的`json`文件进行遍历加载
        - 在刚加载完页面时会出现短时间的卡顿现象
        ```ts title="加载所有lottie文件"
        const data = {};
        const req = require.context("../../assets/", true, /\.json$/);
        req.keys().forEach((filename) => {
            data[filename.replace(/\.\/(.+)\.json/, "$1")] = req(filename);
        });

        ```
    === "polish:优化为按需加载"
        - 原来的方式在组件挂载时会将所有的 `lottie` 文件都加载进来，这样会占用较多的内存和网络带宽，因此当文件夹下的 `Lottie` 文件较多时，可能会导致页面加载缓慢或者卡顿。
        - 修改为：将 `Lottie JSON` 文件的加载放在组件的回调函数中，在组件的 `onLoad` 回调函数中加载 `Lottie JSON` 文件，这样只有当组件实际需要加载某个 `lottie` 文件时才会进行加载。
        - 效果：**非常明显！**减少了明显的卡顿，也优化了内存和网络带宽
        ```js title="按需加载lottie文件"

        useEffect(() => {
            const fetchData = async () => {
                const data = await import(`../../assets/${type}.json`);
                setAnimationData(data);
            };
            fetchData();
        }, [type]);
        ```
    === "polish:优化为懒加载"
        - **懒加载**是指将组件或模块的加载推迟到组件或模块首次使用时再进行加载。
        - 使用 `React.lazy` 来动态加载 `lottie` 组件，然后使用 `Suspense` 组件来包裹需要懒加载的组件，并提供一个 `fallback` 属性，指定组件加载时的 `loading` 界面。这样，在组件首次使用时才会进行加载，可以有效地优化页面加载性能。
        - 效果：懒加载用于优化页面加载性能，但实际上我们的页面的所有组件在首次加载时都使用到了，因此**优化效果不明显**。
        - 优点：提供了**资源加载未成功的loading元素**
        ```js title="懒加载lottie组件"

        import { lazy, Suspense } from "react";
        const LazyMyLottie = lazy(() => import("./MyLottie"));
        const App = () => {
            return (
                <div>
                    <Suspense fallback={<div>Loading...</div>}>
                        <LazyMyLottie type="lottie1" />
                    </Suspense>
                    <Suspense fallback={<div>Loading...</div>}>
                        <LazyMyLottie type="lottie2" />
                    </Suspense>
                    <Suspense fallback={<div>Loading...</div>}>
                      <LazyMyLottie type="lottie3" />
                    </Suspense>
                </div>
            );
          };
        export default App;
        ```
    === "update:通过url加载lottie"
        - [ ] **finish**
        - [ ] **todo**:修改客户端代理，避开cors策略
        - 当`lottie`文件过多时，存在需要将`lottie`文件集中放在服务器中的需求。
        - 当前的`lottie`加载只能通过本地文件加载。
        - 希望设计的组件使用方式足够简洁，便于使用，且不影响原来本地`lottie`的使用。
