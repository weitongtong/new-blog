#### Set
* 成员唯一不重复
* [value, value] 键名和键值一样
* 可以遍历，方法有 add、delete、has

#### WeakSet
* 成员都是对象
* 成员都是弱引用，可以被垃圾回收机制回收，可以用来保存 DOM 节点，不容易造成内存泄漏；
* 不能遍历，方法有 add、delete、has