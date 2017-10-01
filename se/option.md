Option 通用配置
====================

**不兼容提醒**
*服务端版本 1.4.8+*

**继承关系**

``` 
\Tuanduimao\Model
		   |
\Tuanduimao\Option	      
```

**相关数据表**
`core_option`



## 调用示例

```php
// 创建一个配置实例
$option = new \Tuanduimao\Option('org_name/app_name');

// 注册配置项
$option->register(
    "默认图片比例", 
    "image/ratio", 
    [
        "cover"=>["width"=>900,"height"=>500, "ratio"=>"9:5"], 
        "topic1"=>["width"=>null,"height"=>null, "ratio"=>"1:1"]
    ]
]);


// 修改配置项
$option->set("image/ratio", [
    "cover"=>["width"=>600,"height"=>700, "ratio"=>"9:5"], 
    "topic1"=>["width"=>null,"height"=>null, "ratio"=>"2:3"]
]);


// 读取配置项
$ratio = $option->get("image/ratio");
print_r( $ratio );

// 读取所有配置
$opts = $option->getAll();
print_r( $opts["map"] );
print_r( $opts["data"] );

```

## 方法

###  __construct() 构造

```php
/**
 * @param $app_name 命名空间( 应用名 org/name ) 默认值 tuanduimao/tuanduimao
 */
function __construct( $app_name );
```


### register() 注册配置 

```php
  /**
	 * 注册配置项 (一般在安装应用时调用)
	 * @param  string $name    配置项中文名称
	 * @param  string $key     配置项键 
	 * @param  mix    $value   配置项值 (支持数组)
	 * @param  string $app     命名空间( 应用名称， 默认为空使用创建对象时选用的命名空间)
	 * @return $this
	 */
	public function register( $name, $key, $value=null, $app=null); 
```

### unregister() 注销配置

```php
  /**
	 * 注销配置 (只能注销应用的配置, 一般在卸载应用时调用)
	 * @param  string $app  命名空间( 应用名称， 默认为空使用创建对象时选用的命名空间)
	 * @return $this
	 */
	public function unregister( $app=null );
```

### get() 读取配置项值

```php
  /**
	 * 读取配置项值 
	 * @param  string $key 配置项键
	 * @param  string $app 命名空间 (应用名称， 默认为空，使用创建对象时选用的命名空间)
	 * @return mix 配置项值
	 */
	public function get( $key, $app=null )
```


### set() 设定配置项值

```php
  /**
	 * 设定配置项值
	 * @param  string $key 配置项键
	 * @param  mix  $value 配置项值 (支持数组)
	 * @param  string $app 命名空间 (应用名称， 默认为空，使用创建对象时选用的命名空间)
	 */
	public function set( $key, $value, $app=null ) 
```


### getAll() 读取所有配置

```php
  /**
	 * 读取所有配置
	 * @param  string $app 命名空间 (应用名称， 默认为空，使用创建对象时选用的命名空间)
	 * @return array  ["map"=>..., "data"=>...]
	 */
	public function getAll( $app = null ) 
```

