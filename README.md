
#  wuanEditor

# 使用

引用`wuanEditor.css`、`jquery.js`和`wuanEditor.js`之后，即可简单生成富文本编辑器。

## 生成编辑器(使用id)

使用div生成一个编辑器，wuanEditor将自动生成一个可编辑区域
```html
<div id = "div1" style="width: 100%; height: 200px; "></div>

<!--这里引用jquery和wuanEditor.js-->
<script type="text/javascript">
    var editor = new wuanEditor('div1');
    editor.create();
</script>
```

## 调整尺寸

wuanEditor生成编辑器时，默认宽度为100%,请手动设置编辑器高度

## 自定义菜单

可通过修改 editor.config.menus 来自定义编辑器的菜单的显示/隐藏，该操作必须在 editor.create() 方法之前
```html
<div id = "div1" style="width: 100%; height: 200px; "></div>

<!--这里引用jquery和wuanEditor.js-->
<script type="text/javascript">
    var editor = new wuanEditor('div1');
    // 自定义菜单
    editor.config.menus = ['link','img'];
    editor.create();
</script>
```
目前菜单只包括链接插入和图片上传，不定义菜单会使用默认菜单，自动包含链接和图片功能

## 图片上传
目前编辑器只提供集成插件的方式进行图片上传，以集成七牛云存储为例

```html
<div id = "div1" style="width: 100%; height: 200px; "></div>

<!--这里引用jquery,wuanEditor.js及七牛相关依赖-->
<script type="text/javascript">
    function uploadInit (){
		  var editor = this;
		  var btnId = editor.imgUploadBtnId;
		  var uploader = Qiniu.uploader({
          runtimes: 'html5,flash,html4', //上传模式,依次退化
          browse_button: btnId, //上传选择的点选按钮，**必需**
          uptoken_url: '/uptoken',  //Ajax请求upToken的Url，**强烈建议设置**（服务端提供）
          domain: 'http://xxx.xxx.xxx.xxx.clouddn.com/',//bucket 域名，下载资源时用到，**必需**
          max_file_size: '10mb', //最大文件体积限制
          flash_swf_url: '/javascripts/plupload/Moxie.swf', //引入flash,相对路径
          filters: {
             mime_types: [
             //只允许上传图片文件 （注意，extensions中，逗号后面不要加空格）
                {
                  title: "图片文件",
                  extensions: "jpg,gif,png,bmp"
                }
             ]
           },
           max_retries: 3, //上传失败最大重试次数
           chunk_size: '4mb', //分块上传时，每片的体积
           auto_start: true, //选择文件后自动上传，若关闭需要自己绑定事件触发上传
           init: {
              'FileUploaded': function(up, file, info){
                  var domain = up.getOption('domain');
                  var res = $.parseJSON(info);
                  var sourceLink = domain + res.key; //获取上传成功后的文件的Url
                 // 插入图片到editor
                  editor.command("insertImage",sourceLink);
                },
               'Error': function(up, err, errTip) {
                   //上传出错时,处理相关的事情
                   console.log('on Error');
                }
            }
       });
	  }
</script>
```
