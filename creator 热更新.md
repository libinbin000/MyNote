## version_generator.js使用
```
node version_generator.js -v 1.0.0 -u http://127.0.0.1:8080/remote-assets/ -s build/jsb-default/ -d assets/
```  


## 构建成功原生版本之后，打开原生发布包的地址，给 main.js 附加上搜索路径设置的逻辑：

// 在 main.js 的开头添加如下代码
```
if (cc.sys.isNative) {
    var hotUpdateSearchPaths = cc.sys.localStorage.getItem('HotUpdateSearchPaths');
    if (hotUpdateSearchPaths) {
        jsb.fileUtils.setSearchPaths(JSON.parse(hotUpdateSearchPaths));
    }
}
```
或者直接使用项目仓库根目录下的 main.js 覆盖原生打包文件夹内的 main.js。注意，每次使用 Cocos Creator 构建后，都需要重新修改 main.js。

***
## 发布新版本操作步骤
+ 构建项目
+ 使用version_generator.js 生成project.manifest和version.manifest 版本好需要高于现有版本
+ 拷贝 res文件夹 src文件夹 project.manifest和version.manifest作为远程资源

> 1
