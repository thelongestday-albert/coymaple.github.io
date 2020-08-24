# ThickPHP 3.2 集成 swagger

1、用composer安装Swagger
PHPSTUDY 用户可以按照如图所示的方式打开 composer 终端，输入如下命令

`composer require zircote/swagger-php: 2.0.*`

可能终端会提示 `Search for a package: `，这是因为composer.bat 脚本的缘故，只能接受一个参数。此处输入 `zircote/swagger-php`，提示 `Enter the version constraint to require(or leave blank to use the latest version)`。这里提示已经很详细了，输入版本号或空格。然后就开始下载相关包了。

2、在服务器部署官方 swagger ui 界面。官方下载地址如下，

下载后将根目录放到服务器根目录中。根目录下的dist目录下面的文件就是ui页面了。

3、打开dist目录下的 index.html ，修改 swagger.json 文件的路径。

```js
 const ui = SwaggerUIBundle({
	url: "swagger.json path",
  })
```

4、执行命令（实际执行的命令，要根据你那边的目录来确定），生成 swagger.json 

```
php D:/PHPSTUDY/PHPTutorial/tools/composer/vendor/zircote/swagger-php/bin/swagger D:/PHPSTUDY/PHPTutorial/WWW/shop/Application/Home/Controller -o D:/PHPSTUDY/PHPTutorial/WWW/swagger-ui/dist/swagger.json
```

第1个路径是你安装成功后组件的路径；

第2个路径是你想要生成这个目录下所有用swagger方式注释的php文件，把所有注释生成api文档；

第3个路径是你存放生成swagger.json的路径。

5、在controller中添加php注释，

```php
/**
 * @SWG\Swagger(
 *     schemes={"https"},
 *     host="www.omeals.cn",
 *     basePath="https://www.omeals.cn/home/",
 *     @SWG\Info(
 *         version="1.0.0",
 *         title="源膳小程序接口文档",
 *         description="源膳小程序接口文档",
 *         termsOfService=""
 *     )
 * )
 */
```

**@SWG\Swagger** 注解最好加在class上面

```php
/**
 * @SWG\Get(
 *     path="order/index.html",
 *     summary="生成订单",
 *     description="生成订单",
 * 		@SWG\Response(
 * 			response=200,
 * 			description="一个用户列表",
 * 	)
 * )
 */
```

**@SWG\Get** 注解加在控制器器方法上，其内部参数 description @SWG\Response 等都不能少，缺少在执行上一步的命令时会有提示。另外也不要忘记后面加 ","。



6、快速更新文档

```
<?php
namespace app\index\controller;

use think\Controller;
class Index extends Controller
{
    public function index(){
        $path = 'D:/WampServer/WWW/tpSwagger/tp5/application'; //你想要哪个文件夹下面的注释生成对应的API文档
        $swagger = \OpenApi\scan($path);
        // header('Content-Type: application/json');
        // echo $swagger;
        $swagger_json_path = 'D:/WampServer/WWW/tpSwagger/tp5/swaggerApi/swagger.json';
        $res = file_put_contents($swagger_json_path, $swagger);
        if ($res == true) {
           $this->redirect('http://localhost/tpSwagger/swagger-ui/dist/index.html');
        }
    }

}
```

7、ui也可以使用 knife4j 的，这个项目是专门给 spring boot 项目提供增强swagger文档的，项目内有几个可使用静态 swagger.json 文件渲染的前端页面，如根目录下面几个结尾单词是 front 的文件夹。

注意使用该项目时，需要为每一条接口指定 tags 和 operationId。

> https://doc.xiaominfo.com/guide/

## 参考文章
- [thinkphp5集成Swagger-PHP](http://www.thinkphp.cn/code/7012.html)
- [ThinkPHP5使用Swagger-php](https://www.cnblogs.com/shen55/p/10266103.html)
- [如何编写基于 Swagger-PHP 的 API 文档](https://learnku.com/laravel/t/7430/how-to-write-api-documents-based-on-swagger-php)
- [Search for a package: 的解决方法！](https://blog.csdn.net/qq_41408081/article/details/100767440)

<!-- `php C:/phpstudy_pro/WWW/vendor/zircote/swagger-php/bin C:/phpstudy_pro/WWW/tp5/application/index/controller -o C:/phpstudy_pro/WWW/knife4j-front/static/` -->

<!-- https://www.bbsmax.com/A/lk5aYewP51/ -->