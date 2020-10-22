# OC 面试题
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

#### 9.Runloop
[RunLoop](https://blog.ibireme.com/2015/05/18/runloop/)

#### 10. KVO & KVC
* KVO定义
	- KVO 即 Key-Value Observing，翻译成键值观察。它是一种观察者模式的衍生。其基本思想是，对目标对象的某属性添加观察，当该属性发生变化时，通过触发观察者对象实现的KVO接口方法，来自动的通知观察者。

	- （KVO） 观察者模式的一种，
一个目标对象管理所有依赖于它的观察者对象，并在它自身的状态改变时主动通知观察者对象。这个主动通知通常是通过调用各观察者对象所提供的接口方法来实现的。观察者模式较完美地将目标对象与观察者对象解耦。

	- 简单来说KVO可以通过监听key，来获得value的变化，用来在对象之间监听状态变化。KVO的定义都是对NSObject的扩展来实现的，Objective-C中有个显式的NSKeyValueObserving类别名，所以对于所有继承了NSObject的类型，都能使用KVO(一些纯Swift类和结构体是不支持KVC的，因为没有继承NSObject)。

	- KVO 是通过 isa-swizzling 实现的。
基本的流程就是编译器自动为被观察对象创造一个派生类，并将被观察对象的isa 指向这个派生类。如果用户注册了对某此目标对象的某一个属性的观察，那么此派生类会重写这个方法，并在其中添加进行通知的代码。Objective-C 在发送消息的时候，会通过 isa 指针找到当前对象所属的类对象。而类对象中保存着当前对象的实例方法，因此在向此对象发送消息时候，实际上是发送到了派生类对象的方法。由于编译器对派生类的方法进行了 override，并添加了通知代码，因此会向注册的对象发送通知。注意派生类只重写注册了观察者的属性方法。

* KVC定义

	- KVC（Key-value coding）键值编码，就是指iOS的开发中，可以允许开发者通过Key名直接访问对象的属性，或者给对象的属性赋值。而不需要调用明确的存取方法。这样就可以在运行时动态地访问和修改对象的属性。而不是在编译时确定，这也是iOS开发中的黑魔法之一。很多高级的iOS开发技巧都是基于KVC实现的。
	
	- 在实现了访问器方法的类中，使用点语法和KVC访问对象其实差别不大，二者可以任意混用。但是没有访问起方法的类中，点语法无法使用，这时KVC就有优势了。
	- KVC的定义都是对NSObject的扩展来实现的，Objective-C中有个显式的NSKeyValueCoding类别名，所以对于所有继承了NSObject的类型，都能使用KVC(一些纯Swift类和结构体是不支持KVC的，因为没有继承NSObject)，

* KVC 与 KVO 的不同？
	- KVC(键值编码)，即 Key-Value Coding，一个非正式的 Protocol，使用字符串(键)访问一个对象实例变量的机制。而不是通过调用 Setter、Getter 方法等显式的存取方式去访问。
	
	- KVO(键值监听)，即 Key-Value Observing，它提供一种机制,当指定的对象的属性被修改后,对象就会接受到通知，前提是执行了 setter 方法、或者使用了 KVC 赋值。
	
#### 11.单利模式 
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

