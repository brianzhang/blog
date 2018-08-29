title: '迭代器,生成器(generator)和Promise的“微妙”关系'
author: brian
tags: []
categories: []
date: 2018-08-29 16:09:00
---
> 好久没写blog了，重新开始，今天来聊聊generator，promise的关系

这次主要讲讲（iterator）和生成器*/yield之间的联系和各自的用法，以及生成器的高配版本aysnc/await的使用。

栏目：
 - 迭代器（iterator）
 - 生成器 */yield
 - 异步版生成器 aysnc/await


##### 迭代器（iterator）
----------------------

	首先迭代器这个词是什么意思呢？每一次“过程”的重复，称之为迭代。不过迭代是会保留结果的，也就说每次都是以上一次迭代的结果为基准，开始下一次的迭代。举个例子，迭代这个词经常出现在产品开发之中，每个周期都会有产品的迭代开发，但是不可能每次都是从零开始做产品，肯定是基于上一版本的产品进行开发，也就是进行迭代。
    
从中我们可以整理出关于迭代关键点：
- 过程是重复的
- 返回上一次的迭代结果

那么javascript中的“迭代器”是个怎样的概念呢？

	javascript中的迭代器，就是一个数组对象，不断地调用next重复获取过程，然后每次都返回一个结果。等到没有东西可返回了，就终止。因此next的返回对象有两个属性done和value。done表示是否结束了，value表示当前迭代的结果。当done为true的时候，表示迭代已结束，这时候是没有返回结果的也就是没有value这个属性。
    
- 关于迭代器，就是我们上面讨论的next方法，返回done和value（done:true时可以省略）两个参数。

```js
 function iteratorFunc(){
    let arr=[...arguments]
    let nIndex=0
    return {
        next:()=>{
            return nIndex<arr.length?
            {value:arr[nIndex++],done:false}:{done:true}
        }
    }
}
let a=iteratorFunc(1,2,3)
console.log(a.next())//{done:false,value:1}
console.log(a.next())//{done:false,value:2}
console.log(a.next())//{done:false,value:3}
console.log(a.next())//{done:true}
```
###### 可迭代“对象”
- 关于可迭代“对象”，我们需要再对象上实现@@iterator方法，也就是[Symbol.iterator]，返回一个自定义的迭代方法，以表明这个对象是可以迭代的。有些JS内置的对象就是可迭代的，比如String，Array。

> 自带的迭代事例

```js
  let str="我是欢乐的迭代器"
  let b=str[Symbol.iterator]()
  console.log(b.next())//{value: "I", done: false}
  console.log(b.next())//{value: "M", done: false}
  console.log(b.next())//{value: "B", done: false}
```

> 自定义迭代方法

```js
  str[Symbol.iterator] = function() {
      return { // this is the iterator object, returning a single element, the string "bye"
        next: function() {
          this._index += 2
          if (this._index<str.length) {
            return { value: str[this._index], done: false };
          } else {
            return { done: true };
          }
        },
        _index:-2
      };
  };
  let c=str[Symbol.iterator]()
  console.log(c.next())//{value: "I", done: false}
  console.log(c.next())//{value: "M", done: false}
  console.log(c.next())//{value: "B", done: false}
  console.log(c.next())//{value: "R", done: false}
  console.log(c.next())//{done: true}
```

##### 生成器-generator
----------------------

> 生成器(generator)，专门帮我们生成迭代器

```js
  function * g(){}
  let it= g()
  console.log(it.next())//{value: undefined, done: true}
```

> 比较老土的实现方法
```js
  str[Symbol.iterator]= function * (){
      let index=-2;
      while(index<this.length){
          index += 2
          yield this[index]
      }
  }
  let kk=str[Symbol.iterator]()
  console.log(kk.next())//{value: "I", done: false}
  console.log(kk.next())//{value: "M", done: false}
  console.log(kk.next())//{value: "B", done: false}
  console.log(kk.next())//{value: "R", done: false}
```
> 生成器到底干了什么？
- yield 的返回值是啥？
- 执行顺序是什么样的？

```js
  function * gy(){
      console.log("zero")
      let fisrt=yield "first"
      console.log("fisrt",fisrt)
      let second=yield "first"
      console.log("second",second)
  }
  let ity= gy()
```
> run code
```bash
> ity.next() # zero
> ity.next() # first undefined
> ity.next("third") # second third
```
> 由此可见next每次都停止在yield就不再继续执行。yield每次返回的都是当前的ity.next(value)和value的值

###### aysnc/await

> 对于Promise这个对象的迭代器，我们该怎么处理。也就是每个迭代器都是异步的

```js
  function setTime(value,id){
      return new Promise((r,j)=>setTimeout(() => {
          console.log(value)
          r(id)
      }, 10))
  }
  function *a(){
      let r1 = yield setTime("first",1)
      console.log(r1)
      let r2 =yield setTime("second",2)
      console.log(r2)
      let r3 =yield setTime("third",3)
      console.log(r3)
  }
  let k=a();
  new Promise((resolve,reject)=>{
      function next(data){
          let {value,done}=k.next(data)
          //k.next()返回一个promise,因此可以then
          if(!done){
              value.then((data)=>{
                  console.log(data)
                  next(data)
              })
          }
      }
      next();
  })
```

> 这个时候aysnc/await就可以出场了。只需要把*/yield无缝改成aysnc/await即可。

```js
async function a() {
    let r1 = await setTime("first",1)
    console.log(r1)
    let r2 = await setTime("second",2)
    console.log(r2)
    let r3 = await setTime("third",3)
    console.log(r3)
}
a()
```