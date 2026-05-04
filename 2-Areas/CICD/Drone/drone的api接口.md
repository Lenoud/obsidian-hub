# drone的api接口

drone的api接口相关技术笔记。

常用的Drone API路径：

+ /api/user：获取当前用户信息
+ /api/repos：获取用户拥有的仓库列表
+ /api/repos/{namespace}/{name}：获取指定仓库的信息
+ /api/repos/{namespace}/{name}/builds：获取指定仓库的构建列表
+ /api/repos/{namespace}/{name}/builds/{number}：获取指定仓库中某个构建的详细信息
+ /api/repos/{namespace}/{name}/builds/{number}/logs：获取指定构建的日志
+ /api/repos/{namespace}/{name}/builds/{number}/artifacts：获取指定构建的构件列表
+ /api/pipeline/{namespace}/{name}/{number}：获取指定流水线的详细信息
+ /api/pipeline/{namespace}/{name}/{number}/steps：获取指定流水线中所有步骤的信息
+ /api/pipeline/{namespace}/{name}/{number}/logs：获取指定流水线的日志
