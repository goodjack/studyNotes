### 使用 Dropzone 初始化时显示图片

```javascript
new Dropzone({
    // 在 init 方法内初始化
    init: function () {
                this.on('addedfile', function (file) {
                    let changeButton = Dropzone.createElement("<button class='btn-custom'>更换</button>");
                    let _this = this;

                    changeButton.addEventListener('click',function (e) {
                        e.preventDefault();
                        e.stopPropagation();

                        _this.removeFile(file,changeButton);
                    });

                    file.previewElement.parentElement.parentElement.appendChild(changeButton);
                });

              // 需要在 addedfile 后添加，因为需要使用自定义的 addedfile 事件，如果在顶部添加的话，会使用 Dropzone 的默认方法
                let mockfile = {
                    name:"1.mp3",
                    size:'2312',
                    type:"mp3"
                };
        // 第一个参数是 Dropzone 支持的事件
                this.emit('addedfile',mockfile);
        		this.emit('thumbnail',mockfile,url),	// 第三个参数填写 url
               
})
```



