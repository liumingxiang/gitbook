# 函数

Less 所内置的函数。

# 杂项函数

### color

> 解析颜色，将代表颜色的字符串转换为颜色值.

参数： `string`: 代表颜色值的字符串。

返回值： `color`

案例： `color("#aaa");`

输出： `#aaa`

### convert

> 将数字从一种单位转换到另一种单位。

第一个参数为带单位的数值，第二个参数为单位。如果两个参数的单位是兼容的，则数字的单位被转换。如果两个参数的单位不兼容，则原样返回第一个参数。

参见 [unit](http://less.bootcss.com/functions/#misc-functions-unit) 不做转换的情况下改变单位

*兼容的单位是*:

- 长度： `m`, `cm`, `mm`, `in`, `pt` and `pc`,
- 时间： `s` and `ms`,
- 角度： `rad`, `deg`, `grad` and `turn`.

参数：

- `number`: 带单位浮点数。
- `identifier`, `string` or `escaped value`: units

返回值： `number`

案例：

```
convert(9s, "ms")
convert(14cm, mm)
convert(8, mm) // incompatible unit types

```

输出：

```
9000ms
140mm
8

```

### data-uri

> 将资源内联进样式表，如果开启了 ieCompat 选项并且资源太大，或者此函数的运行环境为浏览器，则会回退到直接使用 `url()` 。如果没有指定 MIME，则 node 将使用 mime 包来决定正确的 mime 类型。

参数：

- `mimetype`: (可选) MIME 字符串。
- `url`: 需要内嵌的文件的 URL 。

案例： `data-uri('../data/image.jpg');`

输出： `url('data:image/jpeg;base64,bm90IGFjdHVhbGx5IGEganBlZyBmaWxlCg==');`

浏览器端输出： `url('../data/image.jpg');`

案例： `data-uri('image/jpeg;base64', '../data/image.jpg');`

输出： `url('data:image/jpeg;base64,bm90IGFjdHVhbGx5IGEganBlZyBmaWxlCg==');`

### default

> Available only inside guard conditions and returns `true` only if no other mixin matches, `false`otherwise.

案例：

```
.mixin(1)                   {x: 11}
.mixin(2)                   {y: 22}
.mixin(@x) when (default()) {z: @x}

div {
  .mixin(3);
}

div.special {
  .mixin(1);
}

```

输出：

```
div {
  z: 3;
}
div.special {
  x: 11;
}

```

It is possible to use the value returned by `default` with guard operators. For example `.mixin() when not(default()) {}` will match only if there's at least one more mixin definition that matches`.mixin()` call:

```
.mixin(@value) when (ispixel(@value)) {width: @value}
.mixin(@value) when not(default())    {padding: (@value / 5)}

div-1 {
  .mixin(100px);
}

div-2 {
  /* ... */
  .mixin(100%);
}

```

输出：

```
div-1 {
  width: 100px;
  padding: 20px;
}
div-2 {
  /* ... */
}

```

It is allowed to make multiple `default()` calls in the same guard condition or in a different conditions of a mixins with the same name:

```
div {
  .m(@x) when (default()), not(default())    {always: @x}
  .m(@x) when (default()) and not(default()) {never:  @x}

  .m(1); // OK
}

```

However Less will throw a error if it detects a *potential* conflict between multiple mixin definitions using `default()`:

```
div {
  .m(@x) when (default())    {}
  .m(@x) when not(default()) {}

  .m(1); // Error
}

```

In above example it is impossible to determine what value each `default()` call should return since they recursively depend on each other.

Advanced multiple `default()` usage:

```
.x {
  .m(red)                                    {case-1: darkred}
  .m(blue)                                   {case-2: darkblue}
  .m(@x) when (iscolor(@x)) and (default())  {default-color: @x}
  .m('foo')                                  {case-1: I am 'foo'}
  .m('bar')                                  {case-2: I am 'bar'}
  .m(@x) when (isstring(@x)) and (default()) {default-string: and I am the default}

  &-blue  {.m(blue)}
  &-green {.m(green)}
  &-foo   {.m('foo')}
  &-baz   {.m('baz')}
}

```

输出：

```
.x-blue {
  case-2: #00008b;
}
.x-green {
  default-color: #008000;
}
.x-foo {
  case-1: I am 'foo';
}
.x-baz {
  default-string: and I am the default;
}

```

The `default` function is available as a Less built-in function *only inside guard expressions*. If used outside of a mixin guard condition it is interpreted as a regular CSS value:

案例：

```
div {
  foo: default();
  bar: default(42);
}

```

输出：

```
div {
  foo: default();
  bar: default(42);
}

```

### unit

> 删除或更换单位。

参数：

- `dimension`: 带单位或不带单位的数字。
- `unit`: (可选) 目标单位，如果省略此参数，则删除单位。

See [convert](http://less.bootcss.com/functions/#misc-functions-convert) for changing the unit without conversion.

案例： `unit(5, px)`

输出： `5px`

案例： `unit(5em)`

输出： `5`

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/misc-functions.md)

# 字符串函数

### escape

> 对字符串中的特殊字符做 [URL-encoding](http://en.wikipedia.org/wiki/Percent-encoding) 编码。

- 这些字符不会被编码：`,`, `/`, `?`, `@`, `&`, `+`, `'`, `~`, `!` and `$`。
- 被编码的字符是：`\`, `#`, `^`, `(`, `)`, `{`, `}`, `|`, `:`, `>`, `<`, `;`, `]`, `[` and `=`。

参数：`string`: 需要转义的字符串。

返回值：转义后不带引号的 `string` 字符串。

案例：

```
escape('a=1')

```

输出：

```
a%3D1

```

注意：如果参数不是字符串的话，函数行为是不可预知的。目前传入颜色值的话会返回 `undefined` ，其它的值会原样返回。写代码时不应该依赖这个特性，而且这个特性在未来有可能改变。

### e

> 用于对 CSS 的转义，已经由 `~"value"` 语法代替。

它接受一个字符串作为参数，并原样返回内容，不含引号。它可用于输出一些不合法的 CSS 语法，或者是使用 LESS 不能识别的属性。

参数：`string` - 需要转义的字符串。

返回值： `string` - 转义后的字符串，不含引号

案例：

```
filter: e("ms:alwaysHasItsOwnSyntax.For.Stuff()");

```

输出：

```
filter: ms:alwaysHasItsOwnSyntax.For.Stuff();

```

注意：也接受经 `~""` 转义的值或者是数字作为参数。任何其它的值将产生错误。

### % 格式化

> 此函数 `%(string, arguments ...)` 用于格式化字符串。

第一个参数是一个包含占位符的字符串。占位符以百分号 `%` 开头，后面跟着字母 `s`、`S`、`d`、`D`、`a` 或 `A`。后续的参数用于替换这些占位符。如果你需要输出百分号，可以多用一个百分号来转义 `%%`。

使用大写的占位符可以将特殊字符按照 UTF-8 编码进行转义。 此函数将会对所有的特殊字符进行转义，除了 `()'~!` 。空格会被转义为 `%20` 。小写的占位符将原样输出特殊字符，不进行转义。

占位符说明：

- `d`, `D`, `a`, `A` - 以被任意类型的参数替换 (颜色、数字、转义后的字符串、表达式等)。如果将它们和字符串一起使用，则整个字符串都会被使用，包括引号。然而，引号将会按原样放在字符串中，不会用 "/" 或类似的方式转义。
- `s`, `S` - 可以被除了颜色的之外的任何类型参数替换。如果你将它们和字符串一起使用，则只有字符串的值会被使用，引号会被忽略。

参数：

- `string`: 有占位符的格式化字符串。
- `anything`* : 用于替换占位符的值。

返回值： 格式化后的 `字符串（string）`.

案例：

```
format-a-d: %("repetitions: %a file: %d", 1 + 2, "directory/file.less");
format-a-d-upper: %('repetitions: %A file: %D', 1 + 2, "directory/file.less");
format-s: %("repetitions: %s file: %s", 1 + 2, "directory/file.less");
format-s-upper: %('repetitions: %S file: %S', 1 + 2, "directory/file.less");

```

输出：

```
format-a-d: "repetitions: 3 file: "directory/file.less"";
format-a-d-upper: "repetitions: 3 file: %22directory%2Ffile.less%22";
format-s: "repetitions: 3 file: directory/file.less";
format-s-upper: "repetitions: 3 file: directory%2Ffile.less";

```

### replace

> 用一个字符串替换一段文本。

添加于 [v1.7.0](https://github.com/less/less.js/blob/master/CHANGELOG.md) 版本

参数：

- `string`: 用于搜索、替换操作的字符串。
- `pattern`: 用于搜索匹配的字符串或正则表达式。
- `replacement`: 用于替换匹配项的字符串。
- `flags`: (可选) 正则表达式参数。

返回值： 被替换之后的字符串。

案例：

```
replace("Hello, Mars?", "Mars\?", "Earth!");
replace("One + one = 4", "one", "2", "gi");
replace('This is a string.', "(string)\.$", "new $1.");
replace(~"bar-1", '1', '2');

```

输出：

```
"Hello, Earth!";
"2 + 2 = 4";
'This is a new string.';
bar-2;

```

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/string-functions.md)

# 列表函数

### length

> 返回列表中元素的个数。

参数：`list` - 由逗号或空格分隔的元素列表。 返回值：一个代表元素个数的数字。

案例： `length(1px solid #0080ff);` 输出：`3`

案例：

```
@list: "banana", "tomato", "potato", "peach";
n: length(@list);

```

输出：

```
n: 4;

```

### extract

> 返回列表中指定位置的元素。

参数： `list` - 逗号或空格分隔的元素列表。 `index` - 指定列表中元素位置的数字。 返回值：列表中指定位置的元素。

案例： `extract(8px dotted red, 2);` 输出： `dotted`

案例：

```
@list: apple, pear, coconut, orange;
value: extract(@list, 3);

```

输出：

```
value: coconut;

```

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/list-functions.md)

# 数学函数

### ceil

> 向上取整。

参数： `number` - 浮点数。

返回值： `整数（integer）`

案例： `ceil(2.4)`

输出： `3`

### floor

> 向下取整。

参数： `number` - 浮点数

返回值： `整数（integer）`

案例： `floor(2.6)`

输出： `2`

### percentage

> 将浮点数转换为百分比字符串。

参数： `number` - 浮点数。

返回值： `字符串（string）`

案例： `percentage(0.5)`

输出： `50%`

### round

> 四舍五入取整。

参数：

- `number`: 浮点数
- `decimalPlaces`: 可选：四舍五入取整的小数点位置。默认值为0。

返回值： `数字（number）`

案例： `round(1.67)`

输出： `2`

案例： `round(1.67, 1)`

输出： `1.7`

### sqrt

> 计算一个数的平方根，并原样保持单位。

参数： `number` - 浮点数

返回值： 数字（number）

案例：

```
sqrt(25cm)

```

输出：

```
5cm

```

案例：

```
sqrt(18.6%)

```

输出：

```
4.312771730569565%;

```

### abs

> 计算数字的绝对值，并原样保持单位。

参数： `number` - 浮点数。

返回值： 数字（number）

Example #1: `abs(25cm)`

输出： `25cm`

Example #2: `abs(-18.6%)`

输出： `18.6%;`

### sin

> 正弦函数。

处理时会将没有单位的数字认为是弧度值。

参数： `number` - 浮点数。

返回值： 数字（number）

案例：

```
sin(1); // sine of 1 radian
sin(1deg); // sine of 1 degree
sin(1grad); // sine of 1 gradian

```

输出：

```
0.8414709848078965; // sine of 1 radian
0.01745240643728351; // sine of 1 degree
0.015707317311820675; // sine of 1 gradian

```

### asin

> 反正弦函数。返回以弧度为单位的角度，区间在 -PI/2 到 PI/2之间。

返回以弧度为单位的角度，区间在 `-π/2` 和 `π/2` 之间。

参数： `number` - 取值范围为 `[-1, 1]` 之间的浮点数。

返回值： 数字（number）

案例：

```
asin(-0.8414709848078965)
asin(0)
asin(2)

```

输出：

```
-1rad
0rad
NaNrad

```

### cos

> 余弦函数。

处理时会将没有单位的数字认为是弧度值。

参数： `number` - 浮点数。

返回值： 数字（number）

案例：

```
cos(1) // cosine of 1 radian
cos(1deg) // cosine of 1 degree
cos(1grad) // cosine of 1 gradian

```

输出：

```
0.5403023058681398 // cosine of 1 radian
0.9998476951563913 // cosine of 1 degree
0.9998766324816606 // cosine of 1 gradian

```

### acos

> 反余弦函数。，区间在 0 到 PI之间。

返回以弧度为单位的角度，区间在 0 到 π 之间。

参数： `number` - 取值范围为 [-1, 1] 之间的浮点数。

返回值： 数字（number）

案例：

```
acos(0.5403023058681398)
acos(1)
acos(2)

```

输出：

```
1rad
0rad
NaNrad

```

### tan

> 正切函数。

处理时会将没有单位的数字认为是弧度值。

参数： `number` - 浮点数。

返回值： 数字（number）

案例：

```
tan(1) // tangent of 1 radian
tan(1deg) // tangent of 1 degree
tan(1grad) // tangent of 1 gradian

```

输出：

```
1.5574077246549023   // tangent of 1 radian
0.017455064928217585 // tangent of 1 degree
0.015709255323664916 // tangent of 1 gradian

```

### atan

> 反正切函数。

返回以弧度为单位的角度，区间在 `-π/2` 到 `π/2` 之间。

参数： `number` - 浮点数。

返回值： 数字（number）

案例：

```
atan(-1.5574077246549023)
atan(0)
round(atan(22), 6) // arctangent of 22 rounded to 6 decimal places

```

输出：

```
-1rad
0rad
1.525373rad;

```

### pi

> 返回圆周率 π (pi);

参数： `none`

返回值： `number`

案例：

```
pi()

```

输出：

```
3.141592653589793

```

### pow

> 设第一个参数为A，第二个参数为B，返回A的B次方。

返回值与第一个参数有相同的单位，第二个参数的单位被忽略。

参数：

- `number`: base -浮点数。
- `number`: exponent - 浮点数。

返回值： 数字（number）

案例：

```
pow(0cm, 0px)
pow(25, -2)
pow(25, 0.5)
pow(-25, 0.5)
pow(-25%, -0.5)

```

输出：

```
1cm
0.0016
5
NaN
NaN%

```

### mod

> 返回第一个参数对第二参数取余的结果。

返回值与第一个参数单位相同，第二个参数单位被忽略。这个函数也可以处理负数和浮点数。

参数：

- `number`: 浮点数。
- `number`: 浮点数。

返回值： 数字（number）

案例：

```
mod(0cm, 0px)
mod(11cm, 6px);
mod(-26%, -5);

```

输出：

```
NaNcm;
5cm
-1%;

```

### min

> 返回一系列值中最小的那个。

参数： `value1, ..., valueN` - 一个或多个参与比较的值。

返回值： 最小值。

案例： `min(5, 10);`

输出： `5`

案例： `min(3px, 42px, 1px, 16px);`

输出： `1px`

### max

> 返回一系列值中最大的那个。

参数： `value1, ..., valueN` - 一个或多个参与比较的值。

返回值： 最大值。

案例： `max(5, 10);`

输出： `10`

案例： `max(3%, 42%, 1%, 16%);`

输出： `42%`

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/math-functions.md)

# 类型函数

### isnumber

> 如果待验证的值为数字则返回 `true` ，否则返回 `false` 。

参数：`value` - 待验证的值或变量。

返回值：如果待验证的值为数字则返回 `true` ，否则返回 `false` 。

案例：

```
isnumber(#ff0);     // false
isnumber(blue);     // false
isnumber("string"); // false
isnumber(1234);     // true
isnumber(56px);     // true
isnumber(7.8%);     // true
isnumber(keyword);  // false
isnumber(url(...)); // false

```

### isstring

> 如果待验证的值是字符串则返回 `true` ，否则返回 `false` 。

参数：`value` - 待验证的值或变量。

返回值：如果是字符串则返回 `true` ，否则返回 `false` 。

案例：

```
isstring(#ff0);     // false
isstring(blue);     // false
isstring("string"); // true
isstring(1234);     // false
isstring(56px);     // false
isstring(7.8%);     // false
isstring(keyword);  // false
isstring(url(...)); // false

```

### iscolor

> 如果待验证的值为颜色则返回 `true` ，否则返回 `false` 。

参数：`value` - 待验证的值或变量。

返回值：如果待验证的值为颜色则返回 `true` ，否则返回 `false` 。

案例：

```
iscolor(#ff0);     // true
iscolor(blue);     // true
iscolor("string"); // false
iscolor(1234);     // false
iscolor(56px);     // false
iscolor(7.8%);     // false
iscolor(keyword);  // false
iscolor(url(...)); // false

```

### iskeyword

> 如果待验证的值为关键字则返回 `true` ，否则返回 `false` 。

参数： `value` - 待验证的值或变量。

返回值：如果待验证的值为关键字则返回 `true` ，否则返回 `false` 。

案例：

```
iskeyword(#ff0);     // false
iskeyword(blue);     // false
iskeyword("string"); // false
iskeyword(1234);     // false
iskeyword(56px);     // false
iskeyword(7.8%);     // false
iskeyword(keyword);  // true
iskeyword(url(...)); // false

```

### isurl

> 如果待验证的值为 url 则返回 `true` ，否则返回 `false` 。

参数： `value` - 待验证的值或变量。

返回值：如果待验证的值为 url 则返回 `true` ，否则返回 `false` 。

案例：

```
isurl(#ff0);     // false
isurl(blue);     // false
isurl("string"); // false
isurl(1234);     // false
isurl(56px);     // false
isurl(7.8%);     // false
isurl(keyword);  // false
isurl(url(...)); // true

```

### ispixel

> 如果待验证的值为像素数则返回 `true` ，否则返回 `false` 。

参数： `value` - 待验证的值或变量。

返回值：如果待验证的值为像素数则返回 `true` ，否则返回 `false` 。

案例：

```
ispixel(#ff0);     // false
ispixel(blue);     // false
ispixel("string"); // false
ispixel(1234);     // false
ispixel(56px);     // true
ispixel(7.8%);     // false
ispixel(keyword);  // false
ispixel(url(...)); // false

```

### isem

> 如果待验证的值的单位是 em 则返回 `true` ，否则返回 `false` 。

参数： `value` - 待验证的值或变量。

返回值：如果待验证的值的单位是 em 则返回 `true` ，否则返回 `false` 。

案例：

```
isem(#ff0);     // false
isem(blue);     // false
isem("string"); // false
isem(1234);     // false
isem(56px);     // false
isem(7.8em);    // true
isem(keyword);  // false
isem(url(...)); // false

```

### ispercentage

> 如果待验证的值为百分比则返回 `true` ，否则返回 `false` 。

参数： `value` - 待验证的值或变量。

返回值：如果待验证的值为百分比则返回 `true` ，否则返回 `false` 。

案例：

```
ispercentage(#ff0);     // false
ispercentage(blue);     // false
ispercentage("string"); // false
ispercentage(1234);     // false
ispercentage(56px);     // false
ispercentage(7.8%);     // true
ispercentage(keyword);  // false
ispercentage(url(...)); // false

```

### isunit

> 如果待验证的值为指定单位的数字则返回 `true` ，否则返回 `false` 。

参数：

- `value` - 待验证的值或变量。
- `unit` - 单位标示符 (可加引号) 。

返回值：如果待验证的值为指定单位的数字则返回 `true` ，否则返回 `false` 。

案例：

```
isunit(11px, px);  // true
isunit(2.2%, px);  // false
isunit(33px, rem); // false
isunit(4rem, rem); // true
isunit(56px, "%"); // false
isunit(7.8%, '%'); // true
isunit(1234, em);  // false
isunit(#ff0, pt);  // false
isunit("mm", mm);  // false

```

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/type-functions.md)

# 颜色定义函数

### rgb

> 通过十进制红色、绿色、蓝色三种值 (RGB) 创建不透明的颜色对象。

在 HTML/CSS 中也会用文本颜色值 (literal color values) 定义颜色值，例如 `#ff0000`。

参数：

- `red`: 0-255 的整数或 0-100% 的百分比数。
- `green`: 0-255 的整数或 0-100% 的百分比数。
- `blue`: 0-255 的整数或 0-100% 的百分比数。

返回值： `color`

案例： `rgb(90, 129, 32)`

输出： `#5a8120`

### rgba

> 通过十进制红色、绿色、蓝色，以及 alpha 四种值 (RGBA) 创建带alpha透明的颜色对象。

参数：

- `red`: 0-255 的整数或 0-100% 的百分比数。
- `green`: 0-255 的整数或 0-100% 的百分比数。
- `blue`: 0-255 的整数或 0-100% 的百分比数。
- `alpha`: 0-1 的整数或 0-100% 的百分比数。

返回值： `color`

案例： `rgba(90, 129, 32, 0.5)`

输出： `rgba(90, 129, 32, 0.5)`

### argb

> 创建格式为 `#AARRGGBB` 的十六进制颜色值 (**注意不是** `#RRGGBBAA`！)。

这种格式被用于 IE 、.NET 和 Android 的开发中。

参数： `color`, 颜色对象。

返回值： `string`

案例： `argb(rgba(90, 23, 148, 0.5));`

输出： `#805a1794`

### hsl

> 通过色相 (hue)，饱和度 (saturation)，亮度 (lightness) 三种值 (HSL) 创建不透明的颜色对象。

参数：

- `hue`: 0-360 的整数，用于表示度数。
- `saturation`: 0-100% 的百分比数或 0-1 的整数。
- `lightness`: 0-100% 的百分比数或 0-1 的整数。

返回值： `color`

案例： `hsl(90, 100%, 50%)`

输出： `#80ff00`

当你想基于一种颜色的通道来创建另一种颜色时很方便，例如： `@new: hsl(hue(@old), 45%, 90%);`

`@new` 将拥有 `@old` 的 *hue*，以及它自身的饱和度与亮度。

### hsla

> 通过色相 (hue)，饱和度 (saturation)，亮度 (lightness)，以及 alpha 四种值 (HSLA) 创建透明的颜色对象。

参数：

- `hue`: 0-360 的整数，用于表示度数。
- `saturation`: 0-100% 的百分比数或 0-1 的整数。
- `lightness`: 0-100% 的百分比数或 0-1 的整数。
- `alpha`: 0-100% 的百分比数或 0-1 的整数。

返回值： `color`

案例： `hsl(90, 100%, 50%, 0.5)`

输出： `rgba(128, 255, 0, 0.5)`

### hsv

> 通过色相 (hue)、饱和度 (saturation)、色调 (value) 三种值 (HSV) 创建不透明的颜色对象。

注意，与 `hsl` 不同，这是另一种在 Photoshop 中可用的色彩空间。

参数：

- `hue`: 0-360 的整数，用于表示度数。
- `saturation`: 0-100% 的百分比数或 0-1 的整数。
- `value`: 0-100% 的百分比数或 0-1 的整数。

返回值： `color`

案例： `hsv(90, 100%, 50%)`

输出： `#408000`

### hsva

> 通过色相 (hue)，饱和度 (saturation)，色调 (value)，以及 alpha 四种值 (HSVA) 创建透明的颜色对象。

注意，与 `hsla` 不同，这是另一种在 Photoshop 中可用的色彩空间。

参数：

- `hue`: 0-360 的整数，用于表示度数。
- `saturation`: 0-100% 的百分比数或 0-1 的整数。
- `value`: 0-100% 的百分比数或 0-1 的整数。
- `alpha`: 0-100% 的百分比数或 0-1 的整数。

返回值： `color`

案例： `hsva(90, 100%, 50%, 0.5)`

输出： `rgba(64, 128, 0, 0.5)`

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/color-definition.md)

# 颜色通道函数

### hue

> 从颜色对象的 HSL 颜色空间中提取色色调值。

参数： `color` - 颜色对象。

返回值： `整数（integer）` `0-360`

案例： `hue(hsl(90, 100%, 50%))`

输出： `90`

### saturation

> 从颜色对象的 HSL 色彩空间中提取饱和度值。

参数： `color` - 颜色对象。

返回值： `百分比（percentage）` `0-100`

案例： `saturation(hsl(90, 100%, 50%))`

输出： `100%`

### lightness

> 从颜色对象的 HSL 色彩空间中提取亮度值。

参数： `color` - 颜色对象。

返回值： `百分比（percentage）` `0-100`

案例： `lightness(hsl(90, 100%, 50%))`

输出： `50%`

### hsvhue

> 在颜色对象的 HSV 色彩空间中提取色相值。

参数： `color` - 颜色对象。

返回值： `整数（integer）` `0-360`

案例： `hsvhue(hsv(90, 100%, 50%))`

输出： `90`

### hsvsaturation

> 在颜色对象的 HSV 色彩空间提取饱和度值。

参数： `color` - 颜色对象。

返回值： `百分比（percentage）` 0-100

案例： `hsvsaturation(hsv(90, 100%, 50%))`

输出： `100%`

### hsvvalue

> Extracts the value channel of a color object in the HSV color space.

参数： `color` - 颜色对象。

返回值： `百分比（percentage）` 0-100

案例： `hsvvalue(hsv(90, 100%, 50%))`

输出： `50%`

### red

> 从颜色对象中提取红色通道值。

参数： `color` - 颜色对象。

返回值： `整数（integer）` `0-255`

案例： `red(rgb(10, 20, 30))`

输出： `10`

### green

> 从颜色对象中提取绿色通道值。

参数： `color` - 颜色对象。

返回值： `整数（integer）` 0-255

案例： `green(rgb(10, 20, 30))`

输出： `20`

### blue

> 从颜色对象中提取蓝色通道值。

参数： `color` - 颜色对象。

返回值： `整数（integer）` 0-255

案例： `blue(rgb(10, 20, 30))`

输出： `30`

### alpha

> 从颜色对象中提取 alpha 通道值。

参数： `color` - 颜色对象。

返回值： `浮点数（float）` `0-1`

案例： `alpha(rgba(10, 20, 30, 0.5))`

输出： `0.5`

### luma

> 计算颜色对象的 [luma](http://en.wikipedia.org/wiki/Luma_%28video%29) (perceptual brightness) 值（亮度的百分比表示法）。

Uses **SMPTE C / Rec. 709** coefficients, as recommended in [WCAG 2.0](http://www.w3.org/TR/2008/REC-WCAG20-20081211/#relativeluminancedef). This calculation is also used in the contrast function.

参数： `color` - a颜色对象。

返回值： `百分比（percentage）` 0-100%

案例： `luma(rgb(100, 200, 30))`

输出： `65%`

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/color-channel.md)

# 颜色操作函数

Color operations generally take parameters in the same units as the values they are changing, and percentages are handled as absolutes, so increasing a 10% value by 10% results in 20%, not 11%, and values are clamped to their allowed ranges; they do not wrap around. Where return values are shown, we've used formats that make it clear what each function has done, in addition to the hex versions that you will usually be be working with.

### saturate

> Increase the saturation of a color in the HSL color space by an absolute amount.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `saturate(hsl(90, 80%, 50%), 20%)`

输出： `#80ff00 // hsl(90, 100%, 50%)`

![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABzElEQVRoQ+2avUoDURCFZw1RCBiinUUKwVewstA2rZUPYWOvIOgj2IsvYJs2jZ2PIFgIlhIsAipRmRtGhvXuj0L2noSzTTYs996T8+2ZmSLZ+fPWl/CCcSAjEBgWQQiBYPEgEDAeBEIgaA6A6WEPIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMTtKETF4+5fZ4LA+jt2DLzsGaHF71pLO5ImXPijz0a2J76TrbX+//c8a8+SUDYmaYSfqpcPQaXHZlePoa7tVA/8wb6s3x+w0uujI8m623vRR6GaQ6Z8wbhu6/FEAMRn+3Lfsn6798y8P3CfwL9KUGUlYy8m9r0Xd76y0FvX5Lxk/TUAKrSlYsUfl1TQDIn7FwCfHlzErT9GP2t4DHu/cAwpesspLne8jR9Ybc30x+yqSmKMW1sEBsEFDTtvdWg3etdhbtOXX6Q6ysEUiNpp5v+HX6QQxI0RBQNDQ0BSdZQsp6SNXYGxtX/TSle1sPMRhVo7XvOU2ZHzsnKZCUPxz1bAIBI0MgBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAm5xsmbq3oW2viLQAAAABJRU5ErkJggg==)➜![80ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABjUlEQVRoQ+2YMWpCQRCGfwk2aXOCgBexCrZpc4bkDJ5Bz2CbVtLkJIGcIG2KQDCMz4Xl8Z444Lgj+V6lKzO7ft/OzupkudNOPGkITBCSxsV+IQjJ5QMhyXwgBCHZCCRbDz0EIckIJFsOFYKQZASSLYcKQUgyAsmWQ4UgJBmBZMuhQhByHgLfX9Lrk/TxJs0epMeNdHsnjY2fZ9b4LE0rpIZnX3UM7NhnNr5YSduXDtRiLW2fD68P478/9p+29PnejZ86Rzz64RmaCSkybFm2u+2xHd8H2/9s6L1Vhj3HcnrnKDkvLQYhAxuiHH+XlmHzNRNS72jrA/VxUlfL2M4uMSWuHFf1+P28O65upv4qpEI4svabslmF0ENo6q6Lw787so71kP7viaH+ckoPsStuv7dw7W1xVbniOZv1kCtmFrp0hITi9SdHiJ9ZaARCQvH6kyPEzyw0AiGheP3JEeJnFhqBkFC8/uQI8TMLjUBIKF5/coT4mYVGICQUrz85QvzMQiMQEorXnxwhfmahEX9VhD3oZmqXEwAAAABJRU5ErkJggg==)

### desaturate

> Decrease the saturation of a color in the HSL color space by an absolute amount.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `desaturate(hsl(90, 80%, 50%), 20%)`

输出： `#80cc33 // hsl(90, 60%, 50%)`

![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABzElEQVRoQ+2avUoDURCFZw1RCBiinUUKwVewstA2rZUPYWOvIOgj2IsvYJs2jZ2PIFgIlhIsAipRmRtGhvXuj0L2noSzTTYs996T8+2ZmSLZ+fPWl/CCcSAjEBgWQQiBYPEgEDAeBEIgaA6A6WEPIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMTtKETF4+5fZ4LA+jt2DLzsGaHF71pLO5ImXPijz0a2J76TrbX+//c8a8+SUDYmaYSfqpcPQaXHZlePoa7tVA/8wb6s3x+w0uujI8m623vRR6GaQ6Z8wbhu6/FEAMRn+3Lfsn6798y8P3CfwL9KUGUlYy8m9r0Xd76y0FvX5Lxk/TUAKrSlYsUfl1TQDIn7FwCfHlzErT9GP2t4DHu/cAwpesspLne8jR9Ybc30x+yqSmKMW1sEBsEFDTtvdWg3etdhbtOXX6Q6ysEUiNpp5v+HX6QQxI0RBQNDQ0BSdZQsp6SNXYGxtX/TSle1sPMRhVo7XvOU2ZHzsnKZCUPxz1bAIBI0MgBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAm5xsmbq3oW2viLQAAAABJRU5ErkJggg==)➜![80cc33](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABh0lEQVRoQ+2aPQoCMRSE36I2FuIFthC8gZVHsPUsWgqCpZ7F1iPYeATBwguIhY2/ZCUQgptk19UMOqlkX2LG+XZeUphMN727cMA4kBAIDItMCIFg8SAQMB4EQiBoDoDp4RlCIGAOgMlhQggEzAEwOUwIgYA5ACaHCSEQMAfA5DAhBALmAJicqAk5HS6ynOxkuz5mtnT7LRnOOtJs18VVK+LhN/Yoosc3NxoQbZQSqCCooeCoMRinsprvs892TQPz/TBV/8YeITqKzPlpILYRNiCVRD1ctSKGvjs3GhDzDbZblpmWVwnR9bx1rudmW7Q12LV3zS2zPhqQsu3EbGeDUSqrxbO15T03WxwT4nhFqgASanZIW/r7llUFEF9Crueb+tuA1BrJxy4OZdqSa020luU6Q3zXXvMqm3dV1s/zzhvfHlUbHfp9UYGEivyneQQCRptACATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMzgMRjY/oftCnqAAAAABJRU5ErkJggg==)

### lighten

> Increase the lightness of a color in the HSL color space by an absolute amount.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `lighten(hsl(90, 80%, 50%), 20%)`

输出： `#b3f075 // hsl(90, 80%, 70%)`

![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABzElEQVRoQ+2avUoDURCFZw1RCBiinUUKwVewstA2rZUPYWOvIOgj2IsvYJs2jZ2PIFgIlhIsAipRmRtGhvXuj0L2noSzTTYs996T8+2ZmSLZ+fPWl/CCcSAjEBgWQQiBYPEgEDAeBEIgaA6A6WEPIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMTtKETF4+5fZ4LA+jt2DLzsGaHF71pLO5ImXPijz0a2J76TrbX+//c8a8+SUDYmaYSfqpcPQaXHZlePoa7tVA/8wb6s3x+w0uujI8m623vRR6GaQ6Z8wbhu6/FEAMRn+3Lfsn6798y8P3CfwL9KUGUlYy8m9r0Xd76y0FvX5Lxk/TUAKrSlYsUfl1TQDIn7FwCfHlzErT9GP2t4DHu/cAwpesspLne8jR9Ybc30x+yqSmKMW1sEBsEFDTtvdWg3etdhbtOXX6Q6ysEUiNpp5v+HX6QQxI0RBQNDQ0BSdZQsp6SNXYGxtX/TSle1sPMRhVo7XvOU2ZHzsnKZCUPxz1bAIBI0MgBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAm5xsmbq3oW2viLQAAAABJRU5ErkJggg==)➜![b3f075](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABpUlEQVRoQ+2YMW4CMRREPxEUNCnSI+UEFDkAzba5BlwggpwhSFwAjgHtNhwgRU4QcYAI0aQARGQnX3KsZbHBwqNktgKt1zue5/nf2sZ8PTwILxgHGgQCw8IKIRAsHgQCxoNACATNATA97CEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMDhNCIN8ObD4+ZdJfyGv5Lg/FvTzNHuX2rh1sz6XPB7/oygOzJkRNNWv2gbiGm/suNPe5wUsh0+dSdtu9+Xgtb8uVtVDHm98KXr019wbjQqaj0m4I9zpnc6RkBgnEB6WmKrjY/5q8XyB/gFRthpQGx84FCcRfxKWADJBTc8SUy1iTY8ZDANGy4ZeLqj5RVYK6vY4tV83WjS19foLqgLglK3e5MrohgBgh2gvq+sm5JSsGUO4S9m+BnCqLuUoYJBA9AaVKBBMS2MVCjrZ+fwk9xh47JrvH67r3By4h+bCsCUm+mj8wIYGAQSQQAgFzAEwOE0IgYA6AyWFCCATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACT8wXJpLPQTd69+QAAAABJRU5ErkJggg==)

### darken

> Decrease the lightness of a color in the HSL color space by an absolute amount.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `darken(hsl(90, 80%, 50%), 20%)`

输出： `#4d8a0f // hsl(90, 80%, 30%)`

![80e619](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABzElEQVRoQ+2avUoDURCFZw1RCBiinUUKwVewstA2rZUPYWOvIOgj2IsvYJs2jZ2PIFgIlhIsAipRmRtGhvXuj0L2noSzTTYs996T8+2ZmSLZ+fPWl/CCcSAjEBgWQQiBYPEgEDAeBEIgaA6A6WEPIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMTtKETF4+5fZ4LA+jt2DLzsGaHF71pLO5ImXPijz0a2J76TrbX+//c8a8+SUDYmaYSfqpcPQaXHZlePoa7tVA/8wb6s3x+w0uujI8m623vRR6GaQ6Z8wbhu6/FEAMRn+3Lfsn6798y8P3CfwL9KUGUlYy8m9r0Xd76y0FvX5Lxk/TUAKrSlYsUfl1TQDIn7FwCfHlzErT9GP2t4DHu/cAwpesspLne8jR9Ybc30x+yqSmKMW1sEBsEFDTtvdWg3etdhbtOXX6Q6ysEUiNpp5v+HX6QQxI0RBQNDQ0BSdZQsp6SNXYGxtX/TSle1sPMRhVo7XvOU2ZHzsnKZCUPxz1bAIBI0MgBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAm5xsmbq3oW2viLQAAAABJRU5ErkJggg==)➜![4d8a0f](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABnElEQVRoQ+2ZsUoDQRRFZxMJioWF2AuClZWdHyHW/pI/Yu1X2FlZCYK9pBQlmCi75sGwedmZTVbnCmerhH07uXvPu28GUl3eHHwFLhkHKoDIsGiEAESLB0DEeAAEIGoOiOlhDwGImANickgIQMQcEJNDQgAi5oCYHBICEDEHxOSQEID0c2D2tggPt+/h9ekzHJ3uhPPrvTDZH/VbJFH9F7+RK1giIWZILdozPL5/drUbHu8+GkD1lYIUm+3Ve2uv05Fr6jZ1xYHkdKfVLOY//6WNxlUDrr7q9OSAXFefaoZtzN3kWYAsR2LJVMTgigKx7jy+mISX+9lKp3vjpmtktdNmtWZ2O1H23cZfzgjcpOv7PFMMSGp2e/e7RlZsvn3OGXGMrGW7tLvfusg26bibcw1ud/zhyTi55wDEya9nCgnpM+gGrO06lnopsq6fPs9Xjr1D1KeO0QO+urtUsT3kt1/sv64PEDFyAAGImANickgIQMQcEJNDQgAi5oCYHBICEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJ+QZUR5YAPDOM7QAAAABJRU5ErkJggg==)

### fadein

> Decrease the transparency (or increase the opacity) of a color, making it more opaque.

Has no effect on opaque colors. To fade in the other direction use `fadeout`.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `fadein(hsla(90, 90%, 50%, 0.5), 10%)`

输出： `rgba(128, 242, 13, 0.6) // hsla(90, 90%, 50%, 0.6)`

### fadeout

> Increase the transparency (or decrease the opacity) of a color, making it less opaque. To fade in the other direction use `fadein`.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `fadeout(hsla(90, 90%, 50%, 0.5), 10%)`

输出： `rgba(128, 242, 13, 0.4) // hsla(90, 90%, 50%, 0.6)`

### fade

> Set the absolute transparency of a color. Can be applied to colors whether they already have an opacity value or not.

参数：

- `color`: 颜色对象。
- `amount`: 百分比 0-100%。

返回值： `color`

案例： `fade(hsl(90, 90%, 50%), 10%)`

输出： `rgba(128, 242, 13, 0.1) //hsla(90, 90%, 50%, 0.1)`

### spin

> Rotate the hue angle of a color in either direction.

While the angle range is 0-360, it applies a mod 360 operation, so you can pass in much larger (or negative) values and they will wrap around e.g. angles of 360 and 720 will produce the same result. Note that colors are passed through an RGB conversion, which doesn't retain hue value for greys (because hue has no meaning when there is no saturation), so make sure you apply functions in a way that preserves hue, for example don't do this:

```
@c: saturate(spin(#aaaaaa, 10), 10%);

```

Do this instead:

```
@c: spin(saturate(#aaaaaa, 10%), 10);

```

Colors are always returned as RGB values, so applying `spin` to a grey value will do nothing.

参数：

- `color`: 颜色对象。
- `angle`: A number of degrees to rotate (+ or -).

返回值： `color`

案例：

```
spin(hsl(10, 90%, 50%), 30)
spin(hsl(10, 90%, 50%), -30)

```

输出：

```
#f2a60d // hsl(40, 90%, 50%)
#f20d59 // hsl(340, 90%, 50%)

```

![f2330d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgklEQVRoQ+2XO04DQRBEewRIJoCA3AFn4AochSvAIeAKHIW7OHBO4MQWAtaaES21Gi9G+DOF9DayVmu7XG+qq10WNxeDcck4UAAiw6IJAYgWD4CI8QAIQNQcENNDhwBEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEC+O/D6PtjdbGkviw+7vTyx5+tzuzotR7Oq9/fHH9o9IW5GFfU4ndjDfNX0PU0ndj9fNUj1clD1tcPb5X4GHnUc+0DIAnEjsjkO4e1zsFKKnRVrKfrr/fq+bDpAvo7FJiPGgGQjx0z0+2MAATLSBnF2+yO5Q34Cljsnd4GPPQfgiYpAsoYeHSY/slzgtjGya0IioNhfdMhs2RhEI7bBqM8D5ACL6G86xDeifZX9phFGQsIJ99XW19i88sZ+2dc6nCFnDb3GVvf/IQcI3b/+SICI4QMIQMQcEJNDQgAi5oCYHBICEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATM4aM7qn4M8TltwAAAAASUVORK5CYII=)➜![f2a60d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABvElEQVRoQ+2WPU7DQBSE38ZChCINEi0lBQUtHRUn4AJp0lEDd4A6HU0aTkHDFZC4AC1SilAkigCjdXjR6rHetRURj9C4iWL5Zzzfzpt1s4dBKTxgHHAEAsOiEkIgWDwIBIwHgRAImgNgetghBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiC/HZi+lzIaz+Xx+VPOTwq5v9yT/YHbmlVdvz/80M4TomZ4UbfDvlxPFpW+u2FfriaLCpI/moCKGevvVdip54Q6tr0gYIGoEdYcNdX/1pkVA7v8KMU5JzvF6r7UcwjkZ1nEjGgLRK8/PSrk5mJ3veDaPIdARCQcMeqiHU0pYNo5Ot4OD3ry+va17iI9r8mKJcRqaDIa/7LcoDrEjqNcerRzdDQ9vaw2BfZ83cgKAYX9xQ4Zz6tFFxqRGiF2VZ8d96JdkesQAolkPZeCVGpySSCQlsO1rkPsltf2i93K+v+aEj+2wu1tbtvbpMdaftZGl3feIRup/4c3EwgYVAIhEDAHwOQwIQQC5gCYHCaEQMAcAJPDhBAImANgcpgQAgFzAEwOE0IgYA6AyWFCCATMATA532les9gyfOEWAAAAAElFTkSuQmCC)

![f2330d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgklEQVRoQ+2XO04DQRBEewRIJoCA3AFn4AochSvAIeAKHIW7OHBO4MQWAtaaES21Gi9G+DOF9DayVmu7XG+qq10WNxeDcck4UAAiw6IJAYgWD4CI8QAIQNQcENNDhwBEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEC+O/D6PtjdbGkviw+7vTyx5+tzuzotR7Oq9/fHH9o9IW5GFfU4ndjDfNX0PU0ndj9fNUj1clD1tcPb5X4GHnUc+0DIAnEjsjkO4e1zsFKKnRVrKfrr/fq+bDpAvo7FJiPGgGQjx0z0+2MAATLSBnF2+yO5Q34Cljsnd4GPPQfgiYpAsoYeHSY/slzgtjGya0IioNhfdMhs2RhEI7bBqM8D5ACL6G86xDeifZX9phFGQsIJ99XW19i88sZ+2dc6nCFnDb3GVvf/IQcI3b/+SICI4QMIQMQcEJNDQgAi5oCYHBICEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATM4aM7qn4M8TltwAAAAASUVORK5CYII=)➜![f20d59](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABwUlEQVRoQ+2ZMU4DMRBFx4kQS0FDg7hGOgqouAQ9VwiHgCtAzSVoqOhyAvqIhoKGQJQsssVIg2MHOY7kD/xtEllr79///Gccxb3un/fCC8YBRyAwLIIQAsHiQSBgPAiEQNAcANPDHkIgYA6AyWFCCATMATA5TAiBgDkAJocJIRAwB8DkMCEEsurAS/8uF2+Pcr+YytnwSG72juXA7W5k1TbX2khA5aTmCVED/XtcdSO5nE3CK113IxnPJgGSvxSU/67w7LgFqGt+9EtxTuRh8fzNppK1Kv0tng4FRJNhIfkxhaAG78ggpEjH/adNVQxE709B07m5tYodrZxAIF8wCUREbL3XjRX3EJsWLWO5XR2Xs9PB4UrJisuVLZXb6GGVAWn/f0hcnkrKit3VFpb2op9KnAV4153I7fwp+FlzqPizQFKgcr3FHgLiw0Gqt6RMX7cxak0umf8reoimphTIuoSkEtU6Hf75TYHkekh85LX9JXcc9tBS68V9xPYoxN8sTYGURPm/3EsgYKQJhEDAHACTw4QQCJgDYHKYEAIBcwBMDhNCIGAOgMlhQggEzAEwOUwIgYA5ACaHCSEQMAfA5HwCU5iq0JdZzGAAAAAASUVORK5CYII=)

### mix

> Mix two colors together in variable proportion. Opacity is included in the calculations.

参数：

- `color1`: 颜色对象。
- `color2`: 颜色对象。
- `weight`: Optional, a percentage balance point between the two colors, defaults to 50%.

返回值： `color`

案例：

```
mix(#ff0000, #0000ff, 50%)
mix(rgba(100,0,0,1.0), rgba(0,100,0,0.5), 50%)

```

输出：

```
#800080
rgba(75, 25, 0, 0.75)

```

![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)+![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)➜![800080](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABMUlEQVRoQ+2W0QnCMBRFXwX75ypZpBsITuASXcIJBDfoIlnFvwpVEgiUUqvSj3elpz+FpCU39+S+l6q19mk8Mg5UAJFhkYUARIsHQMR4AAQgag6I6aGHAETMATE5JAQgYg6IySEhABFzQEwOCQGImANickgIQMQcEJPjmpBwCtZcGqsPdbalv/fWnTuL12jv5tJ3c//8Or60RprzetyAFMPTxhOEYmh6x1u0cAx5bDy32+/y2PAYVo0vrVHW9IICkJkDUVLqkRI3IGmzn8rS2oSMkzAti+NEThO6SSCUrPn8uSUEIAD56uKw2aa+1EO49npcJ1hz1gG3HgIPsR4CEID8xRmgZIlhAghAxBwQk0NCACLmgJgcEgIQMQfE5JAQgIg5ICaHhABEzAExOSREDMgLbf+UEKEDvjgAAAAASUVORK5CYII=)

### greyscale

> Remove all saturation from a color in the HSL color space; the same as calling `desaturate(@color, 100%)`.

Because the saturation is not affected by hue, the resulting color mapping may be somewhat dull or muddy; [`luma`](http://less.bootcss.com/functions/#color-channel-luma) may provide a better result as it extracts perceptual rather than linear brightness, for example `greyscale('#0000ff')` will return the same value as `greyscale('#00ff00')`, though they appear quite different in brightness to the human eye.

参数： `color`: 颜色对象。

返回值： `color`

案例： `greyscale(hsl(90, 90%, 50%))`

输出： `#808080 // hsl(90, 0%, 50%)`

![80f20d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABnElEQVRoQ+2XMW7CQBBFZ4OQSCSa3AAp50iVJi1nyBWSOnW4AmdIS5OGe0TiBilSAIpkORqLkSYr46zXRv6I7wowO/7+z39mHV6/p6XwgHEgEAgMi0oIgWDxIBAwHgRCIGgOgOnhDCEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBAC6ceB7Vcp7087+fwo5O5hJPPltdzchqzifdbKEuAWDZoQb4Rq8samnNM1j28TWb3spfgpRUKQzbqobs9q6WcDF1/Dm2fX09+6wD1bILEBZlxl8mIiq+d9dW9qjj/X5rtBGo3r6/hEEcih5dSZTiBdc5a5/lhbakqEwdLZYUddq0tJmiakqTVm3lanZYPNkD5blrWeppr/tTqbRZwhNXOibcvyT7o3tA0gAjnhDMlJzMUD0Sc5ZWtrs+LYNlbrzO6v/mx5/WyJZ078zhJraNoadxoOiYsHmyGJ+i7ubwQChpxACATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMzi9F3a3omiSumwAAAABJRU5ErkJggg==)➜![808080](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABK0lEQVRoQ+2WQQqEMBAER7z5Dj/j1RflRV79jO/wJrskEAgS4y57mF4sL0KipNOVnkkXQngZj4wDHUBkWCQhANHiARAxHgABiJoDYnroIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkuCZk33dblsW2bUu2jONo8zzbMAx2NRe/q/3z7XhrjTjn9bgByYbHjUcI2dD4nqbJ1nVNY+XccRxprO/7n8Zba+Q1vaAApHIgcko9UuIGJG72riy10lOWuatElUk4l8UykeeEPhIIJaueP7eEAAQgH10cHtvUWz2Ea6/HdYI1qw649RB4iPUQgADkL84AJUsME0AAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IySEhYkDePqGoEBt9UbQAAAAASUVORK5CYII=)

Notice that the generated grey looks darker than the original green, even though its lightness value is the same.

Compare with using `luma` (usage is different because `luma` returns a single value, not a color):

```
@c: luma(hsl(90, 90%, 50%));
color: rgb(@c, @c, @c);

```

输出： `#cacaca`

![80f20d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABnElEQVRoQ+2XMW7CQBBFZ4OQSCSa3AAp50iVJi1nyBWSOnW4AmdIS5OGe0TiBilSAIpkORqLkSYr46zXRv6I7wowO/7+z39mHV6/p6XwgHEgEAgMi0oIgWDxIBAwHgRCIGgOgOnhDCEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBAC6ceB7Vcp7087+fwo5O5hJPPltdzchqzifdbKEuAWDZoQb4Rq8samnNM1j28TWb3spfgpRUKQzbqobs9q6WcDF1/Dm2fX09+6wD1bILEBZlxl8mIiq+d9dW9qjj/X5rtBGo3r6/hEEcih5dSZTiBdc5a5/lhbakqEwdLZYUddq0tJmiakqTVm3lanZYPNkD5blrWeppr/tTqbRZwhNXOibcvyT7o3tA0gAjnhDMlJzMUD0Sc5ZWtrs+LYNlbrzO6v/mx5/WyJZ078zhJraNoadxoOiYsHmyGJ+i7ubwQChpxACATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMzi9F3a3omiSumwAAAABJRU5ErkJggg==)➜![cacaca](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABLklEQVRoQ+2VQQ6DIAAE8QV+x4fp0ZNHfZjxN76gDTYk1FhaT0zS6akRKNsZF5pt2x7BD4ZAoxCMiyOIQlg+FALzoRCF0AjA8niHKARGABbHhigERgAWx4YoBEYAFseGKARGABbHhigERgAWx4YoBEYAFseGKOSdwL7vYRzHsK7rMdB1XZimKbRtG/KxT8/zNfF7+q1f5pf2qOWpakMS8Pjn+74Py7IcHKKQBPc8NgxDmOf5GM/X3H1e2iO9EDWkYIRcQbhqTw7+vObu/Ai81NC/FmJDXvqrNuT8hubn/tXbm+6L1JJ475Tuim/zS3vUOraqC6lxLJD3VAjMjkIUAiMAi2NDFAIjAItjQxQCIwCLY0MUAiMAi2NDFAIjAItjQxQCIwCLY0MUAiMAi/MEJi1ewDtU93sAAAAASUVORK5CYII=)

This time the grey's lightness looks about the same as the green, though its value is actually higher.

### contrast

> Choose which of two colors provides the greatest contrast with another.

This is useful for ensuring that a color is readable against a background, which is also useful for accessibility compliance. This function works the same way as the [contrast function in Compass for SASS](http://compass-style.org/reference/compass/utilities/color/contrast/). In accordance with [WCAG 2.0](http://www.w3.org/TR/2008/REC-WCAG20-20081211/#relativeluminancedef), colors are compared using their [luma](http://less.bootcss.com/functions/#color-channel-luma) value, not their lightness.

The light and dark parameters can be supplied in either order - the function will calculate their luma values and assign light and dark automatically, which means you can't use this function to select the *least* contrasting color by reversing the order.

参数：

- `color`: A color object to compare against.
- `dark`: optional - A designated dark color (defaults to black).
- `light`: optional - A designated light color (defaults to white).
- `threshold`: optional - A percentage 0-100% specifying where the transition from "dark" to "light" is (defaults to 43%, matching SASS). This is used to bias the contrast one way or another, for example to allow you to decide whether a 50% grey background should result in black or white text. You would generally set this lower for 'lighter' palettes, higher for 'darker' ones..

返回值： `color`

案例：

```
contrast(#aaaaaa)
contrast(#222222, #101010)
contrast(#222222, #101010, #dddddd)
contrast(hsl(90, 100%, 50%), #000000, #ffffff, 40%);
contrast(hsl(90, 100%, 50%), #000000, #ffffff, 60%);

```

输出：

```
#000000 // black
#ffffff // white
#dddddd
#000000 // black
#ffffff // white

```

These examples use the calculated colors for background and foreground; you can see that you never end up with white-on-white, nor black-on-black, though it's possible to use the threshold to permit lower-contrast outcomes, as in the last 案例：

![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABFUlEQVRoQ+2WsQ2DMBQFv0XNLOzEAAzDAAzANsxCi5TIRFZSEKQoxb/iaJBskB/vOHBZ1/URHpgGikAwLM4gAmHxEAiMh0AEQmsAlsd/iEBgDcDiaIhAYA3A4miIQGANwOJoiEBgDcDiaIhAYA3A4qQasu97zPMc27adtQzDENM0Rd/38W2uXnd1z6/jd2vUuawjDUgrvD54hdAKredxHGNZlnPsc+44jiilRNd1f43frdHWzIIikIsXolmaYYlABPJ67/xkXfunIRqiIXf/pjRDPj9bbnvfiFKBZOxi6GsKBEZIIAKBNQCLoyECgTUAi6MhAoE1AIujIQKBNQCLoyECgTUAi6MhAoE1AIujIQKBNQCL8wRLxrvAMcIf1wAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABQElEQVRoQ+2XQQ6CMBREy55TcCDCGbgI4SKcgXA29pqiTWpVYqKxb/FYtmqHecwfbLquuwQvjAONQDAsDiECYfEQCIyHQARCcwCmxw4RCMwBmBwTIhCYAzA5JkQgMAdgckyIQGAOwOSYEIE8OzAMQ5jnObRtG/Z9D9M0hXVdjw++2/vV+tkZNVhVT0gyNt78sixhHMfDhwglXhFUubdtW+j7/uv1szPyh+KfYFBAShNyWK9Sk8CVafp0PU9H+Z1/QsjPEsh9XAqk6If0lKQOSeMq9kq+l8bVt+v5uCp/q9a4ivdpQkzI43R+1xNn8/1X3WKHvGhKgTyaUnVk5f8l7JCbA1WB1Hq1JJ8rEBgdgQgE5gBMjgkRCMwBmBwTIhCYAzA5JkQgMAdgckyIQGAOwOSYEIHAHIDJMSECgTkAk3MF9fOXACIse2sAAAAASUVORK5CYII=)![dddddd](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABTklEQVRoQ+2XsQ2EMBAEj5guaIFmCGnEEY0Q0gzlEP/L6C3dG2EJ/cveYEiQfMCtdrw+0Q3D8DIuGQc6gMiwOIUARIsHQMR4AAQgag6I6WGGAETMATE5JAQgYg6IySEhABFzQEwOCQGImANickgIQK4OjONoIQTr+96O47BlWWzf9/PBu9q/1ks9WrCSSUgyOJrggXjD8trdO0/XSz1qQwHIx/HShqgJBSAA+d5vfhbEip8hd7X4XJo5/p2n63FOlfrXTEbq1TQh/phY19XmeT51xRmSzI13X9u2zaZpOuu/rJd65DOsJhiAhHCBC5Bst5OQmpnMeuVneGkmpFo6tuJ/S7ri7Hm67sHn32qVkqZHVsN9INsaIGJoAAIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCT8wbhXqMAqNP+BwAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABHElEQVRoQ+2ZsQnCQABFfxAbW1dwF7G1dQZ3cAdnsLUVd3EFWwtBImcIWsSAWNwrXprAXcL9/JeXBNLs2rRxwzTQCATD4hVEICweAoHxEIhAaA3A8vgOEQisAVgcDREIrAFYHA0RCKwBWBwNEQisAVgcDREIrAFYnKqG3K7JcZNczl0ri2WyPiSzefJtrhw3dM6v42NrlLlaWzUgfeHlwguEvtCyX+2T07Yb+5x73MsPg2Qy/W98bI1+zVpQBDJwQ/SW1rBEIALp7jsfWcP+aYiGaMjYu6maIZ+PLT9734iqAqnxFUNfUyAwQgIRCKwBWBwNEQisAVgcDREIrAFYHA0RCKwBWBwNEQisAVgcDREIrAFYHA0RCKwBWJwn1XI76DN5i5cAAAAASUVORK5CYII=)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABSklEQVRoQ+2XsQ3CMBRELw0dq6RnBjZAYgIqNmADKiZAYgNmoM8qdDRBDlhyDERIieIrXkob8OVe7l+oDq1acdk4UAHEhkUnBCBePABixgMgAHFzwEwPHQIQMwfM5JAQgJg5YCaHhADEzAEzOSQEIGYOmMkhIQD5dKDWVmudtNBSD9111U6Nzt0Hf+1NtT50RglWxRMSjQ03f9NRK+07HwKUcAVQ+V6ji2ptRq8PnZE+FHOCsQKSm5DC+paaCC5P07/raTry78wJIT0LIO9xCZCsH+JTEjskjqvQK+leHFdj19Nxlf9WqXEV7pOEkJD+dP7VE0PzfapuoUO+NCVA+qYUHVnpfwk65OVAUSClXi2dzwWIGR2AAMTMATM5JAQgZg6YySEhADFzwEwOCQGImQNmckgIQMwcMJNDQgBi5oCZHBICEDMHzOQ8Ae39TeiaGWTtAAAAAElFTkSuQmCC)

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/color-operations.md)

# 颜色混合函数

> 这些操作和图片编辑器（例如 Photoshop、Fireworks 或 GIMP）中的混合模式很*类似*（虽然不是完全一致），因此，你可以通过这些函数让 CSS 中的颜色与图片中的颜色相匹配。

### multiply

> 分别将两种颜色的红绿蓝 (RGB) 三种值做乘法运算，然后再除以 255，输出结果是更深的颜色。（译注：对应Photoshop中的“变暗/正片叠底”。）

参数：

- `color1`: 颜色对象。
- `color2`: 颜色对象。

返回值： `颜色（color）`

**案例**:

```
multiply(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)

```
multiply(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![331400](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABg0lEQVRoQ+2WsU4CQRRFH7Xa8SeUNJbEWhoSP8BQ0eg3QGNl/AASGqyNJY2lX6MmEEkkM/Elkwkru+yGvSaHBsLb2b1zz9x9r9Pr2o/xkXGgAxAZFlEIQLR4AESMB0AAouaAmB56CEDEHBCTQ0IAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5rSbkanhj97NHOzu/iLZ8fX7Y9G5sL8u5lamFNX69++rrvBa+j3lGW5xaA1JkXDDi9Xlhg+tR9CQY7qamtQAxBRhqKcRQK3Offc/IIZ8STmtA8k3mgEJKDp36PCEAaeDo5Camp7Ootg+e//f+trJe/zIqIyE1ANVJSPpKWzw92Oh2ApAaLOLSJoD4cOBaNut1/Lndfhf2InrIr1vHNvW8yf81ZfHKqhiTMqNtPhJXHWMdStXRuuJWGrtcZspqbEf//EYAEQMIEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAYiYA2JySAhAxBwQk0NCACLmgJicHbF3cSihasXIAAAAAElFTkSuQmCC)

```
multiply(#ff6600, #666666);
```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![662900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuklEQVRoQ+2WPUoEQRCFa1FhwdVjCGLupgYbyh5gQcxMjDyAIHgAIw8gC3sANRSMN/UKBoaCPzCyilIDBTVNzWCD2A/2bTR0z3a/eV+/qu4dbcu38AfjQI9AYFjUQggEiweBgPEgEAJBcwBMD3sIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2ByiidkOD6Qyeml9Nc3pHp/ldn5scyvp7VN0ZyO2/v6bP/JHdc9/Pp+Ldu/BKuiQMwQ/fC7qwsZHZ7UHigUM9jPrayu1eNfn4vGO7njusb8dibD/Um4nz8U/w2lGBCD8fz0KGfjncZ3t815gB4agfzBsTFzq7cX6Q82GyXL0hHNpeUsPe2WNC2Bi48qTBQTEgBM+4MvWVZOrK90lTMrL369h/sb2drdk7bkEEgHkKhndNX3dC5qwAaHQDJKWVs/6Dq9qcFR+YqSlgt9KZt6eq1Nr53RldSXMs9er75pmYtK2W/2KAlD9RW7ZWWEaaleJRAw3ARCIGAOgMlhQggEzAEwOUwIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2ByfgC9d5FoTcTdvQAAAABJRU5ErkJggg==)

```
multiply(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![993d00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABwElEQVRoQ+2XP0oDURCHZw0RCXgB+7SCvYKNHsFC8ARWehCtLKwFC1s7bc0FbFN5BkGU+I+3MDIO7y07cTM7kl+qJG/2zeT7dt5sqssd+iK8whCoICSMi7oQCInlA0KC+YAQCIlGIFg9mCEQEoxAsHLQIRASjECwctAhEBKMQLBy0CEQEoxAsHLQIRCyWALj/SPaPr2g4WidZi/P9HB2TNO7q8Um7XD3XjtEwku/SQIsraU4Bq6vYS58bfo8vb+m8d5hLahtjj4F9iZEQkt3MYOWENN7ubYyGBJVRJ/vs1/fcxyD5L2b4lmUzqH36vDmb7XVvxJSAg8hrVw3B8kOebw5p82Dk59zX969ek12jJ4T+pj7eHstdhQ6JONHAnya3NLG1m4dpaHrNX006WOOJeLI+kPn6JkiB2tpLTe8UwkQMoeI3JGVG7AMt2nYo0PmEJC7pOk/Q2nN8qiccvIcGayu4bG3I29LtU1vj71LRdnwYyHEAMsjFEI8KBtyQIgBlkcohHhQNuSAEAMsj1AI8aBsyAEhBlgeoRDiQdmQA0IMsDxCIcSDsiEHhBhgeYRCiAdlQw4IMcDyCIUQD8qGHN+9xJ+AK6uSXQAAAABJRU5ErkJggg==)

```
multiply(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![cc5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABoklEQVRoQ+2WMU7EMBAAHRAIChooKHkJ4gU0lEiUVFS8hIqKEuk+wAsQLZ+4kgIaChCnI8gWe/JZOUdb7LEWky72JmvNZLPbPZ+FPnC5IdAhxI2LdBCE+PKBEGc+EIIQbwScnYceghBnBJwdhwpBiDMCzo5DhSDEGQFnx6FCEOKMgLPjUCEIcUbA2XGaqpD9k4twdHkbNnb3ljB+f7yH16dJODg+X+zFtendVYrLn5H1t8f7UL4v3/srT00KibAi7Ag1XgJW1kVCt7mV9vv5bElOXBOB5TPlu9ctBiG/ckXivxdS+22Ue/3XZ/pgu+2dxYdb/mbyaqlVARUyUPs5vJeHm3B4ep2i8n9/vJe92i+ofGbsHiEjQvKeMNQXpF/kr6lVQ960hyQjxLBCysoZa/Y09cqokveJVf0gjrm1MbYceSVdbRxm7F33/NhovqbG3kYZq46NEBUu+2CE2DNWZUCICpd9MELsGasyIESFyz4YIfaMVRkQosJlH4wQe8aqDAhR4bIPRog9Y1UGhKhw2QcjxJ6xKgNCVLjsgxFiz1iV4QdntmvAsN6SrQAAAABJRU5ErkJggg==)

```
multiply(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
multiply(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)

```
multiply(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2WQWrCQBSGn5RANnoRD+AFeoqA225ceYPewJUnEHKKXsBLuHFbKSgFQaRlBl6RYGrGWuZXvmwCbybJP/+X/8307MW+jEvGgR5AZFhEIQDR4gEQMR4AAYiaA2J62EMAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IycmakPFobPNqbv2yH23Z7Xc2qSe2WC6s61jbM14P773mG7k4ZQPihoeFBwhuXLjXy9qqURVrv43N3mY2fZ7GeafPeL14KuLY4XhI+ob/FDmg3BUQN3j1vrLh6/DHL4e7/lifrV8C24QOkI4JcSCbz40NykFsdaE1eTq2++3ZOkA6ZP2aluVAyqKMIJqtqa1Oy/pnIE2D2/YK9pAOIHzKXxICkASjU6Z2PdqeHonbjrGp9UtH65R13HJutlPWLRfxSO8CiBhNgABEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkfANpCYIAKq8RkQAAAABJRU5ErkJggg==)

```
multiply(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)

### screen

> 与 `multiply` 函数效果相反，输出结果是更亮的颜色。（译注：对应Photoshop中的“变亮/滤色”。）

参数：

- `color1`: 颜色对象。
- `color2`: 颜色对象。

返回值： `颜色（color）`

案例：

```
screen(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
screen(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![ff8533](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABi0lEQVRoQ+2WQU4CQRBFC0N04VXYufAKxCN4EU/ggot4BMMV3HMVNsSIaTtFik4NMDJJ/5A3GzLTzeTPf/WrerZ/f9obl4wDM4DIsPgTAhAtHgAR4wEQgKg5IKaHGQIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMkhIQBJHFi8mC3fzO4fzXZbs/XKbPNZN55aEzNzCjn9E+KGl6/5+jB7fq3fVaCUq4CKa3fzej9/qL8F4GZttlhWoPHK1iLwCNvfFYthCodHvkMLSGtGhNUCivcO6ec7T1cG2CFmax2h3DaQtjpbwN4WY2t0QHFtZJVfs70vkLZlxLbh7Sq2oaEWlLWsodYkPqP6AjlXmf9tWXH2ZAeELAWn0nNNyY/8720AaQ0eMpeWdUF5jDGprXy/b4f6uedlnaGewJlqhsQTl88cnxXtLOLYe0FK2HJwoP8MAcaRAwARKwiAAETMATE5JAQgYg6IySEhABFzQEwOCQGImANickgIQMQcEJNDQgAi5oCYHBIiBuQXVF1CqDZhA7cAAAAASUVORK5CYII=)

```
screen(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![ffa366](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABr0lEQVRoQ+2WwW0CMRREP1khpY0c00DSQKqgAOpIL5HSANc0kDSQIw1EygUhJESIPtZIP8a7gLLgWTQ+Iez1juft/O/R9mW6NQ0aB0YCQsNiJ0RAuHgICBkPAREQNgfI9KiHCAiZA2RylBABIXOATI4SIiBkDpDJUUIEhMwBMjlKiIAUHLh7NHuYmI1vzdYrs49Xs/l7Wtg1R2ZmH3LqJwSG+2k+38zun9K5HIoPB1WaAzC4EMH5fxHsgIBzAYnJiOkAoBxCDqME0p9xIP8F3sfnf8Qe1wEkPygScdOkmcWX2ez57yqsWX7vzx1h3LmW1AWSl5lYalCuvK9goAzFua7ShHT4Gt8n9ijsUZprS+K5KIR96wI5VJZif0E56+o5+UUACWnGqafEHgVYgJT3r0pQhgckQkR62m5mAPKz2b8kxN6SXyDyXnaBZOAVwwNSSkib8QJy4qfURw/BKzfr9MvLU1cvaus58ZlK5col1E/IiQyvfbmAkBEWEAEhc4BMjhIiIGQOkMlRQgSEzAEyOUqIgJA5QCZHCREQMgfI5CghAkLmAJkcJURAyBwgk/MLp9NRUIPhNeoAAAAASUVORK5CYII=)

```
screen(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ffc299](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABqUlEQVRoQ+2XvUoDURCFZyF2AYs8hr2v4COkSJfOIuYJrAR7TSE26VLYpbVNmd7HsBCsVIjMwsAw+5eFC/cse7bce3c4e749M3eL0+H1JLxgHCgIBIZFKYRAsHgQCBgPAiEQNAfA9HCGEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIFUHfj8+pbFw1bejx9yc30lu/ulzC6n5ca2NTAvk8jJnhAzXN/meT2Xu6e38sUUil4Kqm7NgJkLHpzeM7BWQ2H7+/p80zOxdhKnzywCBcQnw6fDADUZ5aF6kD+/f1IUIheTSQXwZj2XVQP8qONML5NsI5CaNI4WSGwZba2mbxvS/T4F1g5tTrWtjRZIV1uKrcj3/a6ZY6Z66PvHW3nZHyozSiHFtVxzZHAtqw5SF9g4+OtmUlPdJIOhR5FBA7E2FId3PDJ3pSme7nKlQ3VmBZJihujx1uaBHW3tg4xrQ/jHyQqkR5JHs5VAwFATCIGAOQAmhwkhEDAHwOQwIQQC5gCYHCaEQMAcAJPDhBAImANgcpgQAgFzAEwOE0IgYA6AyfkHwWPQIIwo6asAAAAASUVORK5CYII=)

```
screen(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![ffe0cc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABjklEQVRoQ+2XMU7DQBREvwv3IK5CExpExSE4A4gilIggSigQnIFDUFKl4RBcAEFNiqC19KWvzYoQvLJfxGzpddbjeZk/SbN8e12aFsaBRkAwLDohAsLiISAwHgIiIDQHYHrUIQICcwAmRwkREJgDMDlKiIDAHIDJUUIEBOYATI4SIiCrDrx/fNrJ+aU9v8zt+HBiT/c3tre709340x7MyypyRk+IG57e5uFqamfXd92LJShpJVClPQfmLkRw6VoOtopbAxyCAhKTEdPhgHIIOYwSyPzMATzt9QgB6WVf/Q+PCiQfM3HU+LhKveLLx1DcS9ceZxd2Orstjro4+vysOM5oo25UIOvGUuwXHz2lzvlaLKwxs7ZtV7onwurTUfWzUD5xa4HE5Bwd7P8KyF87aigY6TlbCyQWfZ6k+OtMCdng61SjQ7x33Ph1PUH/nzN6Qjbg9y9uFRAYZgEREJgDMDlKiIDAHIDJUUIEBOYATI4SIiAwB2BylBABgTkAk6OECAjMAZgcJURAYA7A5HwDgDisyLDfsugAAAAASUVORK5CYII=)

```
screen(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)

```
screen(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
screen(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ffff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABa0lEQVRoQ+2WQWoDMQxFfxaFbnrBQk/QVU/SVU8Q6AWzKXQxxcEGxcyEVFTTH3jZBOyRRrxnWXNYFi3iZ0PggBAbF+dCEOLlAyFmPhCCEDcCZvUwQxBiRsCsHDoEIWYEzMqhQxBiRsCsHDoEIWYEzMqhQxCyRuBF0oekJ0knSa+Sjv3Brb3frpuR3yjHoEMG2Fbhu6S3XmqT0n5N1Lz3Ken5xvWH/txj/4/Co9S2PR+G/SWaCYmd0WBEWWtd0565th6FzoKj1HlvrmM/MQg5s0bIynwYp3BcG+N0t7kS98bJvmX9qwd+r0CnQzb6futa4sra76K8eBNCIg5mCDMknof5szN+ev7FDGnzaJ45fPb+01V4n681uLLuE1xV1QipIpvMi5AkuKowhFSRTeZFSBJcVRhCqsgm8yIkCa4qDCFVZJN5EZIEVxWGkCqyybwISYKrCkNIFdlkXoQkwVWFIaSKbDIvQpLgqsJ+ANCK57ExXpoRAAAAAElFTkSuQmCC)

```
screen(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAB30lEQVRoQ+2WPW4CMRBGZyMhpYlS0CRSChoukDJbQcMh6NPBDbgEnIFD0GyXnIMCpaNKg5CijWxpkLP/EzHxRPttubZnZ9+zZ5zkr3lOeMwQSCDEjAufCITY8gEhxnxACIRYI2AsH/QQCDFGwFg6OCEQYoyAsXRwQiDEGAFj6eCEQIgxAsbSwQmBkDKB4+lI82xOu8OOZk8z2k63NLwd+olNYxKWf/ENST51c6OfEAblEly/rGn5vvS5OinucaKKY5t0Q4u3RUlgHfTffCPcFNcA3TWGKSFFCCFIHquDy5LaxHb5Rld4GvP+lRA+MelDSqvn1YUHSyq+5wlVYruMaQBvixlVSFhiOFHuIQzf9RV+Jo8TSpKExvdj2n/uLyWLT8fobvTjfVj2wjhN3yj2sDaA1x6PKiRs2tw3uJlXjYWSHDjuOeevsxeVfWT+UtDUi1CyWraQpJyETT7c/SxkcDMoXQaKlwMIgRBRVYtasqQ9xJWj8Mrr/rSuH3TpE1V9qvc9RLR9ejA56gnpAV/xL0KIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ugQIkamuwBCdPmKo0OIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ujfkK7XsJ3U8fsAAAAASUVORK5CYII=)

### overlay

> 结合 `multiply` 与 `screen` 两个函数的效果，令浅的颜色变得更浅，深的颜色变得更深。（译注：对应 Photoshop 中的“叠加”。）注意：输出结果由第一个颜色参数决定。

参数：

- `color1`: 基准颜色对象。也就是用以确定最终结果是浅些还是深些的参考色。
- `color2`: 颜色对象。

返回值： `颜色（color）`

案例：

```
overlay(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)

```
overlay(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XTUoDQRCFX0CJIB7EC7jOAcQDCOIBXHkAVzmAqxwgBDyAuvdQIkQUImVTUGkmnZoYawryZhMyU9NV/b6unxmtzrECrzQKjAgkDYvfQAgkFw8CScaDQAgkmwLJ4mEPIZBkCiQLhxlCIMkUSBYOM4RAkimQLBxmCIEkUyBZOMwQAulQ4OoGeJgBp2fAxzswvQOeF8Ww9SyZmPsIZ/gMUcFlN/NH4Pa+7EugyCWg7LOj4/J/fFJ+FaDaClTPfQFuYdt39DDsQ+Gea+QCYjPDZkcXIAtMIX1/rYPcdF/We30CLq+74ddx9BT1L+YEsgn2QFkyLJC6ZLRKTV1SbKmzp13LnpSuz2U5rHXmMEMaSWyF3Vay6kavp7vuB28vwMUEYMnaoXr2BdKyV/dqQyD/DEQnKZsZdfO3k1qrcbOpd8Dq20O0J+jIq0vK6KsC198yrdGWY+8OGXRgrww7ZR2Y2J7tEohHpUAbAgkU2+OKQDwqBdoQSKDYHlcE4lEp0IZAAsX2uCIQj0qBNgQSKLbHFYF4VAq0IZBAsT2uCMSjUqANgQSK7XFFIB6VAm0IJFBsj6sfRCAuUPgoif8AAAAASUVORK5CYII=)

```
overlay(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![ff5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABg0lEQVRoQ+2WQUoDQRREK4hBFx5JcoKAuBRcunLlxmu4ysql4AU8gXgkF4oikbZp+PzMTDI4kIK83iXdM1Ndr6v/n60vtRbDxoEZQGxY/AkBiBcPgJjxAAhA3Bww00MNAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgHQ4sLiWblbS6Zn08S493kqvT3Xh0JyZmVPI2X9CmuFlNy8P0vKu7qtAKaOAanMX99L8ZHPfBeLbs3R+VaGW0cC2d+T/C/AIOz7TDsMUDo98hxeQmIyYji5AXSnK646Oqx0/35uAG8Bt7x5p6H+XAwQg4QzlK2PoqilzX5/14Xht9dWcsm4oBSSkJ8yxhoy9smKNyTVn22+ATAAkF9sIMxsci3bXtQSQCYD0JSIX79wyA2THXmNsDelrb3PL2z4/1A7T9u4I6cCX7b/tPXAAefsAMTsQAAGImQNmckgIQMwcMJNDQgBi5oCZHBICEDMHzOSQEICYOWAmh4QAxMwBMzkkxAzILw2ZLLhpDad2AAAAAElFTkSuQmCC)

```
overlay(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ff7a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2aQUoDQRREa0TRjWfRvXfwAIIncOXWS7jyBAEPkDu417O4UYIktMPH76RnQprIlPB6Od0zVNfr+r8D6dYPWoth40AHEBsW30IA4sUDIGY8AAIQNwfM9NBDAGLmgJkcEgIQMwfM5JAQgJg5YCaHhADEzAEzOSQEIBUHLm+l6yfp9Fz6fJeWd9Lrol84NWdm5iHkzJ+QMLzs5uVRurrv91WglFFA5bmjE6mTdHz2s/8hxOxMBlqe57VTc4dwt+EbXkByMnI6AlCkJjaaYQ7frb2fAb89Sxc3dfi1bzWY2/LK/wWyCwZAGs7DsGTkkhKnufSVGLVyk9Mz7DdTKSAhI8CmTvrYXO15rRdFv/labfckgPwxkFyiIlWrj/4CAJA9yhcJ+WXWvE29tYfk29LYb5bYZqQkrslce/dIC0v5k4PbGZi3ZLm5YaAHIAYQsgSAAMTMATM5JAQgZg6YySEhADFzwEwOCQGImQNmckgIQMwcMJNDQgBi5oCZnA0sVDb4BW76MgAAAABJRU5ErkJggg==)

```
overlay(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![ffa300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQQrCMBBFR6XgxkO49AIeRvAErjyJK08geBgv4NJDuBGKVmIJxJjUVgr9yOtKmjT+/tc/M6PqYJVxyTgwAogMi5cQgGjxAIgYD4AARM0BMT30EICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAUjCgfnabLk3K2Zm5dXstDG7HOqNTWtiZvYhZ/iEeMPd25x3Zott/V4OirscqNSaB+ZdCMG5ex6sP8PBDu+753PPxGf34XTLM7SAhMkI0+EB5YwKoYYgx0Vtw6P8BHw5ms1XafixjpZm9rHtP4DETnhAAOn4jcQlo6nU5NbCnhP3m6YUkJAMrLjchGUptdbUc+JBgIR0TMi3PpGD9a0ZU7J+ABFPR6nG3TYhuSSQkI5g+ugh/i/vt/rXZPo+3jL2doTC9jcHhh97AQIQ5W+AhIjRAQhAxBwQk0NCACLmgJgcEgIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMl5AiOBPWCy1fHcAAAAAElFTkSuQmCC)

```
overlay(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ffcc00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABjklEQVRoQ+2WMW7CUAxAHaSOLGXo2HshcYJOPUknTlCpp+klOnaAhQUJqak+0RfW7w8xDRhLeRn5Dnbek+3ftJ/SCk8YAg1Cwrg4FoKQWD4QEswHQhASjUCwetghCAlGIFg5dAhCghEIVg4dgpBgBIKVQ4cgJBiBYOXQIQipEHhciTyvRWZzkZ+dyNeLyPa9Czx3FgzmNcq5f4dk4Olrvt9Enl6770pS0pNE1c6ysCEKWmiK1cLPnQ39743OYwnRnaG7IwuySsiwtOxS8OZDZLGsyy/ruBH82t8ipNaNkxVSjgw9UvK4SnslP3nclGd9v7f77s328HcE0iE9fV+OFT2Wamd9O0cDzruoeUDIxeN2jJDabUzvG3bIxTpO19ra4qZD/gF0zCvX2CF919ihfZNGI9feMfam8e79r73T4Gz+SoSYUfkEIsSHszkLQsyofAIR4sPZnAUhZlQ+gQjx4WzOghAzKp9AhPhwNmdBiBmVTyBCfDibsyDEjMonECE+nM1ZEGJG5ROIEB/O5iy/jSA7yO80McwAAAAASUVORK5CYII=)

```
overlay(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)

```
overlay(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![ffcc00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABjklEQVRoQ+2WMW7CUAxAHaSOLGXo2HshcYJOPUknTlCpp+klOnaAhQUJqak+0RfW7w8xDRhLeRn5Dnbek+3ftJ/SCk8YAg1Cwrg4FoKQWD4QEswHQhASjUCwetghCAlGIFg5dAhCghEIVg4dgpBgBIKVQ4cgJBiBYOXQIQipEHhciTyvRWZzkZ+dyNeLyPa9Czx3FgzmNcq5f4dk4Olrvt9Enl6770pS0pNE1c6ysCEKWmiK1cLPnQ39743OYwnRnaG7IwuySsiwtOxS8OZDZLGsyy/ruBH82t8ipNaNkxVSjgw9UvK4SnslP3nclGd9v7f77s328HcE0iE9fV+OFT2Wamd9O0cDzruoeUDIxeN2jJDabUzvG3bIxTpO19ra4qZD/gF0zCvX2CF919ihfZNGI9feMfam8e79r73T4Gz+SoSYUfkEIsSHszkLQsyofAIR4sPZnAUhZlQ+gQjx4WzOghAzKp9AhPhwNmdBiBmVTyBCfDibsyDEjMonECE+nM1ZEGJG5ROIEB/O5iy/jSA7yO80McwAAAAASUVORK5CYII=)

```
overlay(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)

### softlight

> 与 `overlay` 函数效果相似，只是当纯黑色或纯白色作为参数时输出结果不会是纯黑色或纯白色。（译注：对应Photoshop中的“柔光”。）

参数：

- `color1`: 混合色（光源）。
- `color2`: 被混合的颜色。

返回值： `颜色（color）`

案例：

```
softlight(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XTUoDQRCFX0CJIB7EC7jOAcQDCOIBXHkAVzmAqxwgBDyAuvdQIkQUImVTUGkmnZoYawryZhMyU9NV/b6unxmtzrECrzQKjAgkDYvfQAgkFw8CScaDQAgkmwLJ4mEPIZBkCiQLhxlCIMkUSBYOM4RAkimQLBxmCIEkUyBZOMwQAulQ4OoGeJgBp2fAxzswvQOeF8Ww9SyZmPsIZ/gMUcFlN/NH4Pa+7EugyCWg7LOj4/J/fFJ+FaDaClTPfQFuYdt39DDsQ+Gea+QCYjPDZkcXIAtMIX1/rYPcdF/We30CLq+74ddx9BT1L+YEsgn2QFkyLJC6ZLRKTV1SbKmzp13LnpSuz2U5rHXmMEMaSWyF3Vay6kavp7vuB28vwMUEYMnaoXr2BdKyV/dqQyD/DEQnKZsZdfO3k1qrcbOpd8Dq20O0J+jIq0vK6KsC198yrdGWY+8OGXRgrww7ZR2Y2J7tEohHpUAbAgkU2+OKQDwqBdoQSKDYHlcE4lEp0IZAAsX2uCIQj0qBNgQSKLbHFYF4VAq0IZBAsT2uCMSjUqANgQSK7XFFIB6VAm0IJFBsj6sfRCAuUPgoif8AAAAASUVORK5CYII=)

```
softlight(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![ff4100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABlUlEQVRoQ+2WQUoDUQxA063Qu/QISvEGrgqewIV4kuKiJxC68gYieATvIritfEswhJkJMy2ZYN/s2sxMMu/9/PzF4UYOwlWGwAIhZVz8FoKQWj4QUswHQhBSjUCxepghCClGoFg5dAhCihEoVg4dgpBiBIqVQ4cgpBiBYuXQIQjpIHB7L/K4E7lainx/iTw/iLy9HG8cihWDeY5y5u8QBd6+5nUrcvd0/K4mpV1NVBRTgfZd/r8mu11WuJXtY+egO+EdtYTYzrDdoYKGukbl2S6zQr3g973IetMt39cxAezUR/6PkCbLdwhCRq4Lv2XYbcOueH1t2250ZX9+iKyu/1Y4QkbC77u9a9/Xe4dWvJ83CJlRiA5o2zl+RkS/mSE9Asd0iA71voHPDDmxS6bMELvy7VFWV7w/3vpZxLH3RGkX9vj8x94LAx59LkIiQslxhCQDj9IhJCKUHEdIMvAoHUIiQslxhCQDj9IhJCKUHEdIMvAoHUIiQslxhCQDj9IhJCKUHEdIMvAoHUIiQslxhCQDj9L9AJzzMhBo/VTcAAAAAElFTkSuQmCC)

```
softlight(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![ff5a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABhElEQVRoQ+2aQUrEQBBFf5ARXXgh8QKCJxgQ3Lty4zVcuRcEr+AFxAu5UFQYaUNB2ZNpMiGQL7xepivh93/5VVmk26y1EcvGgQ4gNix+hQDEiwdAzHgABCBuDpjpYYYAxMwBMzkkBCBmDpjJISEAMXPATA4JAYiZA2ZySAhABhw4u5Su7qXjE+n9TXq4ll4e+8LWnpmZc8hZPiFheDnN8510ftOfq0Apq4CKvYtb6fBo+9w1xFyRgZbruba1N4e7E57hBSQnI6djCFBdO3T4DLsG/Pokna6H4Y959gSzx9wCkKmwx7g7oWZZIHXLyC0l2lWZK7E+P6RO0iq1rV0tqFxvpYCE7Hhd6rYSw3xMy8ozJhscs+hg1QP8/tqeSQCZAUiG1QIWqYpEAWSP5klC/pj1v2ZInguRgtZnbDlqPXf47N0jLZTyk4PbO7Bsy3Jzw0APQAwgZAkAAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhADEzAEzOT88fSf4UtmH1QAAAABJRU5ErkJggg==)

```
softlight(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ff7200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2awU0DMRREZxEILjQDBVAENyQq4ASN5EQFSNwoggKgGU4IoSBnZfHztV52IdEO0vMtXsc7nufxd6R06zutRbNxoAOIDYuNEIB48QCIGQ+AAMTNATM91BCAmDlgJoeEAMTMATM5JAQgZg6YySEhADFzwEwOCQHIgAPn19LlvXR8Kr2/SU830stDP3DsmZmZu5CzfEKq4WU1zyvp4rZfV4FSWgEVnx0cSZ2kw5Pv9ReIr4/S2VUPtbQKts6R+wvwCDt+p26GXTg8cw4vIDEZMR0VUDaqGlohfX5sg2z1l/kqwCH4WcdMU/8y/P8CiclqmQuQmXsjHxljR00+UiKQoZrzUwpISANWy9ixI2sKjFyD8meA7BnIEKTcFy8JANkjkGh06/giIRPqyW9rSAaQr7b11WPXYa69EwAxhD85uO2B5X+HuDmysB6ALAwgvx4gADFzwEwOCQGImQNmckgIQMwcMJNDQgBi5oCZHBICEDMHzOSQEICYOWAm5wtE1TW4QXOxSAAAAABJRU5ErkJggg==)

```
softlight(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![ff8a00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABeElEQVRoQ+2WwW0CMRBFBwQRFxrZMy3QRKRUwCltcOFEBZFoIi1wppFcoiQKkbFGGhyzEQvRfsHzBYHt5fs//5kd7Je2N4aMAwOAyLA4CAGIFg+AiPEACEDUHBDTQw8BiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkkBCAVBxonszma7OHqdnHm9nrwmz3khe2zYmZeQ05/SfEDU+n2a7MZs/5XAlKGglUnBuO8/fRJH+WAEtXItByfdvcNdzt8AwtIDEZMR01QCWwcu9f+3cbs+axDr/2rA7mdtkCkFOwvWR2cfWCPf0CKUtGLClerlJf8ZHKk99s/z2WrLLftKWAhJy4NrGHXFKyosHei7zffH/+7kkA+Wcgsad4er7e858C5IwiSkKOzLrdHuLH9JTUXpN57T0jOXe6tN+E3KnpbccGiNilAAhAxBwQk0NCACLmgJgcEgIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMkhIWJAfgCg6zV4EzDrmwAAAABJRU5ErkJggg==)

```
softlight(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ffa100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABk0lEQVRoQ+2WQUoDUQxAU0ToxkPo0gt4F8ETuPIkrnoAEbyLF3DZHqIbQaTyHQLxE8pkHDPBvtm09Gcm+e81+bM6PMlBuMoQWCGkjIvvQhBSywdCivlACEKqEShWD2cIQooRKFYOHYKQYgSKlUOHIKQYgWLl0CEIKUagWDl0CEIcAld3IjcbkfMLkY+9yOu9yPZ5CDy2VgzmHOUs3yEKvO3m7VHk+mHYV5PSribKW1NhloJ9lkq1QlusFX5sbQ66E55RS4jtDNsdKsiToJv2OskK7QXvXkQub335fR0TwE695f8I8QQiJPi/6EeGHSkKs50reum4sWveCPJGHh0yUo439/sxZEfW2DOnF4CQPxJiR5N2T985dMhI+F4YHfKDyrKH+hxniG7n8334drYePr3zxv7e3th47f1FJ53Irct2yIlAjmwTIRFaCbEISYAcSYGQCK2EWIQkQI6kQEiEVkIsQhIgR1IgJEIrIRYhCZAjKRASoZUQi5AEyJEUCInQSohFSALkSAqERGglxCIkAXIkxRe7OTUQEXM/IQAAAABJRU5ErkJggg==)

```
softlight(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XTUoDQRCFX0CJIB7EC7jOAcQDCOIBXHkAVzmAqxwgBDyAuvdQIkQUImVTUGkmnZoYawryZhMyU9NV/b6unxmtzrECrzQKjAgkDYvfQAgkFw8CScaDQAgkmwLJ4mEPIZBkCiQLhxlCIMkUSBYOM4RAkimQLBxmCIEkUyBZOMwQAulQ4OoGeJgBp2fAxzswvQOeF8Ww9SyZmPsIZ/gMUcFlN/NH4Pa+7EugyCWg7LOj4/J/fFJ+FaDaClTPfQFuYdt39DDsQ+Gea+QCYjPDZkcXIAtMIX1/rYPcdF/We30CLq+74ddx9BT1L+YEsgn2QFkyLJC6ZLRKTV1SbKmzp13LnpSuz2U5rHXmMEMaSWyF3Vay6kavp7vuB28vwMUEYMnaoXr2BdKyV/dqQyD/DEQnKZsZdfO3k1qrcbOpd8Dq20O0J+jIq0vK6KsC198yrdGWY+8OGXRgrww7ZR2Y2J7tEohHpUAbAgkU2+OKQDwqBdoQSKDYHlcE4lEp0IZAAsX2uCIQj0qBNgQSKLbHFYF4VAq0IZBAsT2uCMSjUqANgQSK7XFFIB6VAm0IJFBsj6sfRCAuUPgoif8AAAAASUVORK5CYII=)

```
softlight(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![ffa100](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABk0lEQVRoQ+2WQUoDUQxAU0ToxkPo0gt4F8ETuPIkrnoAEbyLF3DZHqIbQaTyHQLxE8pkHDPBvtm09Gcm+e81+bM6PMlBuMoQWCGkjIvvQhBSywdCivlACEKqEShWD2cIQooRKFYOHYKQYgSKlUOHIKQYgWLl0CEIKUagWDl0CEIcAld3IjcbkfMLkY+9yOu9yPZ5CDy2VgzmHOUs3yEKvO3m7VHk+mHYV5PSribKW1NhloJ9lkq1QlusFX5sbQ66E55RS4jtDNsdKsiToJv2OskK7QXvXkQub335fR0TwE695f8I8QQiJPi/6EeGHSkKs50reum4sWveCPJGHh0yUo439/sxZEfW2DOnF4CQPxJiR5N2T985dMhI+F4YHfKDyrKH+hxniG7n8334drYePr3zxv7e3th47f1FJ53Irct2yIlAjmwTIRFaCbEISYAcSYGQCK2EWIQkQI6kQEiEVkIsQhIgR1IgJEIrIRYhCZAjKRASoZUQi5AEyJEUCInQSohFSALkSAqERGglxCIkAXIkxRe7OTUQEXM/IQAAAABJRU5ErkJggg==)

```
softlight(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XTUoDQRCFX0CJIB7EC7jOAcQDCOIBXHkAVzmAqxwgBDyAuvdQIkQUImVTUGkmnZoYawryZhMyU9NV/b6unxmtzrECrzQKjAgkDYvfQAgkFw8CScaDQAgkmwLJ4mEPIZBkCiQLhxlCIMkUSBYOM4RAkimQLBxmCIEkUyBZOMwQAulQ4OoGeJgBp2fAxzswvQOeF8Ww9SyZmPsIZ/gMUcFlN/NH4Pa+7EugyCWg7LOj4/J/fFJ+FaDaClTPfQFuYdt39DDsQ+Gea+QCYjPDZkcXIAtMIX1/rYPcdF/We30CLq+74ddx9BT1L+YEsgn2QFkyLJC6ZLRKTV1SbKmzp13LnpSuz2U5rHXmMEMaSWyF3Vay6kavp7vuB28vwMUEYMnaoXr2BdKyV/dqQyD/DEQnKZsZdfO3k1qrcbOpd8Dq20O0J+jIq0vK6KsC198yrdGWY+8OGXRgrww7ZR2Y2J7tEohHpUAbAgkU2+OKQDwqBdoQSKDYHlcE4lEp0IZAAsX2uCIQj0qBNgQSKLbHFYF4VAq0IZBAsT2uCMSjUqANgQSK7XFFIB6VAm0IJFBsj6sfRCAuUPgoif8AAAAASUVORK5CYII=)

### hardlight

> 与 `overlay` 函数效果相似，不过由第二个颜色参数决定输出颜色的亮度或黑度，而不是第一个颜色参数决定。（译注：对应Photoshop中的“强光/亮光/线性光/点光”。）

参数：

- `color1`: 混合色（光源）。
- `color2`: 基准色对象。它决定最终结果是亮些还是暗些。

返回值： `颜色（color）`

案例：

```
hardlight(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)

```
hardlight(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![662900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuklEQVRoQ+2WPUoEQRCFa1FhwdVjCGLupgYbyh5gQcxMjDyAIHgAIw8gC3sANRSMN/UKBoaCPzCyilIDBTVNzWCD2A/2bTR0z3a/eV+/qu4dbcu38AfjQI9AYFjUQggEiweBgPEgEAJBcwBMD3sIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2ByiidkOD6Qyeml9Nc3pHp/ldn5scyvp7VN0ZyO2/v6bP/JHdc9/Pp+Ldu/BKuiQMwQ/fC7qwsZHZ7UHigUM9jPrayu1eNfn4vGO7njusb8dibD/Um4nz8U/w2lGBCD8fz0KGfjncZ3t815gB4agfzBsTFzq7cX6Q82GyXL0hHNpeUsPe2WNC2Bi48qTBQTEgBM+4MvWVZOrK90lTMrL369h/sb2drdk7bkEEgHkKhndNX3dC5qwAaHQDJKWVs/6Dq9qcFR+YqSlgt9KZt6eq1Nr53RldSXMs9er75pmYtK2W/2KAlD9RW7ZWWEaaleJRAw3ARCIGAOgMlhQggEzAEwOUwIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2ByfgC9d5FoTcTdvQAAAABJRU5ErkJggg==)

```
hardlight(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![cc5200](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABoklEQVRoQ+2WMU7EMBAAHRAIChooKHkJ4gU0lEiUVFS8hIqKEuk+wAsQLZ+4kgIaChCnI8gWe/JZOUdb7LEWky72JmvNZLPbPZ+FPnC5IdAhxI2LdBCE+PKBEGc+EIIQbwScnYceghBnBJwdhwpBiDMCzo5DhSDEGQFnx6FCEOKMgLPjUCEIcUbA2XGaqpD9k4twdHkbNnb3ljB+f7yH16dJODg+X+zFtendVYrLn5H1t8f7UL4v3/srT00KibAi7Ag1XgJW1kVCt7mV9vv5bElOXBOB5TPlu9ctBiG/ckXivxdS+22Ue/3XZ/pgu+2dxYdb/mbyaqlVARUyUPs5vJeHm3B4ep2i8n9/vJe92i+ofGbsHiEjQvKeMNQXpF/kr6lVQ960hyQjxLBCysoZa/Y09cqokveJVf0gjrm1MbYceSVdbRxm7F33/NhovqbG3kYZq46NEBUu+2CE2DNWZUCICpd9MELsGasyIESFyz4YIfaMVRkQosJlH4wQe8aqDAhR4bIPRog9Y1UGhKhw2QcjxJ6xKgNCVLjsgxFiz1iV4QdntmvAsN6SrQAAAABJRU5ErkJggg==)

```
hardlight(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![ff8533](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABi0lEQVRoQ+2WQU4CQRBFC0N04VXYufAKxCN4EU/ggot4BMMV3HMVNsSIaTtFik4NMDJJ/5A3GzLTzeTPf/WrerZ/f9obl4wDM4DIsPgTAhAtHgAR4wEQgKg5IKaHGQIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMkhIQBJHFi8mC3fzO4fzXZbs/XKbPNZN55aEzNzCjn9E+KGl6/5+jB7fq3fVaCUq4CKa3fzej9/qL8F4GZttlhWoPHK1iLwCNvfFYthCodHvkMLSGtGhNUCivcO6ec7T1cG2CFmax2h3DaQtjpbwN4WY2t0QHFtZJVfs70vkLZlxLbh7Sq2oaEWlLWsodYkPqP6AjlXmf9tWXH2ZAeELAWn0nNNyY/8720AaQ0eMpeWdUF5jDGprXy/b4f6uedlnaGewJlqhsQTl88cnxXtLOLYe0FK2HJwoP8MAcaRAwARKwiAAETMATE5JAQgYg6IySEhABFzQEwOCQGImANickgIQMQcEJNDQgAi5oCYHBIiBuQXVF1CqDZhA7cAAAAASUVORK5CYII=)

```
hardlight(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![ff2900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XTUoDQRCFX0CJIB7EC7jOAcQDCOIBXHkAVzmAqxwgBDyAuvdQIkQUImVTUGkmnZoYawryZhMyU9NV/b6unxmtzrECrzQKjAgkDYvfQAgkFw8CScaDQAgkmwLJ4mEPIZBkCiQLhxlCIMkUSBYOM4RAkimQLBxmCIEkUyBZOMwQAulQ4OoGeJgBp2fAxzswvQOeF8Ww9SyZmPsIZ/gMUcFlN/NH4Pa+7EugyCWg7LOj4/J/fFJ+FaDaClTPfQFuYdt39DDsQ+Gea+QCYjPDZkcXIAtMIX1/rYPcdF/We30CLq+74ddx9BT1L+YEsgn2QFkyLJC6ZLRKTV1SbKmzp13LnpSuz2U5rHXmMEMaSWyF3Vay6kavp7vuB28vwMUEYMnaoXr2BdKyV/dqQyD/DEQnKZsZdfO3k1qrcbOpd8Dq20O0J+jIq0vK6KsC198yrdGWY+8OGXRgrww7ZR2Y2J7tEohHpUAbAgkU2+OKQDwqBdoQSKDYHlcE4lEp0IZAAsX2uCIQj0qBNgQSKLbHFYF4VAq0IZBAsT2uCMSjUqANgQSK7XFFIB6VAm0IJFBsj6sfRCAuUPgoif8AAAAASUVORK5CYII=)

```
hardlight(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)

```
hardlight(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)

```
hardlight(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)

```
hardlight(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)

### difference

> 从第一个颜色值中减去第二个（分别计算 RGB 三种颜色通道），输出结果是更深的颜色。如果结果为负值则被反转。如果减去的颜色是黑色则不做改变；减去白色将得到颜色反转。（译注：对应Photoshop中的“差值/排除”。）

参数：

- `color1`: 被减的颜色对象。
- `color2`: 减去的颜色对象。

返回值： `颜色（color）`

案例：

```
difference(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
difference(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![cc3333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfklEQVRoQ+2WsQ3CMBBFHURHBwUlQ7AEYgQWYQYWyQiIIViCkgI6OgTIlixZFgnfFN9n6acC5+I7vZc7pzuv12+nywyBTkLMuAiFSIgtHxJizIeESIg1Asbq0RkiIcYIGCtHHSIhxggYK0cdIiHGCBgrRx0iIcYIGCtHHSIhxggYK6e5Dplvt26137vJbBZQvh4Pdzkcwu+S9fvx6Ib28vdqXU0JiQA9rFRCN50Gfu/nE1r3sbfTyS02m/Bculf8X0tKU0LytzYXFCGWrvt9h55hd0p1IWNjAxlPcWTlIwhZT0X4EZg+wxYR81UVkr6V1753y93u6wjJ7/3bCWNdoA75MSYQQKWjSUJ+9H1ph5Qe3kPxOtRHxKTnRD7Dv90r/bwditdnb61TsrG8VQ/1xlhRypUQCmY8iYTgrCiREkLBjCeREJwVJVJCKJjxJBKCs6JESggFM55EQnBWlEgJoWDGk0gIzooSKSEUzHgSCcFZUSIlhIIZTyIhOCtK5Af4/HrgRx8sZAAAAABJRU5ErkJggg==)

```
difference(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![990066](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAByUlEQVRoQ+2WsUoDURBFJ4aVbfQD7GMj2KaxsPArArY2Vv6Bf2BlYS2ksbS1tEmbNiCIrSJEhcUlKvNg4DnraiKEuejdJvBms+/uPXtnXudMDt6FF4wDHQKBYZGEEAgWDwIB40EgBILmAJgezhACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOSEJqS335ed04EUa2WypX6q5PpwKJPzkbTV9L6v/tO27p+V76H/yffxtQhWYUDMCH1phWCG6u9kOJLeoJ/W8tpK0U1rb/VsrnX/rPHJlWwf7TWeqwu+piAjrj8NxABOb+7lYuv4k7/2QTzfPTZqESBsTwgg9nVq69K2kSckr82qeqGEGJDq4UVW18vUGq0tWSJfp1WjFpUO1RQGxPfv28uxbOxuNtqJmmi1RVuW3d8tiwQib0sG3SD9+5bl24KfKflXarXfAvEz57s5ZXMrKiVhCckBzDtslzXs/aHCTnoRsyQMyE9Hzrbj6LKOw/7YHQEjfIZEvTTyvqEJQTYmShuBRDnfsi+BEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIWBAPgCJCJzoERp2cQAAAABJRU5ErkJggg==)

```
difference(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![663399](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABvklEQVRoQ+2WrU4DQRSFb/lLk74BvkhMRQ0SQXgARB+AV8BieQReoAZPEKgGU1tbhURhaNLwn9nkJneHu8uO6T1JT9Vm7nTm7Pn23Jne5ej2R/iDcaBHIDAsKiEEgsWDQMB4EAiBoDkApodnCIGAOQAmhwkhEDAHwOQwIQQC5gCYHCaEQMAcAJPDhBAImANgcsITMj4fyuTqRPqDfVmvPmR68yTz+2Vlk1dL4zo/Pet/SsfTHnZ9u5buH8EqFIgakl78cbqQ08lx5UGCogbb2u7eTjX+9fldm1M6ntaYPyxlfDZ097MfxaahhAFRGK8vb3J9cVd777aanWiBeslqAkUgzmemZq5X79IfHNRalqbDq+Wtxra5vMXZFGgCtTW21bY6IWqQbVlqllfzkqBtLj97bBLSWovZsxyNDv+0Ka8WdY6EtyzvzOja3/9rWW2g8hQ0rbV1Z0gJkNLD287vcmnIAW4aRtovLCH5tTa/djZdSUuvt3Z+l2t1VKtS+KFAIr5A9D0JBIwQgRAImANgcpgQAgFzAEwOE0IgYA6AyWFCCATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmJxf4Y+w4N1v108AAAAASUVORK5CYII=)

```
difference(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![3366cc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABpklEQVRoQ+2WPW7CQBSEnwNRkOigoMwhKLhACsQBUnAArpA2LRfhCFEKLoCUNgegSIHET4VEohCiXelJy8M2Tpo3lmYr660sj+bzzG7Wn7ydhAvGgYxAYFhEIQSCxYNAwHgQCIGgOQCmh2cIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2ByXBMyGnTkaXwv7dZNtGV/+JHpbCkvi61U3St6J52DeV4qxw2IGh7UBQhhBThhvS42Mhx043PZ3my+kvFD7+KddK6A6wLFDYg1yAIKKdGle81GFkcf6095fH6/2F/tvs7mdYGQ6nQHklaTrRm7p8nZH47SbjVi1YV3iuZ/rT9bmx5A3YHYFGhNlSXk7jaLILSaNDl2buvPVllakyg1V0sg38fT2dmiQOw87zzKA533E3ikI3zTDch/DvUi46sCYUKu/GZVr7Zpt2vN2Kty0dxeoa+dU963MreEeFUC+ncJBIwQgRAImANgcpgQAgFzAEwOE0IgYA6AyWFCCATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmJxfgFaC2DBCnnIAAAAASUVORK5CYII=)

```
difference(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![0099ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABq0lEQVRoQ+2WMU4DQQxFf5BScgH6tJyAgnMgcQIqTkJFkRqJC3AFTpA2FWegQYogyGwsjMmskyB5jPJTjmfX3vdiz0wwX6/BXxkCEwop4+KrEAqp5YNCivmgEAqpRqBYPTxDKKQYgWLlsEMopBiBYuWwQyikGIFi5bBDKKQYgWLlsEMopA+B6xlwfwGcToHXFXDzDDwsh1rGYtnVdu0QC0I+3IJqxWSfgrXPtNYFur5L9twtgNvzAbNI0ee2xVRYppRuQiwkD+ZxCVzNfkObngxrq4+fMFvr26DbzrDdoXt7SLDCKWQzyijEgbCjY5cO0dEjZ8Lb+3fn2HUZgfou2ac/HY1+zPmxmTmqNNe/6hAp2gJ+egEuz4ZPaa37cciR1fibHXKG+LHi3+FvTTxD9ujxQ4TYw9veluyI87cof8tih4xI+uu1t3VNHrs+23OCZ8geHXSsW7sd6scKPPpuCokIJccpJBl4lI5CIkLJcQpJBh6lo5CIUHKcQpKBR+koJCKUHKeQZOBROgqJCCXHKSQZeJSOQiJCyXEKSQYepaOQiFBynEKSgUfpPgGf+knQsuuhLwAAAABJRU5ErkJggg==)

```
difference(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2WQWrCQBSGn5RANnoRD+AFeoqA225ceYPewJUnEHKKXsBLuHFbKSgFQaRlBl6RYGrGWuZXvmwCbybJP/+X/8307MW+jEvGgR5AZFhEIQDR4gEQMR4AAYiaA2J62EMAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IycmakPFobPNqbv2yH23Z7Xc2qSe2WC6s61jbM14P773mG7k4ZQPihoeFBwhuXLjXy9qqURVrv43N3mY2fZ7GeafPeL14KuLY4XhI+ob/FDmg3BUQN3j1vrLh6/DHL4e7/lifrV8C24QOkI4JcSCbz40NykFsdaE1eTq2++3ZOkA6ZP2aluVAyqKMIJqtqa1Oy/pnIE2D2/YK9pAOIHzKXxICkASjU6Z2PdqeHonbjrGp9UtH65R13HJutlPWLRfxSO8CiBhNgABEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkfANpCYIAKq8RkQAAAABJRU5ErkJggg==)

```
difference(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![ff9900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABlklEQVRoQ+2XMU7DQBBFJ6BINFwgfdqcIAXnQOIEVJyEKkVqpFyAK3AC2lScgQYpgqBhNdLE8hqbBPlLeW6s7K43f//zn11P9mvbG5eMAxOAyLD4EQIQLR4AEeMBEICoOSCmhz0EIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IA0uLA/M5suTKbXpvt3s1e7s22T2VgV5+YmaeQM35CwnBfzeuj2eKhrMuh+OWgct/FtPy+vCr3ABhjHWqfdgeeYedn4mU4hcMD59ACkpOR09EGKAMLSF+7Q5C1dp9vuzGb37bDb+oYaOoxwwFSgz1SSsYF0iwZXaXG+z4/ysvnSYjy5iWqT3uzBJKQSpDzHvJbyWrW/bdns9lNmTgMdkDRTsn6Q/UcCiT/Re3ZaAfIPwOJk5Tf84ksl59aOyWrB5yhe4gfcXNpyt8ste+VrqMtx94ekM58yLinrDM3v235ABF7KQACEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAYiYA2JySIgYkG930EXQvPoyNwAAAABJRU5ErkJggg==)

```
difference(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAB30lEQVRoQ+2WPW4CMRBGZyMhpYlS0CRSChoukDJbQcMh6NPBDbgEnIFD0GyXnIMCpaNKg5CijWxpkLP/EzHxRPttubZnZ9+zZ5zkr3lOeMwQSCDEjAufCITY8gEhxnxACIRYI2AsH/QQCDFGwFg6OCEQYoyAsXRwQiDEGAFj6eCEQIgxAsbSwQmBkDKB4+lI82xOu8OOZk8z2k63NLwd+olNYxKWf/ENST51c6OfEAblEly/rGn5vvS5OinucaKKY5t0Q4u3RUlgHfTffCPcFNcA3TWGKSFFCCFIHquDy5LaxHb5Rld4GvP+lRA+MelDSqvn1YUHSyq+5wlVYruMaQBvixlVSFhiOFHuIQzf9RV+Jo8TSpKExvdj2n/uLyWLT8fobvTjfVj2wjhN3yj2sDaA1x6PKiRs2tw3uJlXjYWSHDjuOeevsxeVfWT+UtDUi1CyWraQpJyETT7c/SxkcDMoXQaKlwMIgRBRVYtasqQ9xJWj8Mrr/rSuH3TpE1V9qvc9RLR9ejA56gnpAV/xL0KIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ugQIkamuwBCdPmKo0OIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ujfkK7XsJ3U8fsAAAAASUVORK5CYII=)

### exclusion

> 效果与 difference() 函数效果相似，只是输出结果对比度更小 (lower contrast)。（译注：对应Photoshop中的“差值/排除”。）

参数：

- `color1`: 被减的颜色对象。
- `color2`: 减去的颜色对象。

返回值： `颜色（color）`

案例：

```
exclusion(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
exclusion(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![cc7033](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABbklEQVRoQ+2WMW7CQBBF1xYSRbpQUHIIc4CUKDUVp0jHGbgIFTWizAGcQ6SkSLoUkZCJ1tLCsMLGsUT8rTxXMLMLX//tn3WSv2RHxyPjQAIQGRalEIBo8QCIGA+AAETNATE93CEAEXNATA4JAYiYA2JySAhAxBwQk0NCACLmgJgcEgIQMQfE5PQuIY/TZzeZL106fDhZWXx/uY+3nRtls1Pd1943q3KNXR/qn/nWxb9le11x6h0Qa1QwNEkHZflYHC4gVNX92gDQf7bgwncPrIunt0ACjN+Ya/d4CNb0ut5fgukcSN3YaNJrAsSOrXgs2f/49yPLnsr969qNnxblYYxHSF3v2rgJAOLxQ0JuZL2tQfG+GABAWg7ZeyeES70FmLoZXtW7lqyq+4bX3hZQ2HJ2oPO3LGBcOgAQsRMBEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAYiYA2JySAhAxBwQk0NCxID8ABSYeGjMobJlAAAAAElFTkSuQmCC)

```
exclusion(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![997a66](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAB8UlEQVRoQ+2WMU7DUAyGnZbSnoGNoYzA1gFGZg7AATgEE1dg5AIsbMyMZegGnZA6cQKExNASBZAjWXo8XqK6SH5G+bNUqp1n5//i3ymuL86/CJcbBQoAccOibgRAfPEAEGc8AARAvCngrB/sEABxpoCzdjAhAOJMAWftYEIAxJkCztrBhACIMwWctYMJARBnCjhrJ+uEjA8ndHx6RoPhqJalXC1pendDi8cZNcU4L7wnvi+lb3hWWINz22I5WGUDIkLwQzMEEZp/F08zGh9M6v/imAALxZQ8BhlfYZ35wz3tH50kz41jqbMsAP1bIDHQNhjvb690e3X5Q0+5PxWzEL6phgsg8naydbGlhBMSx2JLC6cjth8552O1pO3hqLZGsSyZyFQs13RwT9mAxP798jynnd29X3bCIjbFUjD4P4HY6/WJCqL+1qAGEdqSwBJInbesNq8P90RqV4T7JrVT5COhKssayGdVbbynrO0r24T8Zdmm9kfqPJkQAFnztdr0c7RpocefytyGTAnbFl/rfFp3doesya1Tadksq1MqKx4WQBRiWaQCiIXKihoAohDLIhVALFRW1AAQhVgWqQBiobKiBoAoxLJIBRALlRU1AEQhlkUqgFiorKgBIAqxLFIBxEJlRQ0AUYhlkQogFioranwDn5qs+E/9+xgAAAAASUVORK5CYII=)

```
exclusion(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![668599](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABxUlEQVRoQ+2WMU4DQQxFnSBCzkBDBRWiTUNJwRk4AOeg4RBwABouQEFJkzaiS8UJkBBFCCIgr2TJO8zAjLSSv+BvFY0n3r//7ffO6Pzy6lN4wTgwIhAYFp0QAsHiQSBgPAiEQNAcANPDbwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCEzI73Jez02OZTrZltX6Xm7sHmT8uO5tyNV23/fpb/6P7da/28Feu5u/h+1svf/8IVqFAzBB98Pv5Qk5mR50HaooZ72tb43G3/rHZ9Pak6ynQXE+DmKtFQgkDYjCeX17l4vq29zKWah6gh0YgA2TZzF29rWW6M+mNLEvHTzUbT6WR5dd9ymw0+oRYOnNjc4BHbWoRnhAzwY8s/01QY61WO7L8uPO9FssnOdjb/TYWVUNas7HX5OYAm8OBtMz3EhDrUfp2/LbuDxBprwE8bmrxJ4DUJKfm0BANQ+8fBsS/lf57YCec0pHUxlH6n5r1mmN11KiyGIUCacryP9lMIGCgCYRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AbNsurAX4fK/AAAAAElFTkSuQmCC)

```
exclusion(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![338fcc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2XPW7CQBBGxyiioQsFJSVKS06QDnGEXIQzUHGLHAHR5QKIFqVMmQI6GgQhWkuLVhvbAsmwH/BoEF7/jN/jm7Gz18n8YHxkCGQIkXGRF4IQLR8IEfOBEISoERCrhxmCEDECYuWQEISIERArh4QgRIyAWDkkBCFiBMTKISEIuSyB4cuzjd661mo2bLP9tfHnt02X68tetMazJ01ICM/dUwiwbM3t54GXHeO2fyx+7L3fyVHdkpRkQjxwD8yDdt+zr5UNeu0jTL/21MjMMrPd/pBDDo8p+n1LyfAhSyYkTnksKITp1xBSY28sO1VVzy9aK2tZ8fawnZ3S5tzMiVvgFW7/3yXuPiFh8uK5ErY8lZnzUELi4V7VJlOkw10zmZBrDXUScsZfq87H3lNnSPxuovbekiwhZ3h7qF0RIqYbIQgRIyBWDglBiBgBsXJICELECIiVQ0IQIkZArBwSghAxAmLlkBCEiBEQK4eEIESMgFg5f6F+g0BlYmE+AAAAAElFTkSuQmCC)

```
exclusion(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![0099ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABq0lEQVRoQ+2WMU4DQQxFf5BScgH6tJyAgnMgcQIqTkJFkRqJC3AFTpA2FWegQYogyGwsjMmskyB5jPJTjmfX3vdiz0wwX6/BXxkCEwop4+KrEAqp5YNCivmgEAqpRqBYPTxDKKQYgWLlsEMopBiBYuWwQyikGIFi5bBDKKQYgWLlsEMopA+B6xlwfwGcToHXFXDzDDwsh1rGYtnVdu0QC0I+3IJqxWSfgrXPtNYFur5L9twtgNvzAbNI0ee2xVRYppRuQiwkD+ZxCVzNfkObngxrq4+fMFvr26DbzrDdoXt7SLDCKWQzyijEgbCjY5cO0dEjZ8Lb+3fn2HUZgfou2ac/HY1+zPmxmTmqNNe/6hAp2gJ+egEuz4ZPaa37cciR1fibHXKG+LHi3+FvTTxD9ujxQ4TYw9veluyI87cof8tih4xI+uu1t3VNHrs+23OCZ8geHXSsW7sd6scKPPpuCokIJccpJBl4lI5CIkLJcQpJBh6lo5CIUHKcQpKBR+koJCKUHKeQZOBROgqJCCXHKSQZeJSOQiJCyXEKSQYepaOQiFBynEKSgUfpPgGf+knQsuuhLwAAAABJRU5ErkJggg==)

```
exclusion(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2WQWrCQBSGn5RANnoRD+AFeoqA225ceYPewJUnEHKKXsBLuHFbKSgFQaRlBl6RYGrGWuZXvmwCbybJP/+X/8307MW+jEvGgR5AZFhEIQDR4gEQMR4AAYiaA2J62EMAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IycmakPFobPNqbv2yH23Z7Xc2qSe2WC6s61jbM14P773mG7k4ZQPihoeFBwhuXLjXy9qqURVrv43N3mY2fZ7GeafPeL14KuLY4XhI+ob/FDmg3BUQN3j1vrLh6/DHL4e7/lifrV8C24QOkI4JcSCbz40NykFsdaE1eTq2++3ZOkA6ZP2aluVAyqKMIJqtqa1Oy/pnIE2D2/YK9pAOIHzKXxICkASjU6Z2PdqeHonbjrGp9UtH65R13HJutlPWLRfxSO8CiBhNgABEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkfANpCYIAKq8RkQAAAABJRU5ErkJggg==)

```
exclusion(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![ff9900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABlklEQVRoQ+2XMU7DQBBFJ6BINFwgfdqcIAXnQOIEVJyEKkVqpFyAK3AC2lScgQYpgqBhNdLE8hqbBPlLeW6s7K43f//zn11P9mvbG5eMAxOAyLD4EQIQLR4AEeMBEICoOSCmhz0EIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IA0uLA/M5suTKbXpvt3s1e7s22T2VgV5+YmaeQM35CwnBfzeuj2eKhrMuh+OWgct/FtPy+vCr3ABhjHWqfdgeeYedn4mU4hcMD59ACkpOR09EGKAMLSF+7Q5C1dp9vuzGb37bDb+oYaOoxwwFSgz1SSsYF0iwZXaXG+z4/ysvnSYjy5iWqT3uzBJKQSpDzHvJbyWrW/bdns9lNmTgMdkDRTsn6Q/UcCiT/Re3ZaAfIPwOJk5Tf84ksl59aOyWrB5yhe4gfcXNpyt8ste+VrqMtx94ekM58yLinrDM3v235ABF7KQACEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAYiYA2JySIgYkG930EXQvPoyNwAAAABJRU5ErkJggg==)

```
exclusion(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAB30lEQVRoQ+2WPW4CMRBGZyMhpYlS0CRSChoukDJbQcMh6NPBDbgEnIFD0GyXnIMCpaNKg5CijWxpkLP/EzHxRPttubZnZ9+zZ5zkr3lOeMwQSCDEjAufCITY8gEhxnxACIRYI2AsH/QQCDFGwFg6OCEQYoyAsXRwQiDEGAFj6eCEQIgxAsbSwQmBkDKB4+lI82xOu8OOZk8z2k63NLwd+olNYxKWf/ENST51c6OfEAblEly/rGn5vvS5OinucaKKY5t0Q4u3RUlgHfTffCPcFNcA3TWGKSFFCCFIHquDy5LaxHb5Rld4GvP+lRA+MelDSqvn1YUHSyq+5wlVYruMaQBvixlVSFhiOFHuIQzf9RV+Jo8TSpKExvdj2n/uLyWLT8fobvTjfVj2wjhN3yj2sDaA1x6PKiRs2tw3uJlXjYWSHDjuOeevsxeVfWT+UtDUi1CyWraQpJyETT7c/SxkcDMoXQaKlwMIgRBRVYtasqQ9xJWj8Mrr/rSuH3TpE1V9qvc9RLR9ejA56gnpAV/xL0KIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ugQIkamuwBCdPmKo0OIGJnuAgjR5SuODiFiZLoLIESXrzg6hIiR6S6AEF2+4ujfkK7XsJ3U8fsAAAAASUVORK5CYII=)

### average

> 分别对 RGB 的三种颜色值取平均值，然后输出结果。

参数：

- `color1`: 颜色对象。
- `color2`: 颜色对象。

返回值： `颜色（color）`

案例：

```
average(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![803300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABQUlEQVRoQ+2WwQnCQBQFf0S9iK3kYhOiHQhWYAFevVqAFQh2oFiHrYgXFZVdWAgxG1EJ+wKTi/BXk+cMbzfZamRP45IhkCFExoUPghAtHwgR84EQhKgREMvDGYIQMQJicWgIQsQIiMWhIQgRIyAWh4YgRIyAWBwaghAxAmJxkjYkn8xtutxYfzD0WK6Xs+3XCzsdthZbc9+r+s2387pnuLVUVzIhAbj7405CAOo+T8ed5eOZnxXXOt2enz3ut7/mdc8Iz0wlpVVCyrDKUgPEMI8JREik/5+2par2FBvjtrrYNufmsaYhpELIL1sWDWnwZENINdxWnSEc6g02xN2a1953wMka0rDr1t4eIWLqEIIQMQJicWgIQsQIiMWhIQgRIyAWh4YgRIyAWBwaghAxAmJxaAhCxAiIxaEhCBEjIBbnBbq0iwgKm+6AAAAAAElFTkSuQmCC)

```
average(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![994d1a](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABuElEQVRoQ+2XQUpCURSGj1mR0AqcO6wFhBAuICLaQ7SGFtAaqj1EOHUigjhwmENHTVpBUKRWnAdHzru8d3nIk/snv6Mn53r9/b/z33NtPF60f4UvGAcaBALDIhNCIFg8CASMB4EQCJoDYHo4QwgEzAEwOUwIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAtutAp3ct3dt7OWgdy+LzQ8YPdzIfPm/3S2vcPWlCvHn6m7yBsZqutbo+h6aHNV2jkIrW1uhlLVslAxIzbT56kc751drA0FB7X5YCv7ft9V8Ss/NAND1MSIXw+i5+7T/J6eXN+tz3CSmrvc8m0j45yx1DZcdcDAjazEmWED8H9Dh5mw5yBvtjyWp7zf0MwM9qKQbK5oI3PVbz86aoKVLPmaRAfJCqDGkD0jw8ymVQLwNFqSqCVXYB0KYILxYVgl77kmRAYt0Z63a7wsYuBUzIhn0SO7+r1nxX+2POJK2+v7JHS1X43yScO6lTkiwhGzLc+Y8RCBhiAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDl/QOCFELyf+t0AAAAASUVORK5CYII=)

```
average(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![b36633](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABtElEQVRoQ+2XMU4CQRiF/xWMJByBzt7KggtYGA5gsQfgCrS2XoQLmBALC1tqe7stSEioMBSgZgZ/M/zuP0Cz+4pHA5mBmbfv2zdvKZ7Ht9/CF4wDBYHAsIhCCASLB4GA8SAQAkFzAEwPO4RAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIZO/AYDiSm3Ii3V5ftpu1vE+fpJrPwOxpXk6rCVEo4bItkBRYmE+heTDrxsNvFXxuHTvXPIr9jpBALCg1NbxX8xcZDO+j+I/XqVzflfFz3XjR6UohIl+7bQTurWPn2kwrJBB7dyogNfhzWcnb48Pf13R+s1ocjHvr5BJZN9dkWiCAhB6pOzLsEaQpCMdX+I32jzeud3qur9C6DAKIPX68PtGEXFxexU7RI8sbt3f7KZ3FhJSTf31wDIjthFxXpGsRyJHDNzXIK+i0cD3jzx23DwEs9V9Qpz7apv3iPcaeOx7+8+T2b7LI071a7ZC2Lhp5XwIBo0MgBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAm5wfhY7HwPtGTdAAAAABJRU5ErkJggg==)

```
average(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![cc804d](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABn0lEQVRoQ+2XMU4DMRBFZwOKREEDBSVcgPRUiBohrsExOAbXoKBGVDRU4QJQUkBDgYREgmwxyFg2wVFgf8RLldjr3cl7+8e73c3p0dT4yBDoECLjIhaCEC0fCBHzgRCEqBEQq4c9BCFiBMTKISEIESMgVg4JQYgYAbFySAhCxAiIlUNCECJGwMw2Rge2fXhig+GaTV5f7P7izJ7Gl70UunQJSeEFYg4wfHeo6biD9XVhrgR81vxf2VkqITk0l9ANViKv6eQtwvZxh5/KqiUAIXPccgiZA1rrkloLCq2mNPc4vrLN0X7s996afCxPRPjtc893t7a+sxvXpC3ru+u3/pdFHN9ry0rv+Ifrc9vaO/4ElrYdn6u1pp+M5+dPhYdrleYXAbj1HDJC8o221NNbW5aL6laHX7jkDwII+cDz2wlJ29MswSQkk1J6Byi9H9Qeb2vjtb3IE5nvIb4v9fUu0mvLau2v/+F4hIhZRghCxAiIlUNCECJGQKwcEoIQMQJi5ZAQhIgRECuHhCBEjIBYOSQEIWIExMohIQgRIyBWzjua4Jb4INVBcQAAAABJRU5ErkJggg==)

```
average(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![e69966](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAAB0ElEQVRoQ+2WMU7DQBBFx0SJgijo6aGkpaHkFByAc3ANLkDDFShpaNNS0dAQCYGIMAQnaKyMNTtyHFfeL/FdWbvy7vd/+2e2eLm5WgsfGAcKAoFhUQshECweBALGg0AIBM0BMD3sIQQC5gCYHCaEQMAcAJPDhBAImANgcpgQAgFzAEwOE0IgYA6AyYFOyP7xmRyeX0oxnsp6Wcr7w21tn43p+67xr6dHaVtHx/XpmsvBChaIGaWmLGb3cnB6IbI3kkIhrKoEztZxETEgyToi6fd+j82cARsaCiQQg1F9vsnr3XXjiYfk07IL1O/HPFnHJyPuMTSAuF92IF1laf1TSjGZNiWr7bTX5axaNsmxNPnxVblI1vEw4x46lysdCicrkNay5MqM9Y6mZIW57+eZTI5O6kNmsPSbZnxT4mQ0rnvNtnXiXE4oEEDURHvUHJ+EpDSF+h5LWGzUfXtL1x5DlzAIIPrT/lT26RVtpz027i6wfaH/KyDx2tnnGuuvvXbl7bra+h7l14/fxLmhQdh+WROS66eR9yUQMDoEQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHCaEQMAcAJPDhBAImANgcv4AAmi02Diq3YsAAAAASUVORK5CYII=)

```
average(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![ffb380](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmElEQVRoQ+2WIU4EQRBFa8JiEBhOQIJGcAMUWYvFr2MNF+ACGHB4LAZBMHsEBJqEE2AQiM2SJT2hktre9Ex6VuwPeSNnuie//+tfVc3y6XppPDIONACRYdEKAYgWD4CI8QAIQNQcENNDDwGImANickgIQMQcEJNDQgAi5oCYHBICEDEHxOSQEICsO/D59W0XN4/28vpuZydH9nB1bgf7e+3C+O30+NAaM5u9faytE/N1sJytJ8QNTye4nYxtev/cHiZBSU8CFb/NFz8tlN3RThFcWu9g/R8JdnyfgEfY+bfBjm64UQpITEZMhwOKJna9cwg5vAj4bjK2ywL8XMeGHldt/xdA8hP7zQdI1V1Y7Q++tVRqvKT4ze4rQX0lK6YlL48k5K9P1JasvOfkgwAJqUxIqU/4b2LDd1hdQwBABgAo1fvYpGuBlJJAQioB5WNn17ja10NK4y1jbyUUlq86sPWxFyAAkb4DJEQMD0AAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IySEhABFzQEzOL3g60uADLP52AAAAAElFTkSuQmCC)

```
average(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![ff3300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABjUlEQVRoQ+2WUWoCMRBAR2ixUDyNlygeQegJ+uVXz9AvvzxBwSOI9xK/FCxxCYxLsnV3HBzl7Y+4mxkm72WSjE5TOQlPGAIjhIRxcS4EIbF8ICSYD4QgJBqBYPVwhiAkGIFg5dAhCAlGIFg5dAhCghEIVg4dgpBgBIKVQ4cgpEBg9inyvRJ5n4jsdyI/XyKb32Zg17dgMG9Rzv07JANPs1kvReaLZl5JSnqSKP3t5bX5P35rfrPAPDZJveZ9Eq5l65i8GG5BuGeOWEJ0Z+juKAnSwrKk4+FSZO19yrddi3zMy/LbdfSEahn+WELaK1d3V2mbQ0jPtdHeMrq2mto3fea0z5uuLqBDKrJqq7y0ZdEhPVf8kOEIuaD2WGeIvnVxqA9Z/v/EDDlD8t5/7fW2dh3m2usg9AlT3n/LekKolikhxELPIRYhDlAtKRFioecQixAHqJaUCLHQc4hFiANUS0qEWOg5xCLEAaolJUIs9BxiEeIA1ZISIRZ6DrEIcYBqSYkQCz2HWIQ4QLWk/AMv6y3gk27p7wAAAABJRU5ErkJggg==)

```
average(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![80b300](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABb0lEQVRoQ+2WsW3CQBhGf0ckXSago4eeGbIBUiagygbZIBUTILFAxAz00NN5gnRJBNFZMjpZnM8Hlu6L9Vzis/35Pb77Xbx/2tk4ZAgUCJFxUQVBiJYPhIj5QAhC1AiI5WGGIESMgFgcGoIQMQJicWgIQsQIiMWhIQgRIyAWh4Yg5HYCs/GrvUxX9jR6tu/fL9selrYv17ffUPDKrA3xATs2PuTQObfOSXGHLyS23kns+oyckrMJqQHWYH3Q+3Jjs/HiAr3tnIMXutdD8WhWmJ1OP5W8rs/I2bxBCGnuPLUghCTuybFtpq09sS3IbX8pTQtthYmvdPfyf92Q3fHD5pO34DyhIQn/jz5mCEISgMeW9ikk1AQaErPQOH/PZ29zhtQzoOvv/tfZtXmU+Cq9Lc82Q3p7g4HdCCFiQhGCEDECYnFoCELECIjFoSEIESMgFoeGIESMgFgcGoIQMQJicWgIQsQIiMWhIQgRIyAW5w9PuqUI/FHPAQAAAABJRU5ErkJggg==)

```
average(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![803380](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABQUlEQVRoQ+2WQQ6CMBRECzFsvEs3XsLIgdyy9UAYz8Fd3BCDpsQmBCkEhHRIHhuSX2yHec7/JMWpeBsuGQcSgMiwaIUARIsHQMR4AAQgag6I6WGGAETMATE5JAQgYg6IySEhABFzQEwOCQGImANickgIQMQcEJMTNSH2Yk1+zU12zFpb6mdtyltpqntlQmvuuaHfzK2PneHWYl3RgHjD3Ys7CN5Qd68elbFn29a6a+khbWvNq/mrPnaGPzMWlF0B6ZvVh+pN9PUQQIAE8j/VlobS002Ma3WhNufrU60sdAYJ+c6GOf9eErLipFsyQ2hZKwLob7UECEN9QyBuaz57fw2O9pW1Mevdbg8QMXQAAYiYA2JySAhAxBwQk0NCACLmgJgcEgIQMQfE5JAQgIg5ICaHhABEzAExOSQEIGIOiMn5AB2DnwhE6cN0AAAAAElFTkSuQmCC)

### negation

> 与 `difference` 函数效果相反。

输出结果是更亮的颜色。**注意**：效果 *相反* 不代表做加法运算。

参数：

- `color1`: 被减的颜色对象。
- `color2`: 减去的颜色对象。

返回值： `颜色（color）`

案例：

```
negation(#ff6600, #000000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![000000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABEElEQVRoQ+2WMQqEMBQFv4WlFxQ8gefxBIIXtLTYJZGwFq6wbPGnGBshUfJ842i6iHiFB6aBTiAYFjWIQFg8BALjIRCB0BqA5fEfIhBYA7A4GiIQWAOwOBoiEFgDsDgaIhBYA7A4GiIQWAOwOKmGTNMUy7LEMAy1ln3fY57nWNc1vs2V6+7u+XX8aY0yl3WkAWmFlwcvEFqh5bxtW4zjWMeuc33f17HjOP4af1qjrZkFRSA3L0SzNMMSgQjkfO/8ZN37pyEaoiFP/6Y0Q66fLbe9H0SpQDJ2MfQ1BQIjJBCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxdEQgcAagMXREIHAGoDF0RCBwBqAxXkDqIBsEBlzRGIAAAAASUVORK5CYII=)![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)

```
negation(#ff6600, #333333);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![333333](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABNklEQVRoQ+2WQQqDQBAEN+DN5/gJ8UO+wQ+JH/MmJKyJsJgohIRMQcqLMKtM27U966VpmmvywjhwEQiGxSpEICweAoHxEIhAaA7A9HiGCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5IQmpOu61Pd9qut6tWWe5zQMQxrHMR2t5edevfNu/axHXou6woBshucPzxA2Q/N9mqbUtu1aK9eqqlpry7J8VD/rsfWMghIGZL8D94BKQ47WvlXPWs76/zIt4UDK0VSOrNKkPNKOxtkn9bMev4RQ9goHsokxIXcnBPLYEX8/sjzUXw/F0IT42/sMJRRI1MFJ7isQGB2BCATmAEyOCREIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgcAcgMkxIQKBOQCTcwNlf4n47WpFjAAAAABJRU5ErkJggg==)![cc9933](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABlklEQVRoQ+2WMW7CQBBFxyhUEQ0pkNJQ0dLQ5gCII+QinIEjcIEcAVFHKaloXVFSJFKKFBHBRrvSWqPVrmWnYL/hu4IZ8H7+4884261npfCCcSAjEBgWVgiBYPEgEDAeBEIgaA6A6eEOIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHKYEAIBcwBMTucSMpwsZPyylF7/0VpZnH7k8LGyr9vUv/KNxO5leqmuTgFxBhqzNISs92D9K4u/RnXz2c98K0+Tuf2evpd7nwrKTQPxzfWBatPretdMS3IgdWPD75Xn3yoJx/2bjKavdnTF6qFx5moOhj7D710ThDsrKRD9r3QGh0aI7rlRY0B8H95l8DyzvyVWN+PINz80lpgQkWqp/tegmIlt6+Z8AvFMaJIQvbxDqTHGxur+4uZSjwzkuhke6unHWz3zY/dps6PufoekWJroZyZd6ujmpNBHIClcrzmTQAgEzAEwOUwIgYA5ACaHCSEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBAwIBdI9JPQ5Os4xwAAAABJRU5ErkJggg==)

```
negation(#ff6600, #666666);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![666666](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABfUlEQVRoQ+2W0QmEQAxEIyLYkQ1YhQVYh9VYhQ1Yjb/+yB1ZCMRFBVEuAzf+3S5eZuc5yRZ933+ED4wDBYHAsEhCCASLB4GA8SAQAkFzAEwPZwiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTw4QQCJgDYHLCE9I0jXRdJ3Vdy7quMo6jzPOcbDrbe2v9qkYUp1AgZqwefpomads2+aBQ9FFQ+Z7C0veerl/V8B/Fr8GEATEYy7LIMAy7c5/tvbXuk3FU/9cQfL1wINqmtF35lmXpyPcsHU/XfTqO6lvLjAATDsRA+JZlxud7ZVkmj6qqSvPG3rm7rv9xVsNaZhSUcCBHM8PPCf81m/Hbtu3mzN11D+So/l/PEALZN8awhORXTv3tr73+auv3bL5oO3uybim0K3deP2J+aM1QIFGHRq5LIGB0CIRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOR8AWwHvOD715s5AAAAAElFTkSuQmCC)![99cc66](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABx0lEQVRoQ+2WMU7DQBBFx7GC0tBAgURDFUraNJScggNwDq7BBWi4AiVNJKpIVKlSUgBCAslKcIxmpZGWiWVI5GS/zHcVZezdv//tn93s5vGqEj4wDmQEAsMiCCEQLB4EAsaDQAgEzQEwPTxDCATMATA5TAiBgDkAJocJIRAwB8DkMCEEAuYAmBwmhEDAHACTkzQhw4ORnJ9cSr83CLYsloU8zG5l+jqWptq6HsZjxXPoOE21dedp4/1kQMwIXYRC0Efh6DN9GcvwcBR++5oB++vi43kmz/dydnRRO66v6aZI8XQaiMH4mL/J3dP1D3+bailA2JwQQGx3auvSlhInxNfixPhWZymz/8tqEdZZfH3KXj4IrdFalr07L4uVWqp0qKZkQHz/nr1P5Hj/dKWdqIlxrQ6Wb3MGsZflYbw86wcQcVuycQzSv29Zvi34MyXepXHNny/2Xt33uzqn2mxxyRKy6WEbH/51O17rPiHLqtzqxaETQH67cm5yVa37xp8r27patwUlWULaWkDXxiEQMKIEQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHCaEQMAcAJPDhBAImANgcr4B1a6kyD6cO7kAAAAASUVORK5CYII=)

```
negation(#ff6600, #999999);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![999999](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABdklEQVRoQ+2WQQqDMBBFR8FLuHfrCbyo9/EEbl15DtGWCaRMQ2JbLMwHvxshUebnP/+M1TiOD+EF40BFIDAsghACweJBIGA8CIRA0BwA08MZQiBgDoDJYUIIBMwBMDlMCIGAOQAmhwkhEDAHwOQwIQQC5gCYHNeEdF0nwzBI0zTBlm3bZJomWZZFSnv6XO6dX9fPauie1+UGJBquB1cI0VC9R7PSvbquw3PHcby98+v6WY1Y0wsKgWQ+iJhSj5RAAJnnWfq+D61L25ZNiN3b9/2VkCvrZzVs27wVED2snRPrukrbtsED28IUkt2LsK6un9W4ZULSry+dKbaHl/b+tW4/Ds4QEYntJzfg7V6ulaUD+pvn05+ItL7XQNezuM2QtGWlvdu2s9Lv8JX1T/U95oc7EK9DI9d1TQiyMV7aCMTL+UJdAiEQMAfA5DAhBALmAJgcJoRAwBwAk8OEEAiYA2BymBACAXMATA4TQiBgDoDJYULAgDwB53jLyCU+GewAAAAASUVORK5CYII=)![66ff99](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABwUlEQVRoQ+2WMU7DQBBFf4iCcgZ6KNPSUHIKDsA5uAYXoOEKlGnSpk3FCWgoIqIQtCsN2ow2a2sda7/gu4nisdeT9/x3Mnk8PB+gg4bAREJoXMRGJITLh4SQ+ZAQCWEjQNaPZoiEkBEga0cJkRAyAmTtKCESQkaArB0lRELICJC1o4RIyHkI3OIaD7jDHDNsscMLllhhg1Pnz/PU8VdpnpASwC7oAc8b1rjHIpIyIen5KS5ibYZp/Dwlz9fGR59/QlMhBtyDDW97OEICumqWjHBdup5fw39P5eWuDfUWRzMhBu8Dn3jC69FvL9Vy4A2ehAx4hQzeFl+Y4/JoFlg6SrUwO+wI25C98en5Hfbxkj2+f7c2mzm57c3PowE/r/rW5gkxCLlZkKuVthefED/k13jHDa4irHSd8Bxf+7dblofjh3PXLCjNEA81J8xe5VKt+nWvuLF5QsYU0vePQZrOVHAFz8G3NBOSDmfb9/v8JTXIfWaIny3p+v75vjaYbOUCTYVU9vynb5MQMr0SIiFkBMjaUUIkhIwAWTtKiISQESBrRwmREDICZO0oIRJCRoCsHSVEQsgIkLWjhEgIGQGydn4AcL1fwMP++WEAAAAASUVORK5CYII=)

```
negation(#ff6600, #cccccc);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![cccccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABJ0lEQVRoQ+2VQQqDMBQF4w28jvfSZVYu9V5uPIw3aIkgpLbaCoUMOC7z1TxnfKSa5/kRvDAEKoVgXKxBFMLyoRCYD4UohEYAlsczRCEwArA4NkQhMAKwODZEITACsDg2RCEwArA4NkQhMAKwODZEITACsDg2RCGvBJZlCTHGME3TOmiaJvR9H+q6DkezdN+nZ66un+2RZiWuog3ZgKcPb9s2jOO4MkhCNrj7Wdd1YRiGdZ4/c3X9bI/th7i1kD2EXFY++9d6gn30rhIitj1tSIxvbbttQ/K/NJ0h+flxNsvPlqMz55f1b/uXaErRhpT4YPqeCoEZUohCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcWyIQmAEYHFsiEJgBGBxbIhCYARgcZ7C01ywuhk+owAAAABJRU5ErkJggg==)![33cccc](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABWUlEQVRoQ+2WsQ3CMBREnbR0SZEyQ7AEYgQWYQYWYQTEECxBSUE62oAcKZJlcBRFRv9FOlcoVsz5nu5+iu3t9nZaGAcKAcGwGIQICIuHgMB4CIiA0ByA6dEMERCYAzA5SoiAwByAyVFCBATmAEyOEiIgMAdgcpQQAYE5AJNjmpB9Vblj27pNWQ62vPrene53d+k6N7UH8zCrHDMgo+H+Nh6CXx6OX9fn0+3qevgd743AsroAOswMSOxBDMinZFxTeyAvs0gxBxJWU1hZ/napvVSdjSmLKzD1nFiN5kDmpCBMSFhn58fDHZrmq+bmPI+rMHzHshZXDSQ0LlVra6tCMyBLh7oSkmVU/T5k6WfvnNmS+oSeO6f+eO3Jo80SYnVh+v8KCIyQgAgIzAGYHCVEQGAOwOQoIQICcwAmRwkREJgDMDlKiIDAHIDJUUIEBOYATI4SIiAwB2ByPuHjbshIfN0LAAAAAElFTkSuQmCC)

```
negation(#ff6600, #ffffff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ffffff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABP0lEQVRoQ+2X4Q2CMBhEywwMxRDMAMPADAzBUMyAKdqkVCUmGvt+PH62ao973HfY7Pu+By+MA41AMCwOIQJh8RAIjIdABEJzAKbHDhEIzAGYHBMiEJgDMDkmRCAwB2ByTIhAYA7A5JgQgTw7sG1b6Ps+rOsauq4Ly7KEtm2PD77b+9X61Rk1WFVPSDI23vw0TWEcx8OHCCVeEVS5N89zGIbh6/WrM/KH4p9gUEBKE3JYr1KTwJVp+nQ9T0f5nX9CyM8SyGNcCqToh/SUpA5J4yr2Sr6XxtW36/m4Kn+r1riK92lCTMh5Or/riav5/qtusUNeNKVAzqZUHVn5fwk75O5AVSC1Xi3J5woERkcgAoE5AJNjQgQCcwAmx4QIBOYATI4JEQjMAZgcEyIQmAMwOSZEIDAHYHJMiEBgDsDk3AAoaP6YZp2HIwAAAABJRU5ErkJggg==)![0099ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABq0lEQVRoQ+2WMU4DQQxFf5BScgH6tJyAgnMgcQIqTkJFkRqJC3AFTpA2FWegQYogyGwsjMmskyB5jPJTjmfX3vdiz0wwX6/BXxkCEwop4+KrEAqp5YNCivmgEAqpRqBYPTxDKKQYgWLlsEMopBiBYuWwQyikGIFi5bBDKKQYgWLlsEMopA+B6xlwfwGcToHXFXDzDDwsh1rGYtnVdu0QC0I+3IJqxWSfgrXPtNYFur5L9twtgNvzAbNI0ee2xVRYppRuQiwkD+ZxCVzNfkObngxrq4+fMFvr26DbzrDdoXt7SLDCKWQzyijEgbCjY5cO0dEjZ8Lb+3fn2HUZgfou2ac/HY1+zPmxmTmqNNe/6hAp2gJ+egEuz4ZPaa37cciR1fibHXKG+LHi3+FvTTxD9ujxQ4TYw9veluyI87cof8tih4xI+uu1t3VNHrs+23OCZ8geHXSsW7sd6scKPPpuCokIJccpJBl4lI5CIkLJcQpJBh6lo5CIUHKcQpKBR+koJCKUHKeQZOBROgqJCCXHKSQZeJSOQiJCyXEKSQYepaOQiFBynEKSgUfpPgGf+knQsuuhLwAAAABJRU5ErkJggg==)

```
negation(#ff6600, #ff0000);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![ff0000](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABXklEQVRoQ+2ZsQrCQBBEx0JI4w8KfoGVX5LKLxD8QRvBQlmPgzUkmqhwU7w0Yi4Jk3k3uwtZ3aW7OGwcWAHEhsVTCEC8eADEjAdAAOLmgJkeeghAzBwwk0NCAGLmgJkcEgIQMwfM5JAQgJg5YCaHhABkxIHdTjoepc1Gulyk/V46ncqF79bMzPyHnPYJqYbH2/S9dDiU9woocQSovLZel/9dV34rwHptQJ1zPoBn2Pmeuhn+4fDCZ3gBycnI6RgDlIFVSLfbK8ip8/G881nabsfhD3UsNPWXywEyBbtRStoCGZaMd6Um1q7XsvnmJoGEfBHW3EMoWQYfqADysovblqyxxp1r9xBWnrpo6l+Uo0+3LO0hMeLW6WjueDs1DjP2fqLDejjQvmTBwayHAAQgznuAkmVGByAAMXPATA4JAYiZA2ZySAhAzBwwk0NCAGLmgJkcEgIQMwfM5JAQMyAPsN8j6FDzwoQAAAAASUVORK5CYII=)![006600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABgUlEQVRoQ+2WQWrCQBSGn5RANnoRD+AFeoqA225ceYPewJUnEHKKXsBLuHFbKSgFQaRlBl6RYGrGWuZXvmwCbybJP/+X/8307MW+jEvGgR5AZFhEIQDR4gEQMR4AAYiaA2J62EMAIuaAmBwSAhAxB8TkkBCAiDkgJoeEAETMATE5JAQgYg6IycmakPFobPNqbv2yH23Z7Xc2qSe2WC6s61jbM14P773mG7k4ZQPihoeFBwhuXLjXy9qqURVrv43N3mY2fZ7GeafPeL14KuLY4XhI+ob/FDmg3BUQN3j1vrLh6/DHL4e7/lifrV8C24QOkI4JcSCbz40NykFsdaE1eTq2++3ZOkA6ZP2aluVAyqKMIJqtqa1Oy/pnIE2D2/YK9pAOIHzKXxICkASjU6Z2PdqeHonbjrGp9UtH65R13HJutlPWLRfxSO8CiBhNgABEzAExOSQEIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IAIuaAmBwSAhAxB8TkfANpCYIAKq8RkQAAAABJRU5ErkJggg==)

```
negation(#ff6600, #00ff00);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![00ff00](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABd0lEQVRoQ+2WQaoCMRAFnwvBjRcUPIErT+LKEwhe0I3gwk8HAiIzn7HxOT1Q2ehE08lUpdNZ6amnaGUIrBBSxkVbCEJq+UBIMR8IQUg1AsXWQw1BSDECxZZDhiCkGIFiyyFDEFKMQLHlkCEIKUag2HLIEIR8h8Bee5111lZb3XTTQQdddNFY/3dm9UeZNUNe4cWrjoEd+y36TzrpqGMjddVVO+3a996/1ro9b7Rpn1Pn8KMfnmE2IV1GLCt2d7TY8e9g338beo7MiPZfzE/n6DF/LQYhAxuiH3+/lhHzLU5IP5aidvQWx9BQ/1339peHHh9nIRnCkdU2z+IyhBpiOkgp6sVuWa+3ol4PplxJ+21sSg0Zqi1T5pirfsx6ZJkSb/FhZ6shiydnegGEmMBmwyIkS840DiEmsNmwCMmSM41DiAlsNixCsuRM4xBiApsNi5AsOdM4hJjAZsMiJEvONA4hJrDZsAjJkjONQ4gJbDYsQrLkTOP+AGQuKejGr1D7AAAAAElFTkSuQmCC)![ff9900](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABlklEQVRoQ+2XMU7DQBBFJ6BINFwgfdqcIAXnQOIEVJyEKkVqpFyAK3AC2lScgQYpgqBhNdLE8hqbBPlLeW6s7K43f//zn11P9mvbG5eMAxOAyLD4EQIQLR4AEeMBEICoOSCmhz0EIGIOiMkhIQARc0BMDgkBiJgDYnJICEDEHBCTQ0IA0uLA/M5suTKbXpvt3s1e7s22T2VgV5+YmaeQM35CwnBfzeuj2eKhrMuh+OWgct/FtPy+vCr3ABhjHWqfdgeeYedn4mU4hcMD59ACkpOR09EGKAMLSF+7Q5C1dp9vuzGb37bDb+oYaOoxwwFSgz1SSsYF0iwZXaXG+z4/ysvnSYjy5iWqT3uzBJKQSpDzHvJbyWrW/bdns9lNmTgMdkDRTsn6Q/UcCiT/Re3ZaAfIPwOJk5Tf84ksl59aOyWrB5yhe4gfcXNpyt8ste+VrqMtx94ekM58yLinrDM3v235ABF7KQACEDEHxOSQEICIOSAmh4QARMwBMTkkBCBiDojJISEAEXNATA4JAYiYA2JySIgYkG930EXQvPoyNwAAAABJRU5ErkJggg==)

```
negation(#ff6600, #0000ff);

```

![ff6600](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABmUlEQVRoQ+2WQUoDQRBFf5DAbPQiHiAX8BQBt25ceQNv4MoTCDmFF8g5XIugCANBEsqmSFnpiZkmki/82U1PT/P7v/5VPVnfYA09NA5MBISGxbcQAeHiISBkPAREQNgcINOjHiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCECUnFgdg3MH4HuHOg/gMUtsHwqE/d9IzPzGHJOnxA33Hbz/ABc3ZV9GRR7DFT+tlwAs/kuwBo8X8Ng2xOBx/n52zHcbViDC0hMRkyHA7LUDAF0SBHe2bRY8rXaBRznZ/hZR4Oxrb/8LyB+2t9egPvL7Z4d0tB4LXECUjkzuWTEspFLjb2v+rLI5yvQXWxLlpvbv9fHBWREYGMJ+q1kRUjWC7zneGmadqVH5HGVrD8Gkk/8UK9QDxkBwqe2JERAGow+5JexPcTKUbzy7us5fr3NvUjX3kPIaI47cPprr1j8cEBAyA6EgAgImQNkcpQQASFzgEyOEiIgZA6QyVFCBITMATI5SoiAkDlAJkcJERAyB8jkKCFkQDbGaT3Y2qRbKgAAAABJRU5ErkJggg==)![0000ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABX0lEQVRoQ+2WMY7CQAxFPwUlF0TiBFSchIoTIHFBSgrQbNba2WyCXKz+GOmlnDixeS+22UjPp7jKENggpIyLr0IQUssHQor5QAhCqhEoVg87BCHFCBQrhw5BSDECxcqhQxBSjECxcugQhBQjUKwcOgQhYwgcDtLlIu120v0uHY/S9TrV8u6eu9qhHdKDaD+8B7V2r8UF2P6ZtfMGPd7VYs5n6XSaMDcp8dzSvRDmlDJMSA9pDuZ2k/b7v9C22+ns8fgNc+18CXrfGX13ROwICb1whHyPMoTMQPSj4z87JN7VdkdcMRrnY24+Np2jKnLRIXTI9C2wQ5b7jw6hQ36+DMff3nd7gh0yYkt+WM5hI+vDONnKRYgNdS4RQnKcbFEIsaHOJUJIjpMtCiE21LlECMlxskUhxIY6lwghOU62KITYUOcSISTHyRaFEBvqXCKE5DjZohBiQ51LhJAcJ1vUC4LqJ+hH2qZLAAAAAElFTkSuQmCC)![ff66ff](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAAAoCAYAAAAIeF9DAAABm0lEQVRoQ+2W62kDMRCExwSDO0oDqcIFuA5X4yrSQKrJr4DBmDMbtLCI0+PEOTcOc7/s02tuPo1Wu+k0TdBD48BOQGhY/AoREC4eAkLGQ0AEhM0BMj2qIQJC5gCZHCVEQMgcIJOjhAgImQNkcpQQASFzgEyOEiIgMw68AzgCOAC4ArgA+Er9am1LzPyLNZboKfTdPiFulAn8BPCRlBoUewxU3mawbFwOsGT6yBpxU6xgdO8UXEByE6KR3lYy1yG1wPas0eveE/q9FhBPzDeAc3DDIeXvvcsc2J62JxjemnJbIPGIcaVeQ9x8O5b8uaUfP+m48iPL02Fj7V08yubmqa2R17CWgyu3bwvEPmbJ7o3mmnFec96SK/t0KajVIh1ZjS00AsSmjEXfgdyz93k//+83uNaGWHn390z3mgkRkB62A32W1hA7puKV15Ys1YOeOlGrLzFFA582OmT7hIwq/6fjBIQMrIAICJkDZHKUEAEhc4BMjhIiIGQOkMlRQgSEzAEyOUqIgJA5QCZHCREQMgfI5CghAkLmAJmcBwmV9qFnUtr5AAAAAElFTkSuQmCC)

[Improve this section.](https://github.com/less/less-docs/tree/master/content/functions/color-blending.md)