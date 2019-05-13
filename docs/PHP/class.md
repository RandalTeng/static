Daily Class Record
==

About \stdClass
--

``` php
- \stdClass
/**
 * 基础类对象可以随意添加任何属性
 */
$obj = new \stdClass();
$obj->a = 1;
$obj->b = 2;
var_dump($obj);
/**
 * output:
 * object(stdClass)[1]
 *   public 'a' => int 1
 *   public 'b' => int 2
 */
```

关于 `面向对象` `面向过程` `面向切片(切面)` 这些网上文章一大堆, 这里不做详细介绍

面向对象设计
--

抽象各种对象, 对象有对应的数据和方法(能力)

`封装` `继承` `多态`

> SRP 单一职责原则  
> OCP 开闭原则  
> LSP 里式替换原则  
> LoD 迪米特法则/最少知道法则  
> ISP 接口分离原则  
> DIP 依赖导致原则/控制反转(IoC) 

PS: 原则来源[knightsj](https://knightsj.github.io/2018/09/10/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1%E7%9A%84%E5%85%AD%E5%A4%A7%E8%AE%BE%E8%AE%A1%E5%8E%9F%E5%88%99/)

``` text

```

面向过程设计
--

> 流程的处理, 遇到一个问题解决一个问题

面向切片(切面)设计
--

> 这种在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是面向切面的编程。  
> 个人总结: 基层是对象, 对象遇到一个动作, 附加一个动作, 类似Yii2的behaviors, Events, Laravel的hook  
> 也即是监听者模式, 代理模式等的实现

// PS: 第一句来源[知乎](https://www.zhihu.com/question/24863332)
