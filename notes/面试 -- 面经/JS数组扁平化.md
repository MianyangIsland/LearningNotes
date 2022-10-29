## 数组扁平化

一.普通递归实现

通过for循环的方式,逐层逐个元素地去展平,如果当前元素是一个数组,那么就对它进行递归处理,再将递归处理的结果拼到结果数组上.

```js
let arr = [1, [2, [3, 4, 5]]];
function flatten(arr) {
  let result = [];
  for(let i = 0; i < arr.length; i++) {
    // 当前元素是一个数组，对其进行递归展平
    if(Array.isArray(arr[i])) {
      // 递归展平结果拼接到结果数组
      result = result.concat(flatten(arr[i]));
    } 
    // 否则直接加入结果数组
    else {
      result.push(arr[i]);
    }
  }
  return result;
}
console.log(flatten(a));  //  [1, 2, 3, 4，5]
```

二.reduce实现

上面的递归实现的关键就是对数组的每一项进行处理,遇到数组就递归处理它,既然需要循环和结果数组,那么就可以使用reduce来简化我们的代码:

```js
let arr = [1,[2,[3,4,5]]];
function flatten(arr){
    return arr.reduce(function(pre,cur){
        return pre.concat(Array.isArray(cur)? flatten(cur) : cur);
    },[])
}
console.log(flatten(arr));
```

使用reduce后,代码更加简洁,reduce的第一个参数用来返回最后累加的结果,第二个参数是当前遍历的元素值,处理数组元素和非数组元素的思路和第一种一样,最后再把处理后的结果拼接到累加结果数组中返回即可.

三. 扩展运算符实现

扩展运算符是ES6的新特性之一,用它操作数可以之恶展开数组的第一层,利用这个特性,我们可以不使用递归来实现数组的展平,这是因为每一次递归都是对当前层次数组的一次展开,而扩展操作就是干这工作的.

```js
let arr = [1,[2,[3,[4,[5]]]]];

function flatten(arr){
    while(arr.some(i=>Array.isArray(i))){
        arr = [].concat(...arr);
    }
    return arr;
}
console.log(flatten(arr));
```

四, split + toString 实现

我们也可以通过 `split` 和 `toString` 两个方法，来共同实现数组扁平化。`toString`是`Object`原型链上的一个方法，由于`JS`中所有对象都派生自`Object`对象，所以它们都能调用`toString`，只不过不同的对象可能会对这个方法进行改写以输出自己想要的格式。数组的`toString`方法会将数组转换成一个元素间以逗号相隔的字符串，它内部会先将数组展平成一维后再转换成字符串，因此我们可以先利用`toString`进行展平，然后再通过`split`方法以逗号分隔每个元素来复原一个包含所有元素的数组，从而实现数组的扁平化。

```js
let arr = [1,[2,[3,[4,[5]]]]];

function flatten(arr){
    return arr.toString().split(',').map(i=>Number(i));
}

console.log(flatten(arr));
```

不过需要注意的是，虽然这个方法非常简单，但具有一定的局限性，对于包含引用类型元素的数组来说，在`toString`过程中会发生类型转换，从而使得转换结果异常，因为对于引用类型转成字符串，会调用引用类型的`toString`，上面提到不同对象会对它进行改写，例如函数就会得到一个函数体的代码字符串，而不是我们想要的函数引用。

因此，使用这种方式我们要看元素类型而定。

 五. Array.prototype.flat

Arry.prototype.flat 是 ES6 新增的一个数组方法,它的作用就是用来数组扁平化,并且根据传入的参数来决定展开的层级,是数组扁平化的终极解决方案:

```js
let arr = [1,[2,[3,[4,[5]]]]];
function flatten(arr){
    return arr.flat(Infinity);
}

console.log(flatten(arr));
```

