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
- async······await 处理异步 
  - .then（res =>{  }  ）；  获取请求成功的返回值
  - .catch （err => {  }  )  ； 捕捉异常

# 2. 限制文件上传类型 

已 el-upload 组件为例，el-upload 限制上传文件格式有两种方法

## 2.1  js 方法实现

```vue
<template>
	<el-upload
      class="avatar-uploader"
      action="https://jsonplaceholder.typicode.com/posts/"
      :show-file-list="false"
      :on-success="handleAvatarSuccess"
      :before-upload="beforeAvatarUpload">
      <img v-if="imageUrl" :src="imageUrl" class="avatar">
      <i v-else class="el-icon-plus avatar-uploader-icon"></i>
    </el-upload>
</template>
<script>
  export default {
    data() {
      return {
        imageUrl: ''
      };
    },
    methods: {
        // :before-upload  
      beforeAvatarUpload(file) {
        const isJPG = file.type === 'image/jpeg';
        const isLt2M = file.size / 1024 / 1024 < 2;

        if (!isJPG) {
          this.$message.error('上传头像图片只能是 JPG 格式!');
        }
        if (!isLt2M) {
          this.$message.error('上传头像图片大小不能超过 2MB!');
        }
        return isJPG && isLt2M;
      }
    }
  }
</script>
```

## 2.2 accept 属性（推荐）

```vue
<template>
	  <el-upload
        action="alart"
        name="评估报告"
        list-type="picture-card"
        accept=".jpg,.png"
        :auto-upload="false"
        :file-list="files"
        :on-remove="handleRemove"
        :on-change="handleChange"
      >
      </el-upload>
</template>
```

- 通过 `accept=".jpg,.png"`  限制用户上传时可选择的文件类型。

# 3. 表单验证

```vue
<template>
  <el-form
    :model="formData"
    :rules="rule"
    ref="ruleData"
  >
    <el-form-item class="numrule" label="身份证号码:" prop="idCard">
      <el-input type="number" v-model="formData.idCard" />
    </el-form-item>
    <el-form-item label="电话号码:" prop="tel">
      <el-input v-model="formData.tel" clearable />
    </el-form-item>
    <el-form-item label="姓名:" prop="proposer">
      <el-input v-model="formData.proposer" clearable />
    </el-form-item>
  </el-form>
</template>
<script>
	data() {
    return {
      rule: {
        // 身份证号码验证
        idCard: [{
            required: true,
            pattern: /^[1-9]\d{7}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])\d{3}$|^[1-9]\d{5}[1-9]\d{3}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])\d{3}([0-9]|X)$/,
            message: "请输入正确的身份证号码",
            trigger: "blur",
        }, ],
        // 手机号码验证
        tel: [{
            required: true,
            pattern: /^1[34578]\d{9}$/,
            message: "请输入正确的手机号码",
            trigger: "blur",
        }, ],
        // 中文验证
        proposer: [{
            required: true,
            pattern: /^[\u4e00-\u9fa5]{0,}$/,
            message: "请输入中文",
            trigger: "blur",
        }, ],
      },
    }  
</script>
```

- 其中  `:model="formData" `  `prop="idCard"`  `v-model="formData.idCard"`   以及 data 中 rule 里面的属性有对应关系，rule 中属性验证 porp 对应的值。而他们都来自于 formData

## 3.1 提交 触发 表单验证

```vue
<template>
	<!-- 首先！使用 ref 对校验表单进行标记 ！！-->
	<el-form ref="addRuleForm">...(el-form-item)...</el-form>
</template>
<script>
	  // 调用表单验证的回调，此方法最终被绑定在提交按钮的点击事件上，点击提交直接触发表单中所有验证，返回一个valid(布尔值)，表示所欲表单是否通过验证，若有表单未通过验证，则返回false
      // addRuleForm：为ref标记的表单
      this.$refs.addRuleForm.validate((valid) => {
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

## 3.2 validate 在任意表单项被校验后触发

```vue
<template>
	<el-form @validate="validate">...(el-form-item)...</el-form>
</template>
<script>
  export default {
    methods: {
      validate(boolean, object) {
        // boolean：校验是否通过（true or false）, object：被校验对象
      }
    }
  };
</script>
```

# 4. 预览

## 4.1 图片预览

```vue
<template>
  <div>
      <!-- slot-scope="scope" 放在 template 中，获取当前行的信息 -->
    <template slot-scope="scope">
      <el-button
        size="mini"
        type="primary"
        @click="handlePreview(scope.$index, scope.row)"
        >预览</el-button>
    </template>
      <!-- 图片预览弹框 -->
    <el-dialog
      :append-to-body="true"
      :visible.sync="dialogVisible"
      width="80%">
      <img width="100%" :src="fileListObj.fileUrl" />
    </el-dialog>
  </div>
</template>
<script>
export default {
  data() {
    return {
      // 预览文件暂存对象
      fileListObj: {
        fileUrl: "",
      },
      dialogVisible: false,
    };
  },
  methods: {
    // 要件列表文件预览方法
    handlePreview(index, row) {
        this.fileListObj.fileUrl = row.url; // row(当前行信息)中的url
        this.dialogVisible = true; // 开启弹框
      }
    },
  },
};
</script>
```

## 4.2 PDF 预览

```vue
<template>
  <div>
    <template slot-scope="scope">
        <!-- 按钮 -->
      <el-button
        type="primary"
        @click="handlePreview(scope.$index, scope.row)"
        >预览</el-button>
    </template>
    <el-dialog
      :append-to-body="true"
      :visible.sync="dialogVisible"
      width="80%">
        <!-- pdf 预览 -->
      <InfoPreview  :data="fileListObj.pdfUrl" />
    </el-dialog>
  </div>
</template>
<script>
export default {
  data() {
    return {
      // 预览文件暂存对象
      fileListObj: {
        pdfUrl: "",
      },
      dialogVisible: false,
    };
  },
  methods: {
    // 要件列表文件预览方法
    handlePreview(index, row) {
      // console.log(row);
      if (row.fileName == "协议合同") {
        this.fileListObj.pdfUrl = row.url;
        this.dialogVisible4 = true;
      } else {
        this.fileListObj.fileUrl = row.url;
        this.dialogVisible3 = true;
      }
    },
  },
};
</script>
```

# 5. 文件下载

```vue
<template>
  <div>
    <template slot-scope="scope">
      <el-button
        size="mini"
        type="success"
        @click="handleDownload(scope.$index, scope.row)"
        >下载</el-button>
    </template>
  </div>
</template>
<script>
    handleDownload(index, row) {
      // 拿到文件的格式 (取文件url的后四位)
      var Type = row.url.slice(-4)
      var url = row.url;
      const link = document.createElement("a");
      fetch(url)
        .then((res) => res.blob())
        .then((blob) => {
          link.href = URL.createObjectURL(blob);
          // 下载Type格式的文件
          link.download = row.fileName + Type;
          document.body.appendChild(link);
          link.click();
          document.body.removeChild(link);
        });
    },
</script>
```

- 关于 fetch( ) 方法的讲解：https://blog.csdn.net/qq_36754767/article/details/89645041

  
