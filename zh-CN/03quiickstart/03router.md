﻿#1动态路由
##1.1 源码中注册handle：
在每个handle文件中，在init()调用
Regester(name, description handlePattern string, method []string, handleOrMiddleware ...Handler):

说明：
注册一个原子操作，可以任意指定中间件：
* name @操作名称
* description @操作描述
* method @方法列表
* handlePattern @操作名+匹配参数
* handleOrMiddleware @操作和中间件均实现Handler接口，根据传入顺序不同，执行顺序也不同

实现：
通过Caller()获取函数所在路径 p，
设置HandleMap map[string]Handler的值 HandleMap[p>>handlePattern]=handleOrMiddleware。

##1.2 源码中注册middleware

- RegesterMiddleware(name, description string, middleware Handler)
 说明：
  - 注册一个中间件，
  - name @中间件名称
  - description @中间件描述
  - 设置MiddlewareMap map[string]Handler的值 MiddlewareMap[name]=handleOrMiddleware。


##1.3 后台配置
- 显示HandleMap的列表，字段有：name,description,handlePattern,已被哪些模块调用;
- 显示MiddlewareMap的列表，字段有：name,description,已被哪些模块调用;

- 创建模块：
  - 设置模块modulePattern、name和description，
  - 选择父模块parentPattern，
  - 自由组合Handle和Middleware，
- 该模块下某条完整的url匹配即为：path.Join(parentPattern, modulePattern, handlePattern)，
- 选择状态（启用、禁用），
- 保存配置在数据库。

模块列表以折叠树形式呈现，其字段有：
name,description, modulePattern, 父模块, 状态(已启用、已禁用、配置失败), 修改, 删除。


##1.4 路由的特别要求
- 每个节点有status字段，进行状态标记与控制；
- 支持节点创建；
- 支持节点移除。

#2 动态路由实现
- 以中间件方式实现状态控制。

#3 配置管理功能
- 1. 操作和模块的中间件在后台配置；
- 2. 可以对操作和模块进行禁用与启用控制；
- 3. 操作和模块均支持url重定向设置；
- 4. 支持Home关闭与开启；
- 5. 选择是否开启url简写模式。（/home/index/index?a=0简写为？a=0；/home/index/index/0简写为/index/index/0）

#4 编译期实现的自动化
- 1. 依赖路径，智能配置路由；
- 2. 操作有名称与描述字段，其中描述一般在写API时很有用，写死；
- 3. 模块有名称与描述字段，写死；
- 4. url中可以省略"home"模块；
- 5. 默认各个操作与模块均为启用状态；
- 6. 默认Home为启用状态；
- 7. 若开启简写模式，则应该能自动补全简写的url。

#五、技术实现
1. 核心架构采用echo2二次开发；
2. 数据库模块采用xorm；

