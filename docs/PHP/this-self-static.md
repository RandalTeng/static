PHP - $this/self/static 后期静态绑定
==

PHP的self/static(后期静态绑定)访问关键字很有意思
这两个关键字, 一般情况下的读取都是准确的, 你都能推测到它们指定的数据或方法具体是哪个
但是, 加上public/protected/private, 这个行为就不好预测了
这里尝试用代码说明相关的处理逻辑, 接下来会比较绕, 注意拿好小本本

官方解释
--

``` php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test(); // A
```

> 准确说，后期静态绑定工作原理是存储了在上一个“非转发调用”（non-forwarding call）的类名。
> 当进行静态方法调用时，该类名即为明确指定的那个（通常在 :: 运算符左侧部分）；当进行非静态方法调用时，即为该对象所属的类。
> 所谓的“转发调用”（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call()。
> 可用 get_called_class() 函数来得到被调用的方法所在的类名，static:: 则指出了其范围。
>
> 使用 self 或者 \__CLASS__ 对当前类的静态引用，取决于定义当前方法所在的类
> 表示运行时最初调用的类, 简单地说，这个关键字能够让你在上述例子中调用 test() 时引用的类是 B 而不是 A
>
> **NOTE1**: 在非静态环境下，所调用的类即为该对象实例所属的类。
> 由于 $this-> 会在同一作用范围内尝试调用私有方法，而 static:: 则可能给出不同结果。
> 另一个区别是 static:: 只能用于静态属性。
>
> **NOTE2**: 后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。
> 另一方面，如果静态调用使用 parent:: 或者 self:: 将转发调用信息。

PS: 来源[PHP官方手册](https://www.php.net/manual/zh/language.oop5.late-static-bindings.php)

以上就是官方对于后期静态绑定的说明, 没有详细说明**转发调用信息**是个什么情况,  
加上权限修饰关键字后, **不同结果**也没有详细说明

个人理解
--

!!! IMPORTANT
写了三天, 没弄明白某些情况的读取异常是什么原因, 打算暂时先去看下源码里面关于这一块的处理或者stack上面问一波[无奈脸]
StackOverflow 问题被标记重复, 无奈的一批

首先要明确一下 $this/self/static 的访问机制

``` text
访问属性的时候:
    $this: 访问源(方法体)是谁, 属于谁(归属类), 目标属性在他那有没有, 有就访问没有就向上找
    self: 访问源(方法体)是谁, 属于谁(归属类), 目标属性在他那有没有, 有就访问没有就向上找
    static: 想访问的目标类是谁, 目标属性在他那有没有, 有就访问没有就向上找
访问方法的时候:
    $this: 想访问的目标类是谁, 目标方法在他那有没有, 有就访问没有就向上找
    self: 访问源(方法体)是谁, 属于谁(归属类), 目标方法在他那有没有, 有就访问没有就向上找
    static: 访问源(方法体)是谁, 属于谁(归属类), 目标类是谁, 目标方法在他那有没有, 有就访问没有就向上找

PS: 属性方法, 只要找到, 就开始检查权限修饰关键字, 有没有权限访问, 有就执行, 没有就返回无权限
不会向上查继续找有权限访问的方法, PHP不允许子级属性方法的访问权限修饰关键字比父级范围大
子级的属性方法没权限访问, 父级属性方法一定没权限访问
```

代码说明
--

``` php
/**
 * 情况一: 正常情况, 调用的都是自身的属性方法, 没有的时候就往上找, 找到为止
 * 说明一下, 父级class标为private的属性方法, 是我们要试验的属性方法使用方式
 * public方法是我们的函数入口, 访问源, 提供不同的调用方式
 * 每段代码后面的说明部分, 重点说明访问预期不好确定的访问方式
 *
 * getA: $this访问类属性
 * getSfB: self访问类静态属性
 * getStB: static访问类静态属性
 * getC: $this访问类方法
 * getSfD: self访问类静态方法
 * getStD: static访问类静态方法
 */
class ParentClass {
    private $a = 'pa';
    private static $b = 'psb';

    private function c() {
        echo 'pc', '<br>';
    }

    private static function d() {
        echo 'psd', '<br>';
    }

    public function getA() {
        echo $this->a, '<br>';
    }

    public static function getSfB() {
        echo 'pSelf: ', self::$b, '<br>';
    }

    public static function getStB() {
        echo 'pStatic: ', static::$b, '<br>';
    }

    public function getC() {
        echo 'pf: ', $this->c();
    }

    public static function getSfD() {
        echo 'pSelf: ', self::d();
    }
    public static function getStD() {
        echo 'pStatic: ', static::d();
    }
}
$prt = new ParentClass();
$prt->getA(); // output: pa
ParentClass::getSfB(); // output: pSelf: psb
ParentClass::getStB(); // output: pStatic: psb
$prt->getC(); // output: pf: pc
ParentClass::getSfD(); // output: pSelf: psd
ParentClass::getStD(); // output: pStatic: psd
```

``` php
/**
 * 情况二: 单纯继承, 所有属性不变
 * getA: 访问父级属性
 * getSfB: 访问父级静态属性
 * getStB: 访问异常
 * getC: 访问父级方法
 * getSfD: 访问父级静态方法
 * getStD: 访问父级静态方法
 */
class ChildClass extends ParentClass {
}
$chd = new ChildClass();
$chd->getA(); // output: pa
ChildClass::getSfB(); // output: pSelf: psb
ChildClass::getStB(); // output: Error
$chd->getC(); // output: pf: pc
ChildClass::getSfD(); // output: pSelf: psd
ChildClass::getStD(); // output: pStatic: psd
```

    ChildClass::getStB()方法
    首先, 目标类是谁? Child, 目标属性是啥? $b, Child有没有? No, 父级呢? Yes
    Child可以访问Parent的$b属性么? private权限, 不能访问, 返回异常
    访问路径 Child->Parent->Parent

    ChildClass::getStD()
    访问源是谁? getStD, 属于谁? Parent, 目标类是谁? Child, 目标方法是啥? d(), Child有没有? No, Parent呢? Yes
    Parent可以访问Parent的d()么? 可以, 返回
    访问路径 Child->Parent->Parent

``` php
/**
 * 情况三: 方法属性被继承, 用子类类名访问, 属性关键字不变
 * getA: 访问到父级属性
 * getB: 访问到父级静态属性
 * getC: 访问异常
 * getSfD: 访问到父级静态方法
 * getStD: 访问异常
 */
class ChildClass extends ParentClass {
    private $a = 'ca';
    private static $b = 'csb';
    private function c() {
        echo 'csc', '<br>';
    }
    private static function d() {
        echo 'csd', '<br>';
    }
}
$chd = new ChildClass();
$chd->getA(); // output: pa
ChildClass::getSfB(); // output: pSelf: psb
ChildClass::getStB(); // output: Error
$chd->getC(); // output: pf: pc
ChildClass::getSfD(); // output: pSelf: psd
ChildClass::getStD(); // output: Error
```

    ChildClass::getStB()方法
    首先, 目标类是谁? Child, 目标属性是啥? $b, Child有没有? Yes
    Child可以访问Parent的$b属性么? private权限, 不能访问, 返回异常
    访问路径 Child->Parent->Child

    ChildClass::getStD()
    访问源是谁? getStD, 属于谁? Parent, 目标类是谁? Child, 目标方法是啥? d(), Child有没有? Yes
    Parent可以访问Child的d()么? 不能, 返回Error
    访问路径 Child->Parent->Child

``` php
/**
 * 情况四: 方法属性被继承, 访问修饰关键字改为protected(public的情况和这个情况一样), 用子类类名访问
 * getA: 访问到父级属性
 * getB: 访问到父级静态属性
 * getC: 访问到子级静态属性  !!!注意
 * getSfD: 访问到父级静态方法
 * getStD: 访问到子级静态方法  !!!注意
 */
class ChildClass extends ParentClass {
    protected $a = 'ca';
    protected static $b = 'csb';
    protected static $c = 'csc';
    protected static function d() {
        echo 'csd', '<br>';
    }
}
$chd = new ChildClass();
$chd->getA(); // output: pa
ChildClass::getSfB(); // output: pSelf: psb
ChildClass::getStB(); // output: pStatic: csb
$chd->getC(); // output: pf: pc
ChildClass::getSfD(); // output: pSelf: psd
ChildClass::getStD(); // output: pStatic: csd
```

    ChildClass::getStB()方法
    首先, 目标类是谁? Child, 目标属性是啥? $b, Child有没有? Yes
    Child可以访问Parent的$b属性么? private权限, 不能访问, 返回异常
    访问路径 Child->Parent->Child

    ChildClass::getStD()
    访问源是谁? getStD, 属于谁? Parent, 目标类是谁? Child, 目标方法是啥? d(), Child有没有? Yes
    Parent可以访问Child的d()么? 不能, 返回Error
    访问路径 Child->Parent->Child
