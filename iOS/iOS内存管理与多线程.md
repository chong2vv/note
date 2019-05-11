> 照例，写在前面的话。
>
> 个人对于移动端的理解，其实无外乎俩个方向，一个是UI处理，一个是逻辑处理（废话），但是不管怎么做，其实就是不断的提高性能，特别是内存以及运行速度。对于性能提升，第一想到的其实就是多线程，毕竟所有的任务都在主线程里做的下场大家可以想象一下。（比如下载一堆小电影，不下载完不能操作。。。emmm，不喜勿喷。。。）。那么作为iOS开发的话，在有一定基础之后想进一步进阶强推的书就是（[《iOS与OS X多线程和内存管理》](#https://item.jd.com/11258970.html) [日] Kazuki Sakamoto，[日] Tomohiko Furumoto 著，黎华 译），本文即为本书的笔记，部分个人观点不对欢迎指正。感谢！

#### [内存管理](#内存管理)
#### block
#### 多线程

## 内存管理
### 内存管理/引用计数
#### 什么是ARC？
说道内存管理，首先不得不谈的就是目前大家都在用的ARC（现在已经很少有公司内存管理还在使用MRC），那么什么是ARC呢？这里直接引用苹果的官方说明：
> 在Objective-C中采用Automatic Reference Counting（ARC）机制，让编译器来进行内存管理。在新一代Apple LLVM编译器中设置ARC为有效状态，就无需再次键入retain或者release代码，这在降低程序崩溃、内存泄漏等风险的同时，很大程度上减少了开发程序的工作量。编译器完全清楚目标对象，并能立刻释放那些不再被使用的对象。如此一来，应用程序将具有可预测性，且能流畅运行，速度也将大幅提升。

#### 引用计数
在Objective-C中的内存管理也就是引用计数。（这里《iOS与OS X多线程和内存管理》这本书里所举的开关灯的例子很生动便于理解）

生成对象：引用计数=1

持有对象：引用计数+1

释放对象：引用计数-1

当引用计数由1归零后，废弃对象

不过说到引用计数，很有可能会不自觉的去将注意力都放在这个计数上，但是实际上我们只要关注这个就好：
- 自己生成，自己持有
- 不是自己生成的，自己也能持有
- 不需要自己持有的时候释放
- 自己不持有的对象自己不能释放

不过现在大多数我们在使用ARC的情况下，我们不需要太关注于处理引用计数了。

1.自己生成持有对象
- alloc
- new
- copy
- mutableCopy

```
    id obj = [NSObject new];
    id obj = [[NSObject alloc] init];
```

2.非自己生成的对象，自己也能持有

```
    id obj = [NSMutableArray array];
    //不过ARC环境下不允许再做引用计数，会报错
    [obj retain]
```

3.不再需要自己持有的对象时释放

```
    id obj = [NSMutableArray array];
    //同retain一样，ARC环境下不允许再做引用计数，会报错
    [obj release];
```

4.无法释放非自己持有的对象


#### 不得不说一下的 copy
copy方法利用基于NSCopying方法约定，由各类实现的copyWithZone: 方法生成并持有对象副本。与copy方法类似，mutableCopy方法利用语句NSMutableCopying方法约定，由各类实现的mutableCopyWithZone:方法生成并持有对象的副本。两者的区别在于copy生成不可变的对象，mutableCopy生成可更变的对象。类似NSArray和NSMutableArray的区别。


#### 苹果引用计数的大致实现
通过断点可以发现苹果每次操作（alloc、release、retain等）时都会调用到一个函数：

```
__CFDoExternRefOperation
```
然后再根据操作进行后续函数，例如：

```
//retain
CFBasicHashAddValue(table, obj);
//release
CFBasicHashRemoveValue(table, obj);
```
从这个过程和命名来看的话，其实苹果的做法不难推测就是用的哈希表来管理引用计数。

#### autorelease （自动释放）
名称虽然看着像ARC，但是还是MRC里的东西。（ARC并不能再被调用）当超出作用于时，对象实例的release实例方法被调用。具体使用方法如下：
1. 生成并持有NSAutoreleasePool对象；
2. 调用已分配对象的autorelease实例方法；
3. 废弃NSAutoreleasePool对象。

在cocoa框架中，相当于程序主循环的NSRunLoop或者在其他程序可运行的地方，对NSAutoreleasePool对象进行生成、持有和废弃处理。这也就表示说，只要RunLoop没有循环回来处理，实际上他是不会被立即释放（可以想象一下轮询，比如10分钟轮询，即使第一分钟就处理完了，但是也要到第十分钟才能去触发）。所以，有的时候autorelease并不合适，比如大规模读取图片生成UIImage（高清~~无码~~大图的那种），等到循环回来处理，基本上内存已经溢出了（特别是运行内存几百兆的年代，更是一个噩梦）。所以autorelease的出现不能说没用，只能说它并不是一个优雅的解决方案。（以上纯属个人观点）

至于autorelease的一个具体实现方式，有兴趣的同学可以去翻翻书，这里笔者就不多做记录了，毕竟现在是全民ARC的年代。

#### IMP Caching （方法缓存）
> 跑个题，这里主要是看到书中一个专栏所写的《提高调用Objective-C方法的速度》，结合到有不少同学在聊天的时候说xxx面试的时候问了IMP，xxx问到了消息传递神马的，完全不知道是什么东西，所以在此单独跑题说一下。[这里有篇文章](#https://tech.meituan.com/2015/08/12/deep-understanding-object-c-of-method-caching.html)写的很好，所以建议读一下。

##### 消息传递机制
首先不得不说就是iOS的方法调用——消息传递机制。iOS是动态语音，它将很多静态语言在编译和链接时期做的事放到了运行时来处理。
我们都知道，在iOS中要调用一个方法是如下：

```
[object method];
```
当调用方法的时候，Runtime层会将这个调用翻译成

```
//msgSend 。。。从名字就能明白它是要干嘛
objc_msgSend(id self, SEL op, ...)
```
objc_mesSend()来做消息分发，大致会分为如下几部：

苹果的一些源码可以在这里[下载查看](https://opensource.apple.com/tarballs/)，描述可以看[这个](#https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)

1. 判断receiver是否为nil，也就是objc_msgSend的第一个参数self，也就是要调用的那个方法所属对象从缓存里寻找，找到了则分发，否则利用objc-class.mm中_class_lookupMethodAndLoadCache3方法去寻找selector；
2. 如果支持GC，忽略掉非GC环境的方法（retain等），从本class的method list寻找selector，如果找到，填充到缓存中，并返回selector，否则寻找父类的method list，并依次往上寻找，直到找到selector，填充到缓存中，并返回selector；
3. 否则调用_class_resolveMethod，如果可以动态resolve为一个selector，不缓存，方法返回，否则转发这个selector；
4. 都没有的话报错，抛出异常。

##### 缓存为谁而生
从上面的分析中我们可以看到，当一个方法在比较“上层”的类中，用比较“下层”（继承关系上的上下层）对象去调用的时候，如果没有缓存，那么整个查找链是相当长的。就算方法是在这个类里面，当方法比较多的时候，每次都查找也是费事费力的一件事情。例如：

```
for ( int i = 0; i < 100000; ++i) {
    MyClass *myObject = myObjects[i];
    [myObject methodA];
}
```
当我们需要去调用一个方法数十万次甚至更多地时候，查找方法的消耗会变的非常显著。

就算我们平常的非大规模调用，除非一个方法只会调用一次，否则缓存都是有用的。在运行时，那么多对象，那么多方法调用，节省下来的时间也是非常可观的。

##### 什么是方法缓存
通过objc-cache.mm的源文件可以看到结构体的定义：

```
struct objc_cache {
    uintptr_t mask;            /* total = mask + 1 */
    uintptr_t occupied;       
    cache_entry *buckets[1];
};
```
- mask：可以认为是当前能达到的最大index（从0开始的），所以缓存的size（total）是mask+1
- occupied：被占用的槽位，因为缓存是以散列表的形式存在的，所以会有空槽，而occupied表示当前被占用的数目
- buckets：用数组表示的hash表，cache_entry类型，每一个cache_entry代表一个方法缓存

cache_entry的结构体：
```
typedef struct {
    SEL name;     // same layout as struct old_method
    void *unused;
    IMP imp;  // same layout as struct old_method
} cache_entry;
```
- name，被缓存的方法名字
- unused，保留字段，还没被使用。
- imp，方法实现

##### 方法缓存存在什么地方？
可以看一下类的定义：

```
struct _class_t {
    struct _class_t *isa;
    struct _class_t *superclass;
    void *cache;
    void *vtable;
    struct _class_ro_t *ro;
};
```
可以看到在类的定义里就有cache字段，没错，类的所有缓存都存在metaclass上，所以每个类都只有一份方法缓存，而不是每一个类的object都保存一份。

逼逼了半天，那么至于怎么使用就简单的举个例子:

```
- (void)viewDidLoad {
    [super viewDidLoad];
    //    objc_msgSend(id self, SEL op, ...)
    ((id (*)(id, SEL))objc_msgSend)(self, @selector(helloWorld));
}

- (void)helloWorld{
    NSLog(@"objc_msgSend=======helloworld");
}
```
当然，这么写的话，大家会感觉，卧槽。。。怎么看着这么乱，这是因为苹果再预处理时启用了严格检查objc_msgSend。这里我们可以把他关掉：*Apple LLVM 8.1 - Preprocessing -> Enable Strict Checking of objc_msgSend Calls*

![image](https://upload-images.jianshu.io/upload_images/4068393-4d6e344c43f62c96.png)

这样，我们再写的时候可以这么写了：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    //    objc_msgSend(id self, SEL op, ...)
    objc_msgSend(self, @selector(helloWorld));
    
}

- (void)helloWorld{
    NSLog(@"objc_msgSend=======helloworld");
}
```

至此，简单的IMP caching就到这里了，不过还是要再唠叨一句就是一定要记住技术是为了解决问题而存在的，不是为了使用而使用的。就像`objc_msgSend`这个方法，他是为了提高效率，比如循环十万次调用某个方法等情况，千万不要为了使用而通篇都使用这个方法，那么你可想而知如果全部改用这个方法，代码还能看么。。。

### ARC规则
引用计数式内存管理的思考方式就是思考ARC所引起的变化。
- 自己生成的对象，自己所持有
- 非自己生成的对象，自己也能持有
- 自己持有的对象不再需要时释放
- 非自己持有的的对象无法释放

#### 所有权修饰符
Objective-C中为了处理对象，可将变量类型定义为id类型或各种对象类型。ARC下id类型和对象类型其类型必须附加所有权修饰，所有权修饰符一共有4种：
- __strong 修饰符
- __weak 修饰符
- __unsafe_unretained 修饰符
- __autoreleasing 修饰符

其中，id类型和对象类型默认的所有权修饰符是__strong。

