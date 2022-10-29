## Path.join()连接路径

path.join()方法可以连接任意多个路径字符串.要连接的多个路径可做为参数传入.

path.join() 方法在连接路径的同时也会对路径进行规范化.例如:

```js
var path = require('path'); 
//合法的字符串连接 
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..') 
// 连接后 
'/foo/bar/baz/asdf' 

//不合法的字符串将抛出异常 
path.join('foo', {}, 'bar') 
// 抛出的异常 TypeError: Arguments to path.join must be strings'
```

## Path.resolve()路径解析

path.resolve()方法可以将多个路径解析为一个规范化的绝对路径.其处理方式类似于对这些路径逐一进行cd操作,与cd操作不同的是,这引起路径可以是文件,并且可不必实际存在(resolve()方法不会利用底层的文件系统判断路径是否存在,而只是进行路径字符串操作).

eg:

```js
path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')
```

相当于:

```js
cd foo/bar
cd /tmp/file/
cd ..
cd a/../subfile
pwd
```

例子:

```js
path.resolve('/foo/bar', './baz') 
// 输出结果为 
'/foo/bar/baz' 
path.resolve('/foo/bar', '/tmp/file/') 
// 输出结果为 
'/tmp/file' 

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif') 
// 当前的工作路径是 /home/itbilu/node，则输出结果为 
'/home/itbilu/node/wwwroot/static_files/gif/image.gif'
```

例子:

```js
const path = require('path'); 
let myPathjoin_1 = path.join(__dirname,'/img/so'); 
let myPathjoin_2 = path.join(__dirname,'./img/so'); 
let myPathjoin_3 = path.join(__dirname,'../img/so'); 
let myPathresolve_1 = path.resolve(__dirname,'/img/so'); 
let myPathresolve_2 = path.resolve(__dirname,'./img/so'); 
let myPathresolve_3 = path.resolve(__dirname,'../img/so'); 
console.log(__dirname); //D:\myProgram\test 
console.log('------join------')
console.log(myPathjoin_1); //D:\myProgram\test\img\so 
console.log(myPathjoin_2); //D:\myProgram\test\img\so 
console.log(myPathjoin_3); //D:\myProgram\img\so 
console.log('------resolve------')
console.log(myPathresolve_1); //D:\img\so
console.log(myPathresolve_2); //D:\myProgram\test\img\so
console.log(myPathresolve_3); //D:\myProgram\img\so
```

小总结:

从后向前,若字符以/开头,不会拼接到前面的路径(因为拼接到此已经是一个绝对路径);若以../开头,拼接前面的路径,且不含最后一节路径;若以./开头 或者 没有符号 则拼接前面的路径.

**__dirname**

代表当前所在文件路径

**__filename**

代表当前所在文件名称

