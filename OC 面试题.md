# OC 面试题

一张非常经典的描述instance对象、类对象以及元类对象之间关系的图片。途中虚线代表isa指针，实线代表superClass指针。
![](https://user-gold-cdn.xitu.io/2019/12/27/16f47b9aa164204d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



#### 1. OC 系统对象的 copy 与 mutableCopy 方法

* 非集合类对象的copy与mutableCopy
  在非集合类对象中，对不可变对象进行copy操作，是指针复制，mutableCopy操作是内容复制；
  对可变对象进行copy和mutableCopy都是内容复制。用代码简单表示如下：

	```objc
    NSString *str = @"hello word!";
    NSString *strCopy = [str copy] // 指针复制，strCopy与str的地址一样
    NSMutableString *strMCopy = [str mutableCopy] // 内容复制，strMCopy与str的地址不一样
 
    NSMutableString *mutableStr = [NSMutableString stringWithString: @"hello word!"];
    NSString *strCopy = [mutableStr copy] // 内容复制
    NSMutableString *strMCopy = [mutableStr mutableCopy] // 内容复制
	```
 
* 集合类对象的copy与mutableCopy (同上)
  在集合类对象中，对不可变对象进行copy操作，是指针复制，mutableCopy操作是内容复制；
  对可变对象进行copy和mutableCopy都是内容复制。但是：集合对象的内容复制仅限于对象本身，对集合内的对象元素仍然是指针复制。(即单层内容复制)
  	```objc
    NSArray *arr = @[@[@"a", @"b"], @[@"c", @"d"];
    NSArray *copyArr = [arr copy]; // 指针复制
    NSMutableArray *mCopyArr = [arr mutableCopy]; //单层内容复制
   
    NSMutableArray *array = [NSMutableArray arrayWithObjects:[NSMutableString stringWithString:@"a"],@"b",@"c",nil];
    NSArray *copyArr = [mutableArr copy]; // 单层内容复制
    NSMutableArray *mCopyArr = [mutableArr mutableCopy]; // 单层内容复制
	```
    
【总结一句话】：
    只有对不可变对象进行copy操作是指针复制（浅复制），其它情况都是内容复制（深复制）

#### 2.浅拷贝和深拷贝的区别？ 
* 浅拷贝：只复制指向对象的指针，而不复制引用对象本身。

* 深拷贝：复制引用对象本身。内存中存在了两份独立对象本身，当修改A时，A_copy不变

#### 3. Category（类别）、 Extension（扩展）和继承的区别
*  分类有名字，类扩展没有分类名字，是一种特殊的分类。

* 分类只能扩展方法（属性仅仅是声明，并没真正实现），类扩展可以扩展属性、成员变量和方法。

* 继承可以增加，修改或者删除方法，并且可以增加属性

#### 4.ViewController生命周期

* `initWithCoder：`通过nib文件初始化时触发。

* `awakeFromNib：`nib文件被加载的时候，会发生一个awakeFromNib的消息到nib文件中的每个对象。      
* `loadView：`开始加载视图控制器自带的view。
* `viewDidLoad：`视图控制器的view被加载完成。  
* `viewWillAppear：`视图控制器的view将要显示在window上。
* `updateViewConstraints：`视图控制器的view开始更新AutoLayout约束。
* `viewWillLayoutSubviews：`视图控制器的view将要更新内容视图的位置。
* `viewDidLayoutSubviews：`视图控制器的view已经更新视图的位置。
* `viewDidAppear：`视图控制器的view已经展示到window上。 
* `viewWillDisappear：`视图控制器的view将要从window上消失。
* `viewDidDisappear：`视图控制器的view已经从window上消失。

#### 7. @synthesize 和 @dynamic 分别有什么作用？

* `@property`有两个对应的词，一个是 `@synthesize`，一个是 `@dynamic`。如果 `@synthesize`和 `@dynamic`都没写，那么默认的就是`@syntheszie var = _var`;

* `@synthesize` 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
* `@dynamic` 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。假如一个属性被声明为 `@dynamic var`，然后你没有提供 `@setter`方法和` @getter `方法，编译的时候没问题，但是当程序运行到 `instance.var = someVar`，由于缺 setter 方法会导致程序崩溃；或者当运行到 `someVar = var` 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。

#### 8. UIView和 CALayer是什么关系?
* 创建UIView对象时，UIView内部会自动创建一个层(CALayer对象)，通过UIView的layer属性可以访问这个层。当UIView需要显示到屏幕上时，会调用drawRect:方法进行绘图渲染，并且会将所有内容绘制在自己的层上，绘图完毕后，系统会将层拷贝到屏幕上，于是就完成了UIView的显示

* UIView相比CALayer最大区别是UIView继承自UIResponder，可以响应用户事件，而CALayer不可以；UIView侧重于对显示内容的管理，CALayer侧重于对内容的绘制。

* UIView本身，更像是一个CALayer的管理器，访问它的和绘图、坐标相关的属性，如frame，bounds等，实际上内部都是访问它所在CALayer的相关属性

* UIView和CALayer是相互依赖的关系。UIView依赖CALayer提供的内容，CALayer依赖UIView提供的容器来显示绘制的内容。归根到底CALayer是这一切的基础，如果没有CALayer，UIView自身也不会存在，UIView是一个特殊的CALayer实现，添加了响应事件的能力。

关系：
* UIView对象中的layer指向一个CALayer变量

* UIView对象中的backgroundColor属性，是对CALayer同名属性的封装。

* UIView展示部分是由CALayer中的contents来决定。contents对应的backing store其实是一个bitmap的位图。
区别：
* UIView为其提供内容，以及负责处理触摸等事件，参与响应链。

* CALayer负责显示内容contents。

#### 9.Runloop
[RunLoop](https://blog.ibireme.com/2015/05/18/runloop/)

#### 10.单利模式 
* 单例模式的作用
	- 可以保证在程序运行过程，一个类只有一个实例，而且该实例易于供外界访问
从而方便地控制了实例个数，并节约系统资源
	- 保证一个类仅有一个实例，并提供一个访问它的全局访问点。

* 单例模式的使用场合
	- 在整个应用程序中，共享一份资源（这份资源只需要创建初始化1次），一般用于工具类。例如：登陆控制器，网络数据请求，音乐播放器等一个工程需要使用多次的控制器或方法。

* 单例模式的优缺点
	- 优点：
		- 1.单例模式可以保证系统中一个类只有一个实例而且该实例易于外界访问，从而方便对实例个数的控制并节约系统资源。
		- 2.如果希望在系统中某个类的对象只能存在一个，单例模式是最好的解决方案。
		- 3.单例模式因为类控制了实例化过程，所以类可以更加灵活修改实例化过程。
	- 缺点：
		- 单例对象一旦建立，对象指针是保存在静态区的，单例对象在堆中分配的内存空间，会在应用程序终止后才会被释放。
		- 单例类无法继承，因此很难进行类的扩展。
		- 单例不适用于变化的对象，如果同一类型的对象总是要在不同的用例场景发生变化，单例就会引起数据的错误，不能保存彼此的状态。

#### 11. KVO
* `KVO全称KeyValueObserving`，是苹果提供的一套事件通知机制。允许监听对象特定属性的改变，并在改变时接收到事件。
* 本质：利用runtimeAPI动态生成一个子类，并让instance对象的isa指向这个全新的子类，当修改instance对象的属性时，在全新的子类重写set并调用willChangeValueForKey和didChangeValueForKey并调用内部会触发监听器的监听方法（observerValueForKeyPath:）

* 区别:
    - KVO和NSNotificationCenter都是iOS中观察者模式的一种实现。

    - 相对于被观察者和观察者之间的关系，KVO是一对一的，而NSNotificationCenter一对多的。

    - KVO对被监听对象无侵入性，不需要修改其内部代码即可实现监听。

    - KVO可以监听单个属性的变化，也可以监听集合对象的变化。(通过KVC的mutableArrayValueForKey)
* KVO的触发模式 
    - 在将要观察的对象里添加`+(BOOL)automaticallyNotifiesObserversForKey:(NSString *)key`  `(return YES;//默认，自动模式)  (return NO;//手动模式)`

    - 在属性变化前，调用`willChangeValueForKey`

    - 在属性变化后，调用`didChangeValueForKey`
    
    - 无论属性的值是否发生改变,是否调用Setter方法，只要调用了`willChangeValueForKey`和`didChangeValueForKey`就会触发回调
* KVO原理
    - KVO 底层实现：首先KVO需要创建一个子类(NSKVONotyfing_Class)，这个子类是继承于被观察对象的，这个子类需要重写属性的setter方法，这个时候，外界在调用setter方法的时候，调用的是子类重写的setter方法。就是让外界的Class的对象的isa指针指向这个子类

    - 官文指出KVO的实现，使用了isa-swizzling。当一个object被观察后，该object的 isa 指针将会被修改指向新生成的中间类，而非之前的类；
    ![](https://img2020.cnblogs.com/blog/764024/202004/764024-20200416095844348-793562801.png)

* 注意点
    - 成员变量不能使用KVO，KVO的本质是动态生成一个子类，重写父类的setter方法，实现新值旧值的回调，而成员变量的修改不是setter方法赋值，成员变量没有setter

    - 手动创建`NSKVONotyfing_Class`,KVO不能生效，但是可以编译通过。系统生成的`NSKVONotyfing_Class`是运行时动态创建的

#### 12. KVC
* `KVC(KeyValueCoding)`键值编码，可以动态地访问和修改对象的属性，简单的来说，就是通过对象的属性名（key）给属性赋值，也可以通过属性名（key）获取值。

* 底层的执行机制(赋值操作)
    - 当调用setValue：属性值 forKey：@”name“的代码时，如下：

    - 程序优先调用`set<Key>:`属性值方法，代码通过`setter`方法完成设置

    - 如果没有找到`setName：`方法，KVC机制会检查`+ (BOOL)accessInstanceVariablesDirectly`方法有没有返回YES，默认该方法会返回YES，如果你重写了该方法让其返回NO的话，那么在这一步KVC会执行`setValue：forUndefinedKey：`方法，不过一般开发者不会这么做。所以KVC机制会搜索该类里面有没有名为`_<key>`的成员变量，无论该变量是在类接口处定义，还是在类实现处定义，也无论用了什么样的访问修饰符，只在存在以`_<key>`命名的变量，KVC都可以对该成员变量赋值。

    - 如果该类即没有`set<key>：`方法，也没有`_<key>`成员变量，KVC机制会搜索`_is<Key>`的成员变量。

    - 如果该类即没有`set<Key>：`方法，也没有`_<key>`和`_is<Key>`成员变量，KVC机制再会继续搜索`<key>`和`is<Key>`的成员变量。再给它们赋值。

    - 如果上面列出的方法或者成员变量都不存在，系统将会执行该对象的`setValue：forUndefinedKey：`方法，默认是抛出异常。

* 底层的执行机制(取值操作)
    - 当调用`valueForKey：@”name“`的代码

    - 首先按`get<Key>`,`<key>`,`is<Key>`的顺序方法查找getter方法，找到的话会直接调用。如果是BOOL或者Int等值类型， 会将其包装成一个NSNumber对象。
    
    - 如果上面的`getter`没有找到，KVC则会查找`countOf<Key>`,`objectIn<Key>AtIndex`或`<Key>AtIndexes`格式的方法。如果`countOf<Key>`方法和另外两个方法中的一个被找到，那么就会返回一个可以响应NSArray所有方法的代理集合(它是NSKeyValueArray，是NSArray的子类)，调用这个代理集合的方法，或者说给这个代理集合发送属于NSArray的方法，就会以`countOf<Key>`,`objectIn<Key>AtIndex`或`<Key>AtIndexes`这几个方法组合的形式调用。还有一个可选的`get<Key>:range:`方法。所以你想重新定义KVC的一些功能，你可以添加这些方法，需要注意的是你的方法名要符合KVC的标准命名方法，包括方法签名。

    - 如果上面的方法没有找到，那么会同时查找`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`格式的方法。如果这三个方法都找到，那么就返回一个可以响应NSSet所的方法的代理集合，和上面一样，给这个代理集合发NSSet的消息，就会以`countOf<Key>`，`enumeratorOf<Key>`,`memberOf<Key>`组合的形式调用。

    - 如果还没有找到，再检查类方法`+ (BOOL)accessInstanceVariablesDirectly`,如果返回YES(默认行为)，那么和先前的设值一样，会按`_<key>`,`_is<Key>`,`<key>`,`is<Key>`的顺序搜索成员变量名，这里不推荐这么做，因为这样直接访问实例变量破坏了封装性，使代码更脆弱。如果重写了类方法`+ (BOOL)accessInstanceVariablesDirectly`返回NO的话，那么会直接调用`valueForUndefinedKey`:

    - 还没有找到的话，调用`valueForUndefinedKey`:

* KVC常用场景
    - 运行时动态地取值和赋值
    - 用KVC来访问和修改私有变量
    - Model和字典转换
    - 修改控件的内部属性

#### 13.Category 的实现过程？
* Category，可以动态的为已经存在的类添加新的行为。这样可以保证类的原始设计规模较小，功能增加时再逐步扩展。使用Category对类进行扩展时，不需要访问其源代码，也不需要创建子类。Category使用简单的方式，实现了类的相关方法的模块化，把不同的类方法分配到不同的分类文件中。

* 如果发生方法与原始类重名，则优先加载分类里的方法 `(分类是在main函数之前、初始化runtime库的时候加载的)`

* Category 本质是一个结构体（Category_t），通过runtime与原始类合并，新添加的方法会倒序插入到原始类方法列表的最前面，所以说当出现重名，实际上加载的方法是最后一个被添加的方法,举个例子👇
	```swift
	原方法列表
	[1,2,3,4,5,6] 
	分类新加的方法列表
	[1,1,13,2] 
	倒序插入
	[2,13,1,1,1,2,3,4,5,6] 
	方法查询 查找名为2的方法，那么第一个2就会被加载 所以分类的优先级高
	```
* 不能添加属性变量和实例变量，无法生成`getter`和`setter`，类内存布局在编译时期就确定了，而`CateGory`是在运行时才加载的 。 但是利用`runtime`关联对象可以添加属性变量，在`runtime`中存在一个类型为`AssociationHashMap`的哈希映射表保存着对象动态添加的属性，每个对象以自身地址为`key`维护着一个绑定属性表，我们动态添加的属性就都存储在这个表里

#### 14.Obj-C 中的类信息存放在哪里？

* 类方法存储在元类。
	- 对象方法、属性、成员变量、协议等存放在 Class 对象中。

	- 类方法存放在 meta-class 对象中。
	
	- 成员变量的具体指，存放在 instance 对象中。

#### 15.@autoreleasepool 自动释放池
* 概念：AppKit 和 UIKit 框架在事件循环(RunLoop)的每次循环开始时，在主线程创建一个自动释放池，并在每次循环结束时销毁它，在销毁时释放自动释放池中的所有autorelease对象

* ARC 环境下，autorelease 对象在什么时候释放？
    - 系统干预释放: 由`RunLoop`控制的，会在当前`RunLoop`每次循环结束时释放。
    - 手动干预释: 在`@autoreleasepool`大括号结束时就会释放，不受`RunLoop`控制。

* @autoreleasepool 使用场景 （Apple文档描述）
    - ① 如果你编写的程序不是基于 UI 框架的，比如说命令行工具；
    - ② 如果你编写的循环中创建了大量的临时对象；
你可以在循环内使用`@autoreleasepool`在下一次迭代之前处理这些对象。在循环中使用`@autoreleasepool`有助于减少应用程序的最大内存占用。
    - ③ 如果你创建了辅助线程。
一旦线程开始执行，就必须创建自己的`@autoreleasepool`；否则，你的应用程序将存在内存泄漏。

#### 16. Dealloc 的实现机制
* 1.Dealloc 调用流程

    - 1.首先调用 _objc_rootDealloc()
    - 2.接下来调用 rootDealloc()
    - 3.这时候会判断是否可以被释放，判断的依据主要有5个，判断是否有以上五种情况
        - NONPointer_ISA
        - weakly_reference
        - has_assoc
        - has_cxx_dtor
        - has_sidetable_rc
    - 4-1.如果有以上五中任意一种，将会调用 object_dispose()方法，做下一步的处理。
    - 4-2.如果没有之前五种情况的任意一种，则可以执行释放操作，C函数的 free()。
    - 5.执行完毕。
* 2.object_dispose() 调用流程。

    - 1.直接调用 objc_destructInstance()。
    - 2.之后调用 C函数的 free()。
* 3.objc_destructInstance() 调用流程

    - 1.先判断 hasCxxDtor，如果有 C++ 的相关内容，要调用 object_cxxDestruct() ，销毁 C++ 相关的内容。
    - 2.再判断 hasAssocitatedObjects，如果有的话，要调用 object_remove_associations()，销毁关联对象的一系列操作。
    - 3.然后调用 clearDeallocating()。
    - 4.执行完毕。
* 4.clearDeallocating() 调用流程。

    - 1.先执行 sideTable_clearDellocating()。
    - 2.再执行 weak_clear_no_lock,在这一步骤中，会将指向该对象的弱引用指针置为 nil。
    - 3.接下来执行 table.refcnts.eraser()，从引用计数表中擦除该对象的引用计数。
    - 4.至此为止，Dealloc 的执行流程结束。

#### 17.Runloop 和线程的关系

* 一个线程对应一个 Runloop。

* 主线程的默认就有了 Runloop。

* 子线程的 Runloop 以懒加载的形式创建。

* Runloop 存储在一个全局的可变字典里，线程是 key ，Runloop 是 value。


#### 18.[[WKWebView 那些坑]](https://mp.weixin.qq.com/s/rhYKLIbXOsUJC_n6dt9UfA)
	
#### 19.xib拖出的控件属性为什么要weak修饰？还有Delegate

> 因为会造成循环引用 ，出现释放问题
* ViewController-->View-->subviews-->控件-->ViewController
* ViewController-->TableView-->DataSource-->ViewController

> delegate 
weak：修饰对象只是指明该对象，并不负责保持这个对象，对象的销毁是由外部控制的。

> delegate 
strong：修饰对象是对该对象进行强引用，外界不能销毁该对象，会导致循环引用（Retain Cycles）

#### 20.显示动画和隐式动画的区别？
* 显式动画就是需要我们明确指定类型、时间等参数来实现效果的动画。例如：CABasicAnimation，CAKeyframeAnimation，CATransitionAnimation，CAAnimationGroup
* 隐式动画是指我们可以在不设定任何动画类型的情况下，仅仅改变CALayer的一个可做动画的属性，就能实现动画效果。例如： 改变一个控件的颜色，官方默认有0.25的过渡时间，具体可以查看这个[资料](https://cloud.tencent.com/developer/article/1418000)


#### 21.TCP和UDP的区别于联系
* TCP为传输控制层协议，为面向连接、可靠的、点到点的通信；

* UDP为用户数据报协议，非连接的不可靠的点到多点的通信；

* TCP侧重可靠传输，UDP侧重快速传输。

#### 22.网络七层协议

* 应用层： 1.用户接口、应用程序； 2.Application典型设备：网关； 3.典型协议、标准和应用：TELNET、FTP、HTTP

* 表示层： 1.数据表示、压缩和加密presentation 2.典型设备：网关 3.典型协议、标准和应用：ASCLL、PICT、TIFF、JPEG|MPEG 4.表示层相当于一个东西的表示，表示的一些协议，比如图片、声音和视频MPEG。
* 会话层： 1.会话的建立和结束； 2.典型设备：网关； 3.典型协议、标准和应用：RPC、SQL、NFS、X WINDOWS、ASP
* 传输层： 1.主要功能：端到端控制Transport； 2.典型设备：网关； 3.典型协议、标准和应用：TCP、UDP、SPX
* 网络层： 1.主要功能：路由、寻址Network； 2.典型设备：路由器； 3.典型协议、标准和应用：IP、IPX、APPLETALK、ICMP；
* 数据链路层： 1.主要功能：保证无差错的疏忽链路的data link； 2.典型设备：交换机、网桥、网卡； 3.典型协议、标准和应用：802.2、802.3ATM、HDLC、FRAME RELAY；
* 物理层： 1.主要功能：传输比特流Physical； 2.典型设备：集线器、中继器 3.典型协议、标准和应用：V.35、EIA/TIA-232.

#### 23.Category 为什么不能添加成员变量
Category 不能添加成员变量，但是可以添加属性，但是属性要手动实现setter和getter方法（runtime关联对象）。

* 在Objective-C提供的runtime函数中，确实有一个`lass_addIvar()`
函数用于给类添加成员变量，但是文档中特别说明：`This function may only be called after objc_allocateClassPair and before objc_registerClassPair. Adding an instance variable to an existing class is not supported.`
意思是说，这个函数只能在“构建一个类的过程中”调用。一旦完成类定义，就不能再添加成员变量了。经过编译的类在程序启动后就被runtime加载，没有机会调用addIvar。程序在运行时动态构建的类需要在调用objc_registerClassPair
之后才可以被使用，同样没有机会再添加成员变量。

* runtime头文件中`category_t`中少了 `struct objc_ivar_list * _Nullable ivars`也就是说没有存储ivar数组(成员变量数组)
结合category与原类的结合时机总结：分类并不会改变原有类的内存分布的情况，它是在运行期间决定的，此时内存的分布已经确定，若此时再添加实例会改变内存的分布情况，这对编译性语言是灾难，是不允许的。

* Category为什么能直接添加方法和属性：

    - 因为方法和属性并不“属于”类实例，而成员变量“属于”类实例。我们所说的“类实例”概念，指的是一块内存区域，包含了isa指针和所有的成员变量。所以假如允许动态修改类成员变量布局，已经创建出的类实例就不符合类定义了，变成了无效对象。但方法定义是在objc_class中管理的，不管如何增删类方法，都不影响类实例的内存布局，已经创建出的类实例仍然可正常使用。