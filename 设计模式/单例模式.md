#### 1. 模块结构

* javscript 核心模块

* c++ 内建模块

Buffer 所占用的内存不是通过V8分配的，属于**堆外内存**。

#### 2. Buffer 对象

Buffer 对象类似于数组，它的元素为16进制的两位数，即0-255的数值。

```js
const str = '深入浅出node.js'
const buf = new Buffer(str, 'utf-8')
console.log(buf)
// <Buffer e6 b7 b1 e5 85 a5 e6 b5 85 e5 87 ba 6e 6f 64 65 2e 6a 73>
console.log(buf.length)
// 19
```

不同编码的字符串占用的元素个数各不相同，上面代码中的中文字在`utf-8`编码下占用3个元素，字母和半角标点符号占用1个元素

Buffer受Array类型的影响很大，可以访问length属性得到长度，也可以通过下标访问元素。

```js
const buf = new Buffer(100)
console.log(buf[10])
```

我们也可以对它赋值：

```js
buf[10] = 100
console.log(buf[10])
// 100
```

> 给元素的赋值如果小于0，就将该值逐次加256，直到得到一个0-255之间的整数。如果得到的数值大于255，就逐次减256，直到得到0-255区间内的数值。如果是小数，则舍弃小数，只保留整数部分。

#### 3. Buffer 内存分配

在C++层面申请内存，在javascript中分配内存。

当我们需要一个Buffer对象，可以通过以下方式分配指定大小的Buffer对象：

new Buffer\(size\)

Node 以 8kb 为界限来区分 Buffer 是大对象还是小对象：

Buffer.poolSize = 8 \* 1024

这个 8kb 的值也就是每个slab\(申请好的固定大小的内存区域\)的大小值，在javascript层面，以它作为单位单元进行内存的分配。

##### 3.1 分配小 Buffer 对象

指定的 Buffer 的大小小于 8kb ，Node 会按照小对象的方式进行分配。

分配的过程中主要使用一个局部变量pool作为中间处理对象，处于分配状态的slab单元都指向它。

```js
var pool
function allocPool() {
    pool = new SlowBuffer(Buffer.poolSize) // SlowBuffer为c++模块的类对象
    pool.used = 0 // 已使用了多少
}


new Buffer(1024)
if (!pool || pool.length - pool.used < this.length) allocPool()
```

当再一次 需要分配的时候，如果当前的slab中的空间还够，则继续使用当前slab，如果不够会构造新的slab，当前的slab剩余空间就浪费掉了

回收内存的时候，只有所有的小 Buffer 对象在作用域释放并都可以回收时，slab的 8kb 空间才会被回收。

##### 3.2 分配大 Buffer 对象

超过 8kb 直接分配一个 SlowBuffer 对象作为slab单元，这个slab单元被这个大Buffer独占。



 

