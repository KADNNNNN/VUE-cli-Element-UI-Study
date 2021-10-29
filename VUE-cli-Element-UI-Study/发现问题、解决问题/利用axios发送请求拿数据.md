1. 安装 axios，并在App.vue（要用该数据的组件） 中导入 axios

2. 在 methods 方法中，定义 initCartList 函数请求列表数据 （自命名）

3. 在 created 生命周期函数中，调用步骤2封装的 initCartList  函数

   

注意：只要请求回来的数据，在页面渲染期间要用到，则必须要转存到 data 中

```html
axios({
        method: "post",
        url: "http://192.168.20.112:9004//getAllWordTemplate.do",
      }).then((res) => {
        this.tableData = res.data.data;
        console.log(res.data.data);
      });
```

