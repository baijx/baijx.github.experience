# export和export default

1. export与export default均可用于导出常量、函数、文件、模块等
2. 你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用
3. 在一个文件或模块中，export、import可以有多个，export default仅有一个
4. 通过export方式导出，在导入时要加{ }，按需导入，export default则不需要

```js
1.export
//a.js
export const str = "blablabla~";
export function log(sth) { 
  return sth;
}
对应的导入方式：

//b.js
import { str, log } from 'a'; //也可以分开写两次，导入的时候带花括号

2.export default
//a.js
const str = "blablabla~";
export default str;
对应的导入方式：

//b.js
import str from 'a'; //导入的时候没有花括号
```

```js
// test.js
var info = {
    name: 'zs',
    age: 20
}
export default info 
export var title = '小星星' 
export var content = '哈哈哈'

// main.js
import person, {title, content as content1} from './test.js'
console.log(person);
console.log(title + '=======' + content1);
```