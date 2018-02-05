# 第8节 雅虎35条

[英文原版链接](https://developer.yahoo.com/performance/rules.html)，若是觉得本文哪里不好还请指出，以便及时修改

## 目录（分7类，共35条）

### 内容

1. [内容]尽量减少HTTP请求数
2. [内容]避免重定向
3. [内容]减少DNS查找
4. [内容]让Ajax可缓存
5. [内容]延迟加载组件
6. [内容]预加载组件
7. [内容]减少DOM元素的数量
8. [内容]跨域分离组件
9. [内容]尽量少用iframe
10. [内容]杜绝404

### 服务器

1. [服务器]使用CDN（Content Delivery Network）
2. [服务器]添上Expires或者Cache-Control HTTP头
3. [服务器]Gzip组件
4. [服务器]配置ETags
5. [服务器]尽早清空缓冲区
6. [服务器]对Ajax用GET请求
7. [服务器]避免图片src属性为空

### js\css

1. [css]把样式表放在顶部
2. [js]把脚本放在底部
3. [css]避免使用CSS表达式
4. [js, css]把JavaScript和CSS放到外面
5. [js, css]压缩JavaScript和CSS
6. [js]去除重复脚本
7. [js]尽量减少DOM访问
8. [js]用智能的事件处理器
9. [css]选择舍弃@import
10. [css]避免使用滤镜

### 图片

1. [图片]优化图片
2. [图片]优化CSS Sprite
3. [图片]不要用HTML缩放图片
4. [图片]用小的可缓存的favicon.ico（P.S. 收藏夹图标）

### Cookie

1. [cookie]给Cookie减肥
2. [cookie]把组件放在不含cookie的域下

### 移动端

1. [移动端]保证所有组件都小于25K
2. [移动端]把组件打包到一个复合文档里
