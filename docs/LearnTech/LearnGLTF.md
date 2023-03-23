# 在小程序项目中使用gltf的一种解决方案

---

## 从unity中导出gltf

### [UniVRM](https://github.com/vrm-c/UniVRM/tree/master/Assets/UniGLTF)从unity导出gltf/glb

1. 在unity中添加[UniVRM](https://github.com/vrm-c/UniVRM/tree/master/Assets/UniGLTF)插件
2. 在scene中添加需要导出的模型
3. 选中目标模型导出
4. 在选择文件时修改文件扩展名可以分别导出gltf/glb两种格式
5. 导出设置中第1项第2项可以勾选

!!! warning "可能存在的问题"
    XR-frame在接收glb/gltf资源文件时,可能会显示所需的KHR_texture_transform插件不存在，导致加载模型失败。并且在使用XR-frame-cli进行优化时存在网格索引过大的情况，只有在导出设置中勾选第1项第2项时才能成功。

### [SeinJSUnityToolkit]

## 优化gltf文件

### [gltfpack](https://meshoptimizer.org/gltf/)优化压缩

### [xr-frame-cli](https://github.com/wechat-miniprogram/xr-frame-cli)优化压缩