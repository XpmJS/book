图集 API 手册
==========================================

### 创建图集对象

```php
$g = new \Mina\Pages\Model\Gallery();
```

### 创建/更新一个新图集

数据表 `mina_pages_gallery`

```php
// 生成图集 ID 
$id = $g->genGalleryId();  

// 图集数据结构
$struct = [
    // 摘要信息
    "page" => [
        // 图集ID
        "id" => $id,
        // 图集标题
        "title" => "又一个图集", 
        //背景图片地址
        "bgimage" => "/s/mina/pages/static/defaults/804X1280.png", 
        
        // 背景颜色和透明度  RGBA 格式 
        "bgcolor" => "rgba(254,254,254,1)",
        
        // 背景图关联栏位 -1:不关联, 0:A, 1:B, 2:C ...
        "origin" => -1
    ],
    
    // 图集上面的元素
    "items" => [
        [   
             // 元素类型名称 有效值 qrcode: 二维码 image:图片 text:文本
					"name"=>"qrcode",
					// 元素参数
					"option"=>[
					   // 二维码地址关联栏位 -1:不关联, 0:A, 1:B, 2:C ...
					   "origin"=>3,
					   // 二维码内容
						"text" => "https://www.minapages.com", 
						// 二维码宽高
						"width"=>100, "height"=>100, 
						// 二维码类型 url:网址 wxapp:小程序吗
						"type"=>'url'
					], 
					// 元素坐标 
					"pos"=> ["x"=>676, "y"=>1149] ],
			[
			     // 元素类型名称 有效值 qrcode: 二维码 image:图片 text:文本
					"name"=>"image", 
					// 元素参数
					"option"=> [
        	     // 图片地址关联栏位 -1:不关联, 0:A, 1:B, 2:C ...
					   "origin"=>2,
						"width"=>804, "height"=>423,
					], 
					// 元素坐标 
					"pos"=> ["x"=>0, "y"=>0] ],
			[
					"name"=>"text", 
					"option"=>[
					   "origin"=>0,
					   // 文本框大小
						"width"=>644, "height"=>52, 
                // 字体
						"font"=>1,  
						//  文字大小
						"size"=>48,
						// 文字颜色  （ RGBA格式 ）
						"color"=> "rgba(35,35,35,1)", 
						// 文字背景 （RGBA格式）
						"background"=>"rgba(255,255,255,0)", 
					   // 排列方式 横排/竖排
						"type"=>"horizontal" 
					], 
					"pos"=> ["x"=>80, "y"=>502]],
    ]
];

// 将 struct 转换为可以入库格式
$gallery_data =  $g->editorToGallery( $struct );

// system = 1 代表系统创建模块，不可以手工删除 （非必填）
$gallery_data['system'] = 1;

//  hidden = 1 不出现在图集列表 ( 非必填 )
$gallery_data['hidden'] = 1;

// param = xxxx 用于信息检索（非必填）
$gallery_data['param'] = 'some param';

// 数据入库，图集创建/更新完毕
$rs = $g->save( $gallery_data );

$gallery_id = $rs['gallery_id'];
```

### 向图集中添加一组图片

数据表 `mina_pages_gallery_image`

```php

// 图片数据结构 
$images_struct = [
			[
			 "A"=>"标题1",  
			 "B"=>"/s/mina/pages/static/defaults/950X500.png",
			 "C"=>"https://1.minapages.com"],
			[
			 "A"=>"标题2",  
			 "B"=>"/s/mina/pages/static/defaults/950X500.png",
			 "C"=>"https://2.minapages.com"]
];

// 转换成可入库格式
$images_data = $g->genImageData($images_struct);
$extra = ["param"=>"a group image"];


// 将图片信息入库 
// $gallery_id 图集ID
// $images_data 图片数据信息
// $extra 扩展图片信息
$resp = $g->createImages( $gallery_id, $images_data, $extra );

foreach( $resp as $rs ) {
    $im = $rs['data'];
    $image_id = $rs['image_id'];
    // 生成图片文件
    $g->makeImage($image_id);
}

```


### 删除图集、图片等

```php
  /**
	 * 删除图集
	 * @param  string $gallery_id 图集ID
	 * @return boolean	
	 */
	function rm( $gallery_id );
	
	/**
	 * 删除图集中所有图片
	 * @param  string $gallery_id 图集ID
	 * @return boolean	
	 */
	function rmImages( $gallery_id );
	
	/**
	 * 删除图集中符合参数的图片
	 * @param  string $param 参数
	 * @return boolean	
	 */
	function rmImagesByParam( $param );
	
``` 


###  查询图片
 
```php
/**
 * $query 查询参数
 * $query['keyword']  按关键词检索
 * $query['gallery_id']  按图集检索
 * $query['param']  按参数检索
 */
function getImages( $page=1, $query=[],  $perpage=12 );

```


### 更多内容参考

`/mina/pages/model/Gallery.php`

