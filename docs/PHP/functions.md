Daily Function Record
==

About Array
--

``` php
/**
 * 回调处理数组, 函数处理结果作为元素插入结果数组
 * 注意: 回调参数为原函数除回调函数外所有参数, 每次
 *      取参数数组一个元素
 * 注意: 单纯就效率而言, array_map并没有foreach快
 *      (array_map有函数调用栈)
 */
array_map(callable $callback, array $array1[, ...]): array

/**
 * 根据自定义函数过滤数组, 函数结果为true的值会被返
 * 回到结果数组内, 未传递回调函数的情况下, 返回输入
 * 数组内元素不为空的结果
 */
array_filter(array $input[, callable $callback]): array

/**
 * 打乱数组顺序, 配合 array_slice 获取指定长度的随
 * 机结果
 */
shuffle(array &$input): array
```

About String
--

``` php
/**
 * 二进制安全比较字符串(区分大小写)
 * 配套的 strcasecmp 不区分大小写
 * 二进制安全: 以原始二进制处理字符串, 不使用额外的字符串判断字符串(主要指结尾null或结尾字符\0)
 */
strcmp(string $str1, string $str2): int
```