# Zerg

## thinkPHP

### MVC 架构

1. 业务由专用的 model 层负责，通过业务层返回实体对象到 controller
2. controller 把实体对象组装成 view 之后返回给客户端

### 文件目录

- app: 应用目录
- config: 配置目录
- view: 视图目录
- route: 路由定制文件
- public: web 目录，包含入口文件，所有文件都能被访问到
- extend: 扩展类库目录
- runtime: 应用的运行时目录，包含日志和缓存文件
- vendor: Composor 类库目录
- think: 命令行入口文件
- .example.env: 环境变量示例文件
- composer.json: composer 定义文件

### URL 路径格式

> path info 格式

- http://servername/index.php/module/controller/action/[param/value...]
- index 是 ThinkPHP 默认的保留字，即使省略也会被补全
- url 不区分大小写
- 兼容模式会把 module 和 module 后的内容当成一个 search`http://servername/index.php?s=/module/controller/action/[param/value...]`
- 缺点
    1. 太长
    2. URL 路径暴露了服务器文件结构
    3. 不够灵活，不能支持 URL 语义化
  
> 路由模式

可以自己设置路由路径名，设置之后就不能通过 path info 访问被替换的路由了
```php
// 注册路由到News控制器的read操作
Route::rule('new/:id','News/read');

// 可以在rule方法中指定请求类型（不指定的话默认为任何请求类型有效），例如：
Route::rule('new/:id', 'News/update', 'POST');

// 或者直接使用方法
Route::get('new/:cate', 'News/category');
```

> 只使用路由模式

进入 config/route.php 设置 url_route_must 为 true

> 可以设置虚拟域名，在开发时代替冗长的url路径

- 编辑 xampp/apache/conf/extra/httpd-vhosts.conf 文件，加上
```apacheconf
<VirtualHost *:8080>
    DocumentRoot "E:\project\xampp\htdocs\Zerg\public"
    ServerName z.cn
</VirtualHost>
```
这样是让 apache 认出 z.cn 是干嘛的

- 如果想 apache 进入 localhost:8080 时依然访问到我们的项目，可以编辑 httpd-vhosts.conf 文件，加上
```apacheconf
<VirtualHost *:8080>
    DocumentRoot "E:\project\xampp\htdocs"
    ServerName localHost
</VirtualHost>
```

- 编辑 C:\windows\System32\drivers\etc，加上:
```hosts
127.0.0.1 z.cn
```

- 重启 apache

### 多模块
- TP 下所有的模块的类都要以 app/模块名/controller 作为命名空间，其中 app 是根命名空间。想要在创建类时自动补上命名空间，可以进入设置 -> 文件夹 -> 选择 app 设定它为源文件夹，它的 package name 设为 app

- 模块其实挺大的，不要一个功能一个模块


## PHPStorm 调试

- 在 public 目录下可以新建一个 info.php，之后通过浏览器访问 public/info.php 就能获取网站 php 的大概情况

```php
phpinfo();
```

进入生产环境时一定要把 info.php 删除

- 进入 info.php 之后，可以将其源代码复制进 xdebug 网页中进行分析然后下载对应版本并按提示一步一步操作
- 在 php.ini 的最后加入以下内容

```txt
[Xdebug]
zend_extension=xdebug
xdebug.remote_enable=1
xdebug.remote_handler=dbgp
xdebug.remote_mode=req
xdebug.remote_host=localhost
xdebug.remote_port=9000
xdebug.idekey="PHPSTORM"
```

之后重启 xampp

- 进入 PHPStorm 在编辑器的上方点击添加配置，之后进行新增配置，选择`PHP web 页面`，起始 URL 填写要编辑的页面，之后就能打断点调试

## 路由
- Route::rule('路由表达式', '路由地址', '请求类型', '路由参数（数组）', '变量规则（数组）')
- 路由数据获取
  ```PHP
  // 引入 Request 并获取数据
  class Test {
    // 直接通过参数获取
    public function hello($id, $name, $age) {}
  }
  
  // 引入 Request，通过 Request 获取 
  use think\facade\Request;
  
  class Test1 {
    // 直接通过参数获取
    public function hello() {
      // 实例化后通过 param 名获取值，如果不传字符串则获取所有 param 的数组
      Request::instance() -> param('id'); 
      // 通过 get 可以获取 get 请求的 query
      Request::instance() -> get(); 
      // 获取路径
      Request::instance() -> route(); 
      // 通过 input 助手函数获取
      $all = input('get.age');
    }
  }
  ```
  
## 产品功能

- 购物车保存在微信本地，如果想保存在数据库，则需要使用 websocket，优点是
  - 多端可以共享购物车数据
  - 服务器可以通过购物车分析用户数据
  
## SQL
- 不使用外键约束，如果想快速迭代产品，可以考虑不使用外键约束
- 使用假删除，不会物理删除数据。系统稳定性更高，更容易分析用户数据，此时外键约束就不好用了
- 设计数据库需要时间积累，需要一边写代码一边改，倾向于迭代方式



