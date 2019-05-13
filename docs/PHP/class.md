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
