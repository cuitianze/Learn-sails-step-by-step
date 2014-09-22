Learn-sails-step-by-step
========================
# Assets(资源)
- 主要用来放置(static files)静态文件(js, css, images, etc).
- assets文件夹的内容都会被同步到一个隐藏的临时目录(.tmp/public),当lift app 的时候.这个临时目录是Sails实际serve的。
- 这一中间步骤允许Sails去预编译assets, 例如 LESS, CoffeeScript, SASS, spritesheets, Jade templates, etc.

### Static middleware 中间件
- Sails 从Express使用中间件去serve assets.
- 可以在 /config/http.js 中配置中间件

#### index.html
- 默认渲染 index.html
- If you create assets/foo/index.html, it will be available at both http://localhost:1337/foo/index.html and http://localhost:1337/foo.

#### 优先级
- 要特别注意中间件安装在Sails router之后. 
- 举个例子, 如果你创建了 assets/index.html, 但是没有定义路由在 config/routes.js 文件中, 它会直接加载出主页. 但是如果你定义了一个路由, '/': 'FooController.bar', 这个路由将有优先权.

## Default Tasks(默认任务)
- asset pipeline 绑定在Sails中, 一组Grunt tasks让你的project更一致和产品化.
- 全部的前端asset工作流完全可定制, 已经提供了一些默认的task.
- Sails 更容易配置新的task去满足你的需求.

### Sails中Grunt默认配置的任务 
- LESS, JST, Coffeescript自动编译，
- 可选的自动化asset(资源)注入, 压缩, 串接
- web公众目录的创建
- 文件监测和同步
- 产品环境资源最优化

#### 默认的Grunt Task 行为

##### clean 
- clean out the contents in the .tmp/public
- 清除Sails项目 .tmp/public文件夹中的内容

##### coffee
- Compiles coffeeScript files from asset/js/ into Javascript and places them into .tmp/public/js/ directory.
- 编译asset/js里的coffeescript文件为js至.tmp/public/js目录.  (笔者注: Sails官网上错将文件夹名字写为assest)

##### concat 
- Concatenates javascript and css files, and saves concatenated files in .tmp/public/concat/ directory.
- 将javascript文件和css文件联系起来，保存在 ./tmp/public/concat/ 目录中.

##### copy
- dev task config 
- Copies all directories and files, except coffescript and less files, from the sails assets folder into the .tmp/public/ directory. (官网将except打成了exept)
- 从assets文件夹中 复制所有的目录和文件 到 .tmp/public/ 目录，除了coffescript和less文件
- build task config 
- 从 .tmp/public目录复制所有的目录和文件到 www 目录

##### cssmin
- 压缩css文件，放置到 .tmp/public/min/ 目录

##### jst
- 预编译 Underscore模板为 .jst 文件.
- 将HTML文件放进极小的javascript函数中,加速客户端的模板渲染,较少带宽.

##### less 
- 编译LESS文件为CSS
- 只有 assets/styles/importer.less 会被编译
- 允许你自己控制命令.
- 例如，在其他样式表之前导入你的依赖，变量，重置

##### sails-linker
- 自动为javascript文件注入 <script> 标签, css文件注入 <link> 标签
- 也会自动链接 使用<script>标签的预编译模板 的输出文件

##### sync
- A grunt task 保持目录同步
- 类似于 grunt-contrib-copy 只去复制那些被改变过的文件. 同步assets/ folder 和 .tmp/public/ 文件, 重写已经存在的一切.

##### uglify
- 压缩客户端javascript资源

##### watch
- 检测 assets 文件夹里的变化 ，重新执行相应的task(例如coffee,和less的编译)

## Disabling Grunt
- 禁止Grunt结合在Sails里，只要删除Gruntfile和tasks文件夹；
- 你也可以去禁用Grunt hook.
- 在 .sailsrc 文件里 设置 grunt的属性为 false
-  {
-   "hooks": {
-       "grunt": false
-    }
-  }

### 我可以为SASS, Angular, client-side Jade templates等定制tasks吗？
- 你只要去替换tasks目录里相关的grunt task，或者是增加一个.

#### 如果不要默认的assets文件夹
- 删除assets文件夹，和 grunt/register/ and grunt/config/ 文件夹里相关的前端task
- 或者 sails new myCoolApi --no-frontend 去省略assets文件夹和grunt里相关task

## Task Automation (自动化任务)
- task/ 目录里包含一套 Grunt tasks 和他们的配置
- Tasks 对于捆绑前端资源是十分有用的，（比如stylesheets,scripts,client-side markup templates）
- 也可以被用来自动化各种各样的重复性的开发琐事(chores)，从browserify编译到数据库的迁移. （笔者注：Browserify 通过预编译的方法，让Javascript前端可以直接使用Node后端的程序。）
- Sails为了方便，捆绑了一些默认的tasks,但是你可以选择差不多(literally)上百种插件，以最少的努力去让各方面的任务自动化.
- 如果还没有人开发你所需要的，你也可以自己成为作者，并发布你自己的Grunt插件到npm上.
- 如果之前还没有用过Grunt，先来查看一些 [入门文档](http://gruntjs.com/getting-started),因为它会告诉你怎样去创建一个Gruntfile,还有安装及使用Grunt插件.

### Asset pipeline
- Asset pipeline是你组织那些被注入到视图中资源的地方，在tasks/pipeline.js文件里.
- 配置这下资源很容易使用 grunt [task file configuration](http://gruntjs.com/configuring-tasks#files) and [wildcard/glob/splat patterns](http://gruntjs.com/configuring-tasks#globbing-patterns).
- 被分成3块
- 注入CSS文件
- CSS文件以<link>方式注入在HTML文档中，以<!--STYLES--><!--STYLES END--> 包裹
- 注入Javascript文件
- Javascript文件以<script>方式注入在HTML文档中，以<!--SCRIPTS--><!--SCRIPTS END--> 包裹
- 注入模板
- 会被编译为jst函数的html文件以<script>方式注入在HTML文档中，以<!--TEMPLATES--><!--TEMPLATES END--> 包裹
- 具体的Task配置参考其指导文档
