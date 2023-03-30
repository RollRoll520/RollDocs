---
icon: material/video/3d
---

# LearnGLTF

!!! abstract "简介"
    在微信小程序xr-frame框架通过gltf/glb实现AR+VR时由已有3D资源获取gltf/glb的一种解决方案

## 从unity中导出gltf

### [UniVRM](https://github.com/vrm-c/UniVRM/tree/master/Assets/UniGLTF)从unity导出gltf/glb

1. 在unity中添加[UniVRM](https://github.com/vrm-c/UniVRM/tree/master/Assets/UniGLTF)插件
2. 在scene中添加需要导出的模型
3. 选中目标模型导出
4. 在选择文件时修改文件扩展名可以分别导出gltf/glb两种格式
5. 导出设置中第1项第2项可以勾选

???+ abstract "官方的简介"
    Unity-Supports 2019.4 and later
    You can import and export `glTF-2.0 in` Unity's editor and runtime.
    implement `KHR_materials_unlit`
    implement `KHR_texture_transform` (partial)

!!! bug "可能存在的问题"
    * XR-frame在接收glb/gltf资源文件时,可能会显示所需的`KHR_texture_transform`插件不存在，导致加载模型失败。

    * 并且在使用`XR-frame-cli`进行优化时存在网格索引过大的情况，只有在导出设置中勾选第1项第2项时才能成功。

### [SeinJSUnityToolkit](https://github.com/hiloteam/SeinJSUnityToolkit)

1. 官方提示下载unitypackage到unity即可使用SeinToolkit，但是最新版的包导入项目中后存在问题，可能是因为版本不兼容。
2. 通过文档中提示的通过npm创建sein命令行工具可以穿件sein项目**但下载的版本是1.2.6，最新版为1.3.3**，并且通过sein new创建的sein项目必须用

=== "导入fbx并导出"
    1. 导入fbx时出现无材质的现象

!!! bug "踩的坑"
    * 通过gltfpack的-cc拓展指令压缩的gltf模型无法正确import到sein项目中

## 优化gltf文件

### [gltfpack](https://meshoptimizer.org/gltf/)优化压缩

1. 在[gltfpack官网](https://meshoptimizer.org/gltf/)中获取合适的gltfpack版本
2. 将gltfpack工具包下载到本地
3. 在工具包的路径下打开命令行窗口
4. 通过`gltfpack -i scene.gltf -o scene.glb`将指定gltf/glb文件进行压缩到指定目录

??? tip "指令的扩展可选项"
    * `-cc`: produce compressed gltf/glb files (requires `EXT_meshopt_compression`)

    * `-tc`: convert all textures to KTX2 with BasisU supercompression (requires `KHR_texture_basisu` and may require`-tp` flag for compatibility with WebGL 1)

    * `-mi`: use mesh instancing when serializing references to the same meshes (requires `EXT_mesh_gpu_instancing`)
    
    * `-si R`: simplify meshes targeting triangle count ratio R (default: 1; R should be between 0 and 1) The following settings are frequently used to restrict some optimizations:

    * `-kn`: keep named nodes and meshes attached to named nodes so that named nodes can be transformed externally

    * `-km`: keep named materials and disable named material merging

    * `-ke`: keep extras data

### [xr-frame-cli](https://github.com/wechat-miniprogram/xr-frame-cli)优化压缩

1. 通过npm将[xr-frame-cli](https://github.com/wechat-miniprogram/xr-frame-cli)添加到项目中

    ```md "添加命令行工具"
    npm i xr-frame-cli
    ```

2. 在外部通过管理员身份打开项目终端
3. 通过`xr-frame gltf -h`或`xr-frame env-data -h`获取工具使用指令

!!! warning "gltfpack与xr-frame-cli的区别"

    * gltfpack的对于文件大小的压缩效果明显高于xr-frame-cli
    
    * gltfpack可以压缩glb/gltf两种格式；而xr-frame-cli仅能压缩gltf文件

    * gltfpack生成的gltf/glb文件可能在被xr-frame加载时存在错误

!!! tip "Todo"
    1. 完成模型的导出，并进行足够的优化
    2. 成功加载场景模型
    3. 实现相机的移动
    4. 实现场景的交互，在场景中添加button
    5. 实现AR
