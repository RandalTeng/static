Stream about PHP
==

PHP 文件流
--

``` php
/**
 * 获取整个客户端输入流
 * 可以用来获取客户端输入的JSON/XML等数据
 */
$reqInput = file_get_contents('php://input'); // The PHP input stream.
$cmdInput = file_get_contents('php://stdin'); // Client mode, the stand input of script.
$input = stream_get_contents('php://input');

/**
 * 获取服务器输出流
 * 刷新输出缓冲区/写入数据之类的作用
 */
$fd = fopen('php://output', 'w'); // The PHP output stream.
$fd = fopen('php://stdout', 'w'); // Client mode, stand output stream.

fwrite($fd, 'content\n'); // output: content
fclose($fd);


/**
 * 一些用来获取远程资源的函数/方法
 */
$curl = curl_init();
$content = $curl->send();
$content = file_get_contents();
$content = stream_get_contents('php://filter/read=<format>/resource=<url>');
$content = readfile();
```

    Every Stream Should Be Closed When Response Ended Or Script Exit(in php)