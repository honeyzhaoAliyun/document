# 实现分布式文件上传功能
## 概述
继续文章消费者服务，以下为 form.html 中的关键代码

## 引入所需的 CSS
```
<link rel="stylesheet" th:href="@{{adminlte}/plugins/dropzone/dropzone.css(adminlte=${adminlte})}" />
<link rel="stylesheet" th:href="@{{adminlte}/plugins/dropzone/min/basic.min.css(adminlte=${adminlte})}" />
<link rel="stylesheet" th:href="@{{adminlte}/plugins/wangEditor/wangEditor.min.css(adminlte=${adminlte})}" />
```
## 引入所需的 JS
```
<script th:src="@{{adminlte}/plugins/dropzone/min/dropzone.min.js(adminlte=${adminlte})}"></script>
<script th:src="@{{adminlte}/plugins/wangEditor/wangEditor.min.js(adminlte=${adminlte})}"></script>
```
## 表单元素
```
<div class="form-group">
    <label class="col-sm-2 control-label">封面缩略图片</label>

    <div class="col-sm-10">
        <input id="thumbImage" name="thumbImage" class="form-control" placeholder="封面缩略图片" />
        <div id="dropz" class="dropzone"></div>
    </div>
</div>
```
## 调用 Dropzone 上传
```
<script>
    App.initDropzone({
        id: "#dropz",
        url: "http://localhost:8505/upload",
        init: function () {
            this.on("success", function (file, data) {
                $("#thumbImage").val(data.fileName);
            });
        }
    });
</script>
```