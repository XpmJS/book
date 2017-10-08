Model 数据模型
====================

**继承关系**

``` 
\Tuanduimao\Model     
```

**相关代码文件**

Model类 `/service/lib/Model.php`
数据接口 `/service/lib/data-driver/Data.php`  
MySQL引擎 `/service/lib/data-driver/Common.php`  
MySQL引擎(Model建表) `/service/lib/data-driver/Database.php`  


## 调用示例

**自定义数据模型**

```php
// 通过继承 Model 创建一个数据模型
class Hello extends Model {
    // 绑定 core_hello 数据表
    function __construct( $param=[] ) {
        parent::__construct(['prefix'=>'core_'] , 'Database');
        $this->table('hello');
    }
    
    // 数据表结构
    function __schema() {
        
        // 字段类型参见
        // https://laravel.com/docs/5.3/migrations#creating-columns
        try {
            $this->putColumn( 'hello_id', $this->type('integer', ['unique'=>1] ) )
                 ->putColumn( 'name', $this->type('string', ['length'=>128] ) )
                 ->putColumn( 'value', $this->type('longText', ["json"=>true] ) )
                 ;
        } catch( Exception $e ) {
    			Excp::elog($e);
    			throw $e;
    		}
    }
    
    
    // 删除数据表
    function __clear() {
    		$this->dropTable();
    }
    
    // 重构创建数据方法，自动生成 hell_Id
    function create( $data ) {
        if ( $data['hello_id'] ) {
		      $data['hello_id'] = $this->nextid();
	       }
		  return parent::create( $data );
	}
	
}


// 数据增删改查
$hello = new Hello();

// 添加一条记录
$hello->create([
    "name"=>"测试数据"
    "value"=>[
        "world"=>"1",
        "remark"=>"这是一条测试数据"
    ]
]);

// 修改一条记录
$hello->updateBy("hello_id", [
    "hello_id"=>192,
    "value" => [
        "world"=>"39",
        "remark"=>"测试修改数据"
    ]
]);

// 检索记录
$resp = $hello->query()
      ->where("name", "like" , "%测试%")
      ->get()
      ->toArray()
    ;
    
// 标记删除记录
$hello->remove(192, "hello_id", true );

// 彻底删除记录
$hello->remove(192, "hello_id", false );

// 运行SQL
$hello->runsql("UPDATE {{table}} SET name=?", false, ["批量修改名称"]);

```

**访问已有数据表**

```php
use \Tuanduimao\Utils;

// 访问 Model 方法创建的数据表
$hello = Utils::getTab("hello", "core_", "Database");
$resp = $hello->query()
      ->where("name", "like" , "%测试%")
      ->get()
      ->toArray()
    ;

// 访问 其他系统创建的数据表
$hello = Utils::getTab("article", "wp_", "Common");
$resp = $hello->query()
      ->where("title", "like" , "%测试%")
      ->get()
      ->toArray()
    ;
    
```


## 方法

### create() 添加一条记录 

```php
  /**
	 * 添加一条记录
	 * @param  array $data 记录数组（需包含所有必填字段）
	 * @return array | boolean 
	 *     成功返回新记录  
	 *         [
	 *             "key"=>"value",
	 *             ...
	 *         ]
	 *     失败返回 false
	 * @sample:
	 * $resp = $hello->create([
	 *     "key"=>"value",
	 *     "key2"=>"DB::RAW(NOW())", // MySQL 函数
	 *     ...
	 * ]);
	 */
	public function create( $data  ) ;
```



### update() 根据数据表主键，修改数据记录 

```php
	/**
	 * 根据数据表主键，修改数据记录
	 * @param  array $data 记录数组（需修改的字段 map）
	 * @return array | boolean 
   *     成功返回更新后的记录数值  
	 *         [
	 *             "key"=>"value",
	 *             ...
	 *         ]
	 *     失败返回 false
	 * @sample:
	 * $resp = $hello->update([
	 *     "key"=>"value",
	 *     "key2"=>"DB::RAW(NOW())", // MySQL 函数
	 *     ...
	 * ]);
	 */
	public function update( $_id, $data );
```


### updateBy() 根据指定唯一索引，修改数据记录 

```php
	/**
	 * 根据指定唯一索引，修改数据记录
	 * @param  string $uni_key 唯一索引名称
	 * @param  array  $data    记录数组（ 需包含 uni_key 字段）
	 * @return array | boolean 
   *     成功返回更新后的记录数值  
	 *         [
	 *             "key"=>"value",
	 *             ...
	 *         ]
	 *     失败返回 false
   * @sample:
	 * $resp = $hello->updateBy("uni_key",[
	 *     "uni_key"=>"106",
	 *     "key"=>"value",
	 *     "key2"=>"DB::RAW(NOW())", // MySQL 函数
	 *     ...
	 * ]);    
	 */
	public function updateBy( $uni_key, $data ) ;
```

### createOrUpdate() 创建一条数据，如果存在则更新

```php
	/**
	 * 创建一条数据，如果存在则更新
	 * @param  array      $data        数据集合
	 * @param  array|null $updateColumns 待更新字段清单，为 null 则更新 data 中填写的字段。
	 * @return boolean 成功返回  true, 失败返回false
   * @sample:
	 * $resp = $hello->createOrUpdate([
	 *     "key"=>"value",
	 *     "key2"=>"DB::RAW(NOW())", // MySQL 函数
	 *     ...
	 * ]); 
	 */
	public function createOrUpdate(  $data,  $updateColumns = null ); 
```


### get() 根据数据表 ID 读取一条记录

```php
	/**
	 * 根据数据表 ID 读取一条记录
	 * @param  [type] $_id [description]
	 * @return array | boolean 
	 *     成功返回符合条件的记录数值
	 *         [
	 *             "key"=>"value",
	 *             ...
	 *         ]
	 *     失败返回 false
	 * @sample:
	 * $rs = $model->get(100);
	 */
	public function get( $_id );
```

### delete() 根据数据表主键，删除数据记录 

```php
	/**
	 * 根据数据表主键，删除数据记录
	 * @param  int  $_id      数据表主键
	 * @param  boolean $mark_only 是否为标记删除， 默认 fasle	
	 * @return boolean 成功返回 true, 失败返回 false
  	 * @sample:
  	 * $hello->delete(100);
	 */
	public function delete( $_id, $mark_only=false );
```


### remove() 根据数据表唯一索引数值，删除数据记录

```php
	/**
	 * 根据数据表唯一索引数值，删除数据记录
	 * @param  mix   $data_key  唯一索引数值
	 * @param  string  $uni_key   唯一索引键名，默认 "_id"	
	 * @param  boolean $mark_only 是否为标记删除， 默认 true 
	 * @return boolean 成功返回 true, 失败返回 false
	 * @sample:
  	 * $hello->remove(100, "uni_key");
	 */
	public function remove( $data_key, $uni_key="_id", $mark_only=true );
```


### select() 查询数据表, 返回结果集 

```php
	/**
	 * 查询数据表, 返回结果集
	 * @param  string $query  检索条件, 默认为空, 列出所有记录
	 * @param  array || string  $fields 字段清单， 默认为空数组，返回所有字段
	 * @return array | boolean 
	 *         成功返回符合条件的记录 
	 *         [
	 *             "data"=>[
	 *                 ["key"=>"value"],
	 *                 ...
	 *             ], 
	 *             "total"=>1000
	 *         ]  
	 *         失败返回 false
	 * @sample:
	 * $rows = $model->select( "WHERE title=? AND author=?",["title","author", "name"], ["标题", "张三"]);         
	 */
	public function select( $query="", $fields=[], $data=[] ) ;
```


### getData() 查询数据表, 仅返回结果集 

```php
	/**
	 * 查询数据表, 仅返回结果集
	 * @param  string $query  检索条件, 默认为空, 列出所有记录
	 * @param  array || string  $fields 字段清单， 默认为空数组，返回所有字段
	 * @param  array $data 变量数值
	 * @return array | boolean 
	 *     成功返回符合条件的记录数组
	 *     [
	 *         ["key"=>"value"],
	 *         ...
	 *     ]  
	 *     失败返回 false
	 * @sample:
	 * $rows = $model->getData( "WHERE title=? AND author=?",["title","author", "name"], ["标题", "张三"]);
	 */
	public function getData( $query="", $fields=[], $data=[] ) ;
```


### getLine() 查询数据表，返回最后一行数据 

```php
	/**
	 * 查询数据表，返回最后一行数据
	 * @param  string $where  检索条件, 默认为空, 列出所有记录
	 * @param  array || string  $fields 字段清单， 默认为空数组，返回所有字段
	 * @param  array $data 变量数值
	 * @return array | boolean 
    *     成功返回符合条件的记录数值  
	 *         [
	 *             "key"=>"value",
	 *             ...
	 *         ]
	 *     失败返回 false
	 * @sample:
	 * $rs = $model->getLine( "WHERE title=? AND author=?",["title","author", "name"], ["标题", "张三"]);
	 */
	public function getLine( $query="", $fields=[], $data=[]) ;
```


### getVar() 查询数据表，返回一行中指定字段的数值 

```php
	/**
	 * 查询数据表，返回一行中指定字段的数值
	 * @param  string  $field_name 字段名称
	 * @param  string $where  检索条件, 默认为空, 列出所有记录
	 * @param  array $data 变量数值
	 * @return array | boolean 成功返回符合条件的记录 map, 失败返回 false
	 * @sample:
	 * $name = $model->getVar("name", "WHERE title=? AND author=?", ["标题", "张三"]);
	 *
	 */
	public function getVar( $field_name, $where="", $data=[] ) ;
```


### query() 读取查询构造器 

兼容 Laravel Query Builder，等价于与 laravel `DB::table('table_name')` 
具体查询方法可查阅 laravel 文档。https://laravel.com/docs/5.0/queries

```php
	/**
	 * 读取查询构造器
	 * @see https://laravel.com/docs/5.3/queries
	 * @see https://laravel.com/docs/5.3/pagination
	 * @see https://github.com/illuminate/database/blob/master/Query/Builder.php
	 * @return \Illuminate\Database\Query\Builder 对象
	 */
	public function query( $db="read" , $include_removed=false ) ;
```

特别说明: 

#### selectRaw() 重构
`{table_name}` 自动增加前缀. 如数据表前缀为 `core_` 则 `{table_name}` 代表
`core_table_name`

```php
$model = Utils::getTab("hello", "somepre_");
$sql = $model->query()
            ->selectRaw("SELECT * FROM {hello} WHERE id=?", [100])
            ->getSql();
            
// 返回值
"SELECT * FROM somepre_hello WHERE id=100"
            
```



#### pgArray() 分页查询

$model->query()->paginate()->toArray()  迅捷函数
参阅: https://laravel.com/docs/5.3/pagination

```php
/**
 * @param  int $perpage 每页显示记录数量，默认为  20条
 * @param  array $count 分页记录总数 COUNT 字段默认 ["_id"]
 * @param  string $link 分页链接 "page"
 * @param  int $page 当前页码 默认为 1
 * @return array 查询结果
 *      [
 *          "total":80,
 *          "per_page": 20,
 *          "current_page": 4,
 *          "last_page": 4,
 *          "next_page_url": "http://laravel.app?page=2",
 *          "prev_page_url": null,
 *          "from": 1,
 *          "to": 15,
 *          "next":false,
 *          "prev":3,
 *          "curr":4,
 *          "last":4,
 *          "perpage":20
 *          "data":[
 *              {
 *                  },
 *              {
 *                  }
 *                  ...
 *          ]
 *      ]
 */
pgArray($perpage=20, $count=['_id'], $link='page', $page=1 )
```



### runsql() 运行 SQL 

 **`{{table}}` 指代模型数据表全名**

```php
	/**
	 * 运行 SQL
	 * @param  string $sql SQL语句
	 * @param  bool $return 是否返回结果
	 * @return mix $return = false， 成功返回 true, 失败返回 false; $return = true , 返回运行结果
	 * @sample:
	 * $resp = $model->runsql("UPDATE table_fullname SET name=?", false, ["批量修改名称"]);
	 * $resp = $model->runsql("UPDATE {{table}} SET name=?", false, ["批量修改名称"]);
	 */
	public function runsql( $sql, $return=false, $data=[] );
```


### getErrors() 读取错误记录栈 

```php
	/**
	 * 读取错误记录栈
	 * @return array 错误栈
	 */
	public function getErrors() ;
```


### nextid() 读取下一个自增 ID 

```php
	/**
	 * 读取下一个自增 ID
	 * @return string id
	 */
	public function nextid();
```



### getColumn() 读取数据表字段结构

```php
	/**
	 * 读取数据表 $column_name 结构
	 * @param  [type] $column_name [description]
	 * @return [Type] Type 结构体
	 */
	public function getColumn( $column_name );
```


### getColumns() 读取数据表列结构 

```php	
	/**
	 * 读取数据表列结构
	 * @param  [type] $column_name [description]
	 * @return [Type] Type 结构体
	 */
	public function getColumns();
```


### addColumn() 为数据表添加一列

```php
	/**
	 * 为数据表添加一列
	 * @param String $column_name 列名称 (由字符、数字和下划线组成，且开头必须为字符)
	 * @param Type   $type  Type 结构体
	 * @return $this
	 */
	public function addColumn( $column_name, $type );
```


### alterColumn() 修改数据表字段结构 

```php
	/**
	 * 修改数据表 $column_name 列结构
	 * @param String $column_name 列名称 (由字符、数字和下划线组成，且开头必须为字符)
	 * @param Type   $type        Type 结构体
	 * @return $this
	 */
	public function alterColumn( $column_name, $type );
```



### putColumn() 替换数据表字段结构（ 如果字段不存在则创建)

```php
	/**
	 * 替换数据表 $column_name 列结构（ 如果列不存在则创建)
	 * @param String $column_name 列名称 (由字符、数字和下划线组成，且开头必须为字符)
	 * @param Type   $type        Type 结构体
	 * @return $this
	 */
	public function putColumn( $column_name, $type );
```


### dropColumn() 删除数据表字段 

```php
	/**
	 * 删除数据表 $column_name 列
	 * @param String $column_name 列名称 (由字符、数字和下划线组成，且开头必须为字符)
	 * @param boolen $allow_not_exists 数据表是否存在
	 * @return $this
	 */
	public function dropColumn( $column_name, $allow_not_exists=false );
```
	

### type() 格式化 Type 结构体 

字段类型名称兼容 Laravel columns.
https://laravel.com/docs/5.3/migrations#columns

```php
	/**
	 * 格式化 Type 结构体
	 * @param  string $name   字段类型名称
	 * @param  array  $option 字段类型配置
	 *     $option["unique"]  boolen 是否为唯一索引，默认 fasle
	 *     $option["index"]  boolen 是否为索引，默认 fasle
	 *     $option["primary"]  boolen 是否为 Primary ，默认 fasle
	 *     $option["json"] boolen 是否为 JSON字段，默认 fasle
	 *     $option["default"] string  默认值 
	 *         $option["default"] = "DB::RAW:CURRENT_TIMESTAMP" 当前时间戳
	 *         $option["default"] = "DB::RAW(NOW())" MySQL 函数
	 *     $option["null"]  boolen 是否允许为空，默认 true
	 *     $option["unsigned"]  boolen 是否为无符号型, 默认false
	 *     
	 *     其他类型相关参数：
	 *     "char"=>["length"]
	 *     "decimal"=>["precision", "scale"]
	 *     "double"=>["total", "decimal"]
	 *     "enum"=>["enum"]
	 *     "float"=>["total", "decimal"]
	 *     "string"=>["length"]
	 *     
	 *     @see https://laravel.com/docs/5.3/migrations#columns
	 *     
	 * @return array 
	 * 
	 */
	public function type( $name, $option );
```


### table() 设定当前数据表名称 

```php
	/**
	 * 设定当前数据表名称
	 * 
	 * @param string $table 数据表名称
	 * return $this
	 */
	public function table( $table );
```


### tableExists() 检查数据表是否存在 

```php
	/**
	 * 检查数据表是否存在
	 * @return boolen 存在返回 true , 不存在返回false
	 */
	public function tableExists();
```


### dropTable() 删除数据表 

```php
	/**
	 * 删除数据表
	 * @return boolen 成功返回 true ,失败返回false
	 */
	public function dropTable();
```


### db() 读取数据库连接 

```php
	/**
	 * 快速读取数据库连接
	 * 
	 * @param  string $conn 'write'/'read' 
	 * @return conn
	 */
	public function db( $conn='write' );
```

