Yii2 Request Life
==

``` text
1. index.php  
2. 创建Application实例, 注册各种组件, 注册异常处理方法, 注册基础事件  
3. 解析请求路由, 处理路由转换  
4. 处理请求检测, 处理请求前事件, 如果返回失败, 终止请求  
5. 到达runAction(), 开始处理请求, 处理业务, 如果有未捕获异常, 触发异常处理  
6. 返回响应结果, 处理请求后事件, 处理响应体  
7. 发送响应头, 发送响应体, 处理响应后事件
```

PS: 请求生命周期图(来源: [筑梦悠然](https://blog.csdn.net/wuhuagu_wuhuaguo/article/details/80637277))  
![Yii2请求生命周期结构图](https://github.com/RandalTeng/static-page/blob/master/assets/images/public/yii2-request-life.png)