---
icon: material/video/3d
---

# xr-frame资源

!!! abstract "简介"
    在微信小程序`xr-frame`框架下通过`gltf/glb`实现`AR+VR`时由已有3D资源获取`gltf/glb`的一种解决方案

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
    * `XR-frame`在接收`glb/gltf`资源文件时,可能会显示所需的`KHR_texture_transform`插件不存在，导致加载模型失败。

    * 并且在使用`XR-frame-cli`进行优化时存在网格索引过大的情况，只有在导出设置中勾选第1项第2项时才能成功。

### [SeinJSUnityToolkit](https://github.com/hiloteam/SeinJSUnityToolkit)

1. 官方提示下载`unitypackage`到unity即可使用`SeinToolkit`，但是最新版的包导入项目中后存在问题，可能是因为版本不兼容。
2. 通过文档中提示的通过npm创建sein命令行工具可以穿件sein项目**但下载的版本是1.2.6，最新版为1.3.3**，并且通过`sein new`创建的sein项目必须用

=== "导入fbx并导出"
    1. 导入fbx时出现无材质的现象

???+ bug "踩的坑"
    * 通过`gltfpack`的-cc拓展指令压缩的`gltf`模型无法正确`import`到`sein`项目中
    * `seinJS`导出的`gltf`文件也可能无法在`xr-frame`中打开，并不是完全优于`UniVRM`
    * 相对于比较大的3D模型文件，`sein`的表现会比`UniVRM`
    * 模型过大时`UniVRM`的导出没有压缩作用，但`seinJS`中必须使用的`sein/pbr`着色器调整不好会让模型较为严重地失真
    * 因此当`seinJS`导入`xr-frame`中不起作用时应及时改用`UniVRM`
    * `fbx`文件导入`unity`中无法显示背面

## 优化gltf文件

### [gltfpack](https://meshoptimizer.org/gltf/)优化压缩

1. 在[gltfpack官网](https://meshoptimizer.org/gltf/)中获取合适的gltfpack版本
2. 将`gltfpack`工具包下载到本地
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

2. 打开命令行工具（因为已经全局添加了`xr-frame-cli`，任意一个目录下都可以使用该工具）
3. 通过`xr-frame gltf -h`或`xr-frame env-data -h`获取工具使用指令

!!! warning "`gltfpack`与`xr-frame-cli`的区别"

    * `gltfpack`的对于文件大小的压缩效果明显高于`xr-frame-cli`
    
    * `gltfpack`可以压缩`glb/gltf`两种格式；而`xr-frame-cli`仅能压缩`gltf`文件

    * `gltfpack`生成的`gltf/glb`文件可能在被`xr-frame`加载时存在错误

## 获取环境数据env-data

[xr-frame-cli](#xr-frame-cli优化压缩)提供了环境数据的生成功能。使用正确的环境贴图即可导出xr-frame所需的环境数据env-data
因此关键在于获取**环境贴图**
