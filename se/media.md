Media 本地媒体库
====================

**不兼容提醒**
*服务端版本 1.4.7+*

**继承关系**

``` 
\Tuanduimao\Model
		   |
\Tuanduimao\Media	      
```

**相关数据表**
`core_option`


**相关代码文件**
Media 类 `/service/lib/Media.php`
\Tuanduimao\Model\Media `/model/Media.php`

## 调用示例

```php
$media = new \Tuanduimao\Media();

// 上传图片
$rs = $media->uploadImage("/tmp/image.png"); // 本地文件
$rs = $media->uploadImage("http://some.url.com/image.png"); // 图片网址

// 裁切图片
$rs_crop = $media->crop($rs["media_id"], 100, 100, 200, 300);

// 压缩图片
$rs_resize = $media->resize($rs_crop["media_id"], 20, 30);


// 上传视频
$rs = $media->uploadVideo("/tmp/video.mp4"); // 本地视频文件
$rs = $media->uploadVideo("http://some.url.com/video.mp4"); // 视频网址
$rs = $media->saveVideoUrl("https://v.qq.com/x/cover/jzhtr2cgy35ejz0/v0024xn6ba9.html");  // 视频网站网址 (支持优酷、腾讯视频)

// 视频缩略图
// ["jpg"=>"缩略图地址...", "gif"=>"动图地址..."]
$info = $media->getVideoCover("/tmp/video.mp4");  // 视频文件地址


// 上传文件
$rs = $media->uploadFile("/tmp/file.pdf");  // 本地文件
$rs = $media->uploadFile("http://some.url.com/file.pdf");  // 文件网址



```

