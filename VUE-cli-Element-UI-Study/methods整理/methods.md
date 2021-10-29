[toc]

# 1. 多文件上传

```vue
<script>
	    async uploadFiles() {
	  // fileList: el-upload 中接收到的文件列表，可以通过el-upload 的状态改变钩子函数handleChange(file, fileList)拿到
      // 循环上传文件列表
      for (const file of this.fileList) {
        // 构造一个新的 FormData 对象，用于上传文件
        var data = new FormData();
        data.append("withdrawalId", this.formData.id);
        data.append("file", file.raw);
        data.append("node", 4);
        data.append("fileName", "价值评估");
        data.delete("token");
        // 向后台发送请求 
        await this.$api.post("接口url", data);
        loading.text = file.name + "上传完成"; // 加载项
      }
    },
</script>
```

- formData 相关知识：https://developer.mozilla.org/zh-CN/docs/Web/API/FormData

# 2. 表单提交验证

```vue
<script>
	      // 调用表单验证的回调，此时本方法最终被绑定在提交按钮上，则可以在最后加上此回调，直接触发表单中所有验证，返回一个valid(布尔值)，是否通过验证
      this.$refs.formData.validate((valid) => {
        if (valid) {
          // 通过所有表单验证的方法
          this.$message.success("通过所有表单验证！=");
        } else {
          // 有表单未通过验证的方法
          this.$message.error("有表单未通过验证，请检查表单！");
          return false;
        }
       });
</script>
```

