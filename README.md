# drop-image-upload 
## html5图片拖拽上传
##### 1. 在页面放置拖拽区域、图片显示区域及上传按钮。
```html
	<div id="dropImgZone">把图片拖拽到此区域</div>
	<ul id="showImgList"></ul>
	<button id="uploadBtn">上传</button>
```

##### 2. 定义变量
```javascript
var dropImgZone = $("#dropImgZone"),
	showImgList = $("#showImgList"),
	uploadBtn = $("#uploadBtn"),
	// 存贮图片数据
	imgData = [],
	// 存贮图片html结构数据
	imgHtmlData = [];
```
##### 3. 图片拖拽过程，先阻止事件默认行为，不然的话图片会直接覆盖原页面。
```javascript
dropImgZone.on('dragover', function(e) {
	e.stopPropagation();
	e.preventDefault();		
});
```
##### 4. 图片拖拽到区域放下时
```javascript
dropImgZone.on('drop', function(e) {
	e.stopPropagation();
	e.preventDefault();	
	var files = e.originalEvent.dataTransfer.files,
		reader = new FileReader();
	for (var i = 0, file; file = files[i]; i++) {
		// 读取文件
		reader.readAsDataURL(file);
		imgData.push(file);
		reader.onload = function(e) {
			imgHtmlData.push('<li><img src="'+ e.target.result +'" alt="" /><li>');
			showImgList.html(imgHtmlData.join(''));
		};
	}
});
```
##### 5. 提交上传图片。
```javascript
uploadBtn.on('click', function() {
    var formData = new FormData();
	formData.append("img", imgData);
	$.ajax({
		url: '/path/to/file',
		type: 'POST',
		dataType: 'json',
		data: formData,
		// 告诉jQuery不要去处理发送的数据
		processData: false,  
		// 告诉jQuery不要去设置Content-Type请求头
		contentType: false   
	})
	.done(function() {
		console.log('success');
	})
	.fail(function() {
		console.log('error');
	})
	.always(function() {
		console.log('complete');
	});
   });
```