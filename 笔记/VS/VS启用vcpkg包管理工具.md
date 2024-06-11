# 安装vcpkg
在Visual Studio install中安装vcpkg

# 启用vcpkg集成
在终端中运行
```shell
vcpkg integrate install
```
如果找不到命令就在vs的安装目录的Community的vcpkg，把这个路径添加到环境变量，然后把再运行

这样就能在项目属性中找到vcpkg了

[[MSBuild 项目中的 vcpkg | Microsoft ()Learn](https://learn.microsoft.com/zh-cn/vcpkg/users/buildsystems/msbuild-integration)]

# 在项目中启用vcpk
看这个文章
[[在 Visual Studio 中使用 CMake 安装和管理包 | Microsoft Learn](https://learn.microsoft.com/zh-cn/vcpkg/get_started/get-started-vs?pivots=shell-cmd)]
后面的在项目中创建
`vcpkg.json
的部分，在项目中创建类似pyth
