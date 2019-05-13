Daily Syntax Record
==

About Syntax
--

``` php
- foreachlist
/**
 * 可以直接用快捷list拆分数组, 达到快速二次循环的效果
 */
$arr = [['a' => 1, 'b' => 3], ['a' => 2, 'b' => 3]];
foreach ($arr as ['a' => $a, 'b' =>$b]) {
    echo $a, $b, ' ';
}
// output: 13 24

```
