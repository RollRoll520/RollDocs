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

??? warning "Landing编辑器"
    > 使用[`Landing`](https://landing.ant.design/index-cn)编辑器设计网页，导出后发现与antd5不兼容！
    >
    > 确实挺好的，而且感觉比[`WebFlow`](https://webflow.com/)良心很多，只可惜不兼容antd5，并且是jsx项目
    >
    > 虽然无法正常使用，但可以从下载的代码中进行学习

### 0622record

???+ abstract "尝试修改lottieFile的加载方式"
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

---

### 0625record

???+ failure ":sob:今天的任务：通过代理:material-code-tags:解决跨域！"
    === "问题分析"
        - 客户端未设置代理
        - [ ] 未完成，因为收到了**chanwoo hwang**的回信
        - **OpenGL**真的需要好好学学！
        - [gsap](https://greensock.com/gsap/)不错的库！chan推荐的
    === "尝试结论"
        - json不同于图片，图片不受浏览器cors影响，而json会受到限制
        - 或许目前来说，将json文件放在同一域下最合适，**毕竟lottie很轻量！**

---

### 0626record

???+ abstract ":sob:今天的任务：添加:material-code-tags:训练页的布局！"
    === "todo任务"
        - [x] 学习**easyDL**的布局进行设计
        - [x] 使用合适的**lottie**动画进行美化
        - [x] 添加**大幅**的的动画
        - [x] **整齐美观**的布局

### 0628record

???+ success ":angel:完成0626的任务，并且进行了配色调整！"
    === "experience"
        - 花了很久修改`antd`中步骤条`Steps`组件的样式，但是**无法正常修改！**修改后无法正常使用！
        - 增加了训练页和测试页的退出功能。
        - 写了一个退出警告的提示框，但是似乎**复用性不高！**因为需要在使用的时候传递`onOK`和`onCancel`函数。
        - 发现**需要增加展示数据集记录、结果记录**的页面，因为在上传训练集或者测试集之后，这些信息对于用户来说就**无法溯**了，并且当训练请求出现问题后，训练将不可恢复。
    === ":rocket:todo（replaced by **0701discuss**）"
        1. [ ] 为训练页和测试页添加跳转功能
        - [ ] 设计合适的布局，保证**界面简洁、人机交互好**
        - [ ] 浅尝**antv**
        - [ ] 添加身份认证功能（暂时使用**zja**的后端）
        - [ ] 需要开始调整后端，拓展**zja**后端

### 0701discuss

???+ abstract ":thought_balloon:明确开发思路"
    === "下一步"
        1. [ ] 训练模型**对应**测试模型，训练完模型之后可以提供下载。
        - [ ] 测试模型时可以选择上传模型，也可以选择测试提供的默认模型。
        - [ ] 训练模型**耗时较长**，测试模型**耗时较短**。
        - [ ] 提供账户登录，可以提供训练记录，但要对此进行数据库的设计。
    === "系统逻辑"
        1. 在训练模型页面上传数据集（仅仅上传**csv**文件），点击训练直接训练。
        - 后端将上传的数据集按照制定命名方式（**csv/用户名/1+.csv**）储存。
        - 异步调用**train_model**来进行模型的训练。
        - 训练时根据**中间过程返回值**来展示训练进度。
        - 训练结束时展示训练结果（机器学习模型无结果，神经网络模型有结果）。
        - 提供下载模型的方法（根据**数据集命名方式**）。
        - **提示**用户**前往**在线测试页对刚才下载的模型进行测试。
        - 进行测试时可以**选择**是否上传模型进行测试。

### 0703record

???+ abstract "通过反向代理同源跨域问题"
    === "问题分析"
        - 在之前的开发中使用的是**koa-cors**对跨域问题进行处理。
        - 但在实际过程中**koa-cors**的使用上具有**局限性**，比如无法对本地运行的客户端提供服务。
        - 尝试通过配置**nginx**来对请求进行代理，同时可以屏蔽服务端运行的端口。
    === "实现代码"
        ```md
        location /zja {
            proxy_pass http://example.com:port;
            # 设置响应头，允许跨域请求
            add_header 'Access-Control-Allow-Origin' 'http://localhost:3000';
            add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'Content-Type, Authorization';
            add_header 'Access-Control-Allow-Credentials' 'true';

            # 处理 OPTIONS 请求
            if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Allow-Origin' 'http://localhost:3000';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'Content-Type, Authorization';
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Content-Length' 0;
                return 204;
            }
      
        }
        ```

### 0705record

???+ Question "尝试响应式布局方案"
    === "尝试"
        1. 在接有大屏显示器的客户端进入网页时会出现**网页布局混乱**的情况（主要考虑**pc**端）
        - 通过使用**相对分辨率单位**来实现响应式布局```
        使用相对单位如**em、rem、vh、vw**等能够保证页面元素的大小和间距在不同分辨率下的比例关系不会失衡，从而保证页面在大屏幕高分辨率下显示的一致性。
        ```
        - 通过css的**媒体查询**`@media (max-width: 480px)`来修改布局
        - 使用**网格布局**能够确保页面元素在不同分辨率下的排列和比例关系不会失衡，从而保证页面在大屏幕高分辨率下的显示效果。
    === "经验"
        1. 使用**相对分辨率单位**对于很多场景的响应式需求并不适用，因为可能分辨率变大（**X**应该是文本大小变大，因为连接显示器不是改变分辨率，而是改变文本大小）后整个页面需要重新布局才能更好地展示。
        - 可以通过**媒体查询**来实现分辨率变化后的重新布局
        - **网格布局**在响应式需求中表现得很好
        - 直接放大页面**并不能**完全模拟在不同设备上的显示效果，因为不同设备的屏幕分辨率和显示技术可能存在差异，导致页面在不同设备上的显示效果也会有所不同。而且，通过放大页面来模拟不同设备的显示效果也**可能会失真**，因为放大后的页面**并不能完全还原**出原始页面在**不同分辨率**设备上的显示效果。
        - 应该使用**设备仿真**来测试不同设备上的显示效果。
        - 可以通过设置**显示设置**中的**文本大小**来模拟大屏显示器的效果

### 0706record

???+ Tip "冲刺后端"
    === "goal"
        1. [ ] 设计数据库并且完成基础配置
        - [ ] 搭建后端服务器，提供基本服务
        - [ ] 测试后端接口
        - [ ] 为前端交互组件添加交互事件
        - [ ] 测试交互事件
    === "database design"
        === "需求分析"
            1. 用户需要上传 csv 格式的数据集，并通过页面进行模型训练。
            - 后端将数据集按照命名方式存储，方便后续使用。
            - 系统需要异步调用 train_model 进行模型训练，同时根据中间过程返回值展示训练进度。
            - 训练结束后，系统需要展示训练结果，对于神经网络模型还需要提供下载模型的方法。
            - 系统需要提示用户前往在线测试页面进行模型测试，同时在测试页面中提供选择上传模型的功能。
        === "概念结构设计"
            === "**User**用户表"
                - 用户名（username）：用户的用户名，为该表的主键。
                - 密码（password）：用户的密码，用于登录系统。
                - 邮箱（email）：用户的电子邮件地址，用于接收系统通知和管理。
                - 注册时间（register_time）：用户的注册时间，记录用户加入系统的时间。
            === "**Dataset**数据集表"
                - 数据集ID（id）：数据集的唯一编号，为该表的主键。
                - 用户名（username）：数据集上传者的用户名，为该表的外键，关联到用户表的用户名字段。
                - 数据集文件名（filename）：数据集文件的名称，用于在文件系统中查找该数据集文件。
                - 数据集路径（path）：数据集文件的路径，用于在文件系统中查找该数据集文件。
                - 数据集类型（type）：数据集的类型，用于区分训练集和测试集。
                - 上传时间（upload_time）：数据集的上传时间，记录数据集被上传的时间。
            === "**TrainRecord**训练记录表"
                - 训练记录ID（id）：训练记录的唯一编号，为该表的主键。
                - 训练集ID（dataset_id）：训练所使用的数据集的编号，为该表的外键，关联到数据集表的id字段。
                - 用户名（username）：训练者的用户名，为该表的外键，关联到用户表的用户名字段。
                - 训练开始时间（start_time）：训练的开始时间，记录训练开始的时间。
                - 训练结束时间（end_time）：训练的结束时间，记录训练结束的时间。
            === "**TestRecord**测试记录表"
                - 测试记录ID（id）：测试记录的唯一编号，为该表的主键。
                - 测试集ID（dataset_id）：测试所使用的数据集的编号，为该表的外键，关联到数据集表的id字段。
                - 用户名（username）：测试者的用户名，为该表的外键，关联到用户表的用户名字段。
                - 测试开始时间（start_time）：测试的开始时间，记录测试开始的时间。
                - 测试结束时间（end_time）：测试的结束时间，记录测试结束的时间。
            === "**TrainResult**训练结果表"
                - 训练结果ID(id)：训练结果的唯一编号，为该表的主键。
                - 训练记录ID(train_record_id)：训练记录的编号，为该表的外键，关联到训练记录表的id字段。
                - 训练模型文件(modelName)：模型训练生成的模型文件名
                - 训练模型路径(modelPath):模型训练生成的模型文件所在路径
            === "**TestResult**测试结果表"
                - 测试结果ID(id)：测试结果的唯一编号，为该表的主键。
                - 测试记录ID(train_record_id)：测试记录的编号，为该表的外键，关联到测试记录表的id字段。
                - 测试结果文件(filename)：模型测试生成的结果文件名
                - 测试结果路径(path):模型测试生成的结果文件所在路径
    === "experience"
        1. 通过**Pyinstaller**将python打包为exe文件

### 0709Mind

???+ example "根据需求优化后端接口"
    === "reason"
        1. 后端基本完成，但是不符合**前端需求**
        - 前端需求分析并没有考虑好实际
        - 是否需要储存**model**，不需要储存应该储存
        - 用户可以有很多选择，是否可以进行丰富
    === "decision"
        1. 提供**更多更全面**的服务！
        - 通过**实现删除来限制**用户资源个数！
        - **优化表格逻辑**以提供更优质的业务！
    === "idea about backend"
        === "dataset"
            1. 用户上传的所有数据集**都将保留**
            - 数据集的**id不递增**，而是根据**YY_MM_DD_001**命名,命名时需要根据当前数据库顶部id设置（确保不重复）
            - 为用户的数据集添加**remark**字段进行备注，并提供**添加以及修改备注**的功能
            - 为用户的数据集添加**isDeleted**字段，并提供**删除数据集**的功能
            - 为用户的数据集提供**获取**功能
            - 为数据集添加状态**未完成**、**已完成**、**隐藏**
            - **我们会为你保留训练/测试集，但仅提供10条结果**
        === "record"
            1. 为记录添加**isExpired**字段，并提供删除记录的功能
            - 记录的**isExpired**改变应跟随dataset的改变而改变
            - 记录**删除的同时**删除结果文件
            - 提供展示**isExpired**的记录的接口，但在前端不提供其获取result的方法
            - **我们会为你保存所有训练/测试记录，但仅支持5条有效训练记录/10条有效测试记录**
            - **你仍可以查看失效记录，但无法通过失效记录获取结果文件**
        === "result"
            1. 提供**删除**接口，当记录isExpired时**unlink当前的结果文件**
            - 为结果添加**isDeleted**字段，当结果删除时**将该字段置为true**
            - 由于在测试需要获取当前用户拥有的模型，因此应该**为TrainResult添加u_id的外键**
            - 将TrainResult的**modelName**字段改为**modelRemark**用于对模型文件进行**备注**
            - 为TrainResult添加根据u_id获取**模型文件信息**的功能（用于**选择**）
        === "model"
            1. **train**不再使用当前用户最新的数据集**getLatest**
            - **train**根据用户传入的**dataset_id**判断是否属于当前用户，判断是否为训练集
            - **train**还需要根据用户传入的remark对**modelRemark**进行备注
            - **defaultTest**添加对**dataset_id**的**validate**
            - **customTest**
    === "idea about frontend"
        === "OnlineTrain"
            1. 将路由改为**OnlineTrain**
            - 添加训练集的展示，**0/10**,表示当前有效的测试集
            - 添加数据服务页面
        === "OnlineTest"
    === "minor idea"
        1. 添加注册功能（需要提供邮箱或短信验证），或提供相应身份注册码。
        - 添加漫游式指引（通过antd组件实现，需要判断是否需要漫游式指引）
