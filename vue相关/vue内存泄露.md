#### vue可能引起的内存泄露

1. 监听在 window/body 等事件没有解绑；
2. 绑定在 EventBus 的事件没有解绑；
3. vuex 的 $store watch了之后没有 unwatch;
4. 模块形成的闭包内部变量使用后没有置为 null；
5. 使用第三方库没有调用正确的销毁函数；
