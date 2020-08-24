# String

这里整理了 String 对象常用的方法。

## charAt()

**charAt()** ： 从一个字符串中返回指定的字符。

- 语法: `str.charAt(index)`
- 参数：**index**
  0 ~ length - 1 的整数。默认： 0
- 例子

  ```js
  console.log('hello world!'.charAt(2));
  // e
  ```

## slice()

`slice()` 方法提取某个字符串的一部分，并返回一个新的字符串，且`不会改动原字符串`。

- 参数
  - beginIndex
    从该索引（以 0 为基数）处开始提取原字符串中的字符。
  - endIndex
    可选。在该索引（以 0 为基数）处结束提取字符串。如果省略该参数，slice() 会一直提取到字符串末尾。

> 参数如果值为负数，会被当做 strLength + Index 看待，这里的 strLength 是字符串的长度（例如， 如果 Index 是 -3 则看作是：strLength - 3）

- 返回值： 新的字符串

## substr()

`substr()` 方法返回一个字符串中从`指定位置`开始到`指定长度`的字符。

- 语法：
- 参数
  - start
    开始提取字符的位置。如果为负值，则被看作 strLength + start，其中 strLength 为字符串的长度（例如，如果 start 为 -3，则被看作 strLength + (-3)）。
  - length 可选
    提取的字符数。

## substring()

`substring()` 方法返回一个字符串在`开始索引`到`结束索引`之间的一个子集, 或从开始索引直到字符串的末尾的一个子集。

- 参数
  - indexStart
    需要截取的第一个字符的索引，该索引位置的字符作为返回的字符串的首字母。
  - indexEnd 可选
    一个 0 到字符串长度之间的整数，该索引所在字符`不包含在内`。
- tips

  - 如果 indexStart 等于 indexEnd，substring 返回一个空字符串。
  - 如果省略 indexEnd，substring 提取字符一直到字符串末尾。
  - 如果任一参数小于 0 或为 NaN，则被当作 0
  - 如果任一参数大于 stringName.length，则被当作 stringName.length。
  - 如果 indexStart 大于 indexEnd，则 substring 的执行效果就像两个参数调换了一样。

- 示例

```js
var anyString = 'Mozilla';

// 输出 "Moz"
console.log(anyString.substring(0, 3));
console.log(anyString.substring(3, 0));
console.log(anyString.substring(3, -3));
console.log(anyString.substring(3, NaN));
console.log(anyString.substring(-2, 3));
console.log(anyString.substring(NaN, 3));
```

## includes()

**includes()** 方法用于判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false。

- 语法： str.includes(searchString[, position])
- 参数：
  - searchString
    要在此字符串中搜索的字符串。
  - position 可选
    从当前字符串的哪个索引位置开始搜寻子字符串，默认值为 0。
- 返回值
  如果当前字符串包含被搜索字符串，返回`true`;否则返回 `false`。
- 示例
  ```js
  var str = 'To be, or not to be, that is the question.';
  console.log(str.includes('To be')); // true
  console.log(str.includes('nonexistent')); // false
  console.log(str.includes('To be', 1)); // false
  console.log(str.includes('TO BE')); // false
  ```

## indexOf()

`indexOf()` 方法返回调用它的 String 对象中`第一次`出现的指定值的索引，从 fromIndex 处进行搜索。如果未找到该值，则返回 -1。

- 语法： `str.indexOf(searchValue [, fromIndex])`
- 参数：
  - searchValue
    要被查找的字符串值。默认： "undefined"
  - 数字表示开始查找的位置。可以是任意整数，默认值为 0。
- 返回值
  查找的字符串 searchValue 的第一次出现的索引，如果没有找到，则返回`-1`。

## lastIndexOf()

`lastIndexOf()` 方法返回调用 String 对象的指定值`最后一次`出现的索引，在一个字符串中的指定位置 fromIndex 处从后向前搜索。如果没找到这个特定值则返回-1 。

- 语法： `str.lastIndexOf(searchValue[, fromIndex])`
- 参数：
  - searchValue
    一个字符串，表示被查找的值。如果 searchValue 是空字符串，则返回 fromIndex。
  - fromIndex 可选
    待匹配字符串 searchValue 的开头一位字符从 str 的第 fromIndex 位开始向左回向查找。fromIndex 默认值是 +Infinity。如果 fromIndex >= str.length ，则会搜索整个字符串。如果 fromIndex < 0 ，则等同于 fromIndex == 0。
- 返回值
  返回指定值最后一次出现的索引(该索引仍是以从左至右 0 开始记数的)，如果没找到则返回`-1`。

## match()

`match()` 方法检索返回一个字符串匹配正则表达式的结果。

- 语法： `str.match(regexp)`
- 参数：
- regexp
  一个正则表达式对象。如果传入一个非正则表达式对象，则会隐式地使用 new RegExp(obj) 将其转换为一个 RegExp 。如果你没有给出任何参数并直接使用 match() 方法 ，你将会得到一 个包含空字符串的 Array ：[""] 。
- 返回值
  - 如果使用 g 标志，则将返回与完整正则表达式匹配的所有结果，但不会返回捕获组。
  - 如果未使用 g 标志，则仅返回第一个完整匹配及其相关的捕获组（Array）。 在这种情况下，返回的项目将具有如下所述的其他属性。

示例：

使用 match 查找 "Chapter" 紧跟着 1 个或多个数值字符，再紧跟着一个小数点和数值字符 0 次或多次。正则表达式包含 i 标志，因此大小写会被忽略。

```js
var str = 'For more information, see Chapter 3.4.5.1';
var re = /see (chapter \d+(\.\d)*)/i;
var found = str.match(re);

console.log(found);

// logs [ 'see Chapter 3.4.5.1',
//        'Chapter 3.4.5.1',
//        '.1',
//        index: 22,
//        input: 'For more information, see Chapter 3.4.5.1' ]

// 'see Chapter 3.4.5.1' 是整个匹配。
// 'Chapter 3.4.5.1' 被'(chapter \d+(\.\d)*)'捕获。
// '.1' 是被'(\.\d)'捕获的最后一个值。
// 'index' 属性(22) 是整个匹配从零开始的索引。
// 'input' 属性是被解析的原始字符串。
```

## matchAll()

`matchAll()` 方法返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器。

- 语法： `str.matchAll(regexp)`
- 参数：

  - regexp
    正则表达式对象。如果所传参数不是一个正则表达式对象，则会隐式地使用 new RegExp(obj) 将其转换为一个 RegExp 。

  RegExp 必须是设置了全局模式 g 的形式，否则会抛出异常 TypeError。

- 返回值
  一个迭代器（不可重用，结果耗尽需要再次调用方法，获取一个新的迭代器）。
- 示例

  ```js
  const regexp = /t(e)(st(\d?))/g;
  const str = 'test1test2';

  const array = [...str.matchAll(regexp)];

  console.log(array[0]);
  // expected output: Array ["test1", "e", "st1", "1"]

  console.log(array[1]);
  // expected output: Array ["test2", "e", "st2", "2"]
  ```

## concat()

**concat()** 将一个或多个字符串与元字符串连接合并，形成一个新的字符串并返回。

- 语法： `str.concat(str2, [, ...strN])`
- 参数：
  - `str2 [, ...strN]`
    需要连接到`str` 的字符串
- 返回值：一个新的字符串
- 性能： 强烈建议使用赋值操作符（+，+=）代替 concat 方法。
- 实例

  ```js
  let hello = 'Hello, ';
  console.log(hello.concat('Kevin', '. Have a nice day.'));
  // Hello, Kevin. Have a nice day.

  let greetList = ['Hello', ' ', 'Venkat', '!'];
  ''.concat(...greetList); // "Hello Venkat!"

  ''.concat({}); // [object Object]
  ''.concat([]); // ""
  ''.concat(null); // "null"
  ''.concat(true); // "true"
  ''.concat(4, 5); // "45"
  ```

## padEnd()

`padEnd()` 方法会用一个字符串填充当前字符串（如果需要的话则重复填充），返回填充后达到指定长度的字符串。从当前字符串的末尾（右侧）开始填充。

- 语法： `str.padEnd(targetLength [, padString])`
- 参数：
  - targetLength
    当前字符串需要填充到的目标长度。如果这个数值小于当前字符串的长度，则返回当前字符串本身。
  - padString 可选
    填充字符串。如果字符串太长，使填充后的字符串长度超过了目标长度，则只保留最左侧的部分，其他部分会被截断。此参数的缺省值为 " "（U+0020）。
- 返回值
  填充之后的新字符串
- 示例

  ```js
  'abc'.padEnd(10); // "abc       "
  'abc'.padEnd(10, 'foo'); // "abcfoofoof"
  'abc'.padEnd(6, '123456'); // "abc123"
  'abc'.padEnd(1); // "abc"
  ```

## padStart()

`padStart()`方法用另一个字符串填充当前字符串(如果需要的话，会重复多次)，以便产生的字符串达到给定的长度。从当前字符串的左侧开始填充。

语法、参数同 `padEnd`。

示例

```js
'abc'.padStart(10); // "       abc"
'abc'.padStart(10, 'foo'); // "foofoofabc"
'abc'.padStart(6, '123465'); // "123abc"
'abc'.padStart(8, '0'); // "00000abc"
'abc'.padStart(1); // "abc"
```
