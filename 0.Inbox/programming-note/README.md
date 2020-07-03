# 编程的思考

## 简化编程成本提高效率

* DB库提供的relation功能

## 同步还是异步的代码

* `This may be converted to an async function.ts(80006)`
  * 推荐我将`Promise.catch()`转换为`try catch`，`function`转变为`async function`，是否说明同步的代码更加易读？

## 事件机制

* 通常调用事件的地方只完成触发动作，并传递数据
  * 不关心执行什么动作
  * 不关心执行的动作是否成功
  * 可以理解为时数据生产者

* 奇思妙想，触发事件的地方能不能拿到事件的函数体和事件的返回值？
  * 如果事件绑定的函数是单个，几乎等同于直接调用目标函数
  * 如果事件绑定的函数是多个，等同于循环调用这些目标函数，也可以以数组的方式返回这些函数的返回值
  * 结论：是可以拿到返回值的，但是为什么要这么做？可能会用到的

* 奇思妙想，如果只有一个事件消费者，即只有一个事件监听，是否可以不用事件？
  * 触发的事件名称 相当于 预先定义的函数名称，这个常量是不可用轻易更改的
  * 事件回调函数 和 普通的函数体 都是定义在外面的
  * 事件回调函数 的返回值不会被处理，函数体的回调可以被处理
  * 结论： 如果只有一个事件消费者，区别就是是否接受返回值的区别
    * 事件也可以拿到返回值，借助Promise，事件可以拿到异步操作的返回值
    * 触发函数后，我们在函数内部再触发事件也可以达到事件机制的效果，只是调用的时机有所不同，但其实这个时机的差异可以做到很微小
    * 如果事件定义处的代码我们可以自由的修改，那么**触发事件和直接调用函数没有本质区别，只是写法上有所差异**
      * 事件机制的意义是，当如果一个触发的动作，会导致不同的业务操作，则**事件机制提供一种简单的方式将不同业务代码分类的方法**
        * 且**事件机制注册的方法是可以拆卸的**，**动态注册**，**动态调用**
        * 场景：数据层在`batch`被调用时`emit('bacth')`操作
          * 外部定义onBacth时 触发数据保存的动作
          * 外部定义onBacth时 记录batch日志
          * 或者其它
        * 场景：通过配置去关闭某个动作
        * 以上就很有意义，因为**将不相关的代码从数据层解耦了**，且不同业务的代码被分开了
        * 这些也是直接调用函数做不到的
    * 事件通常被强制用于我们只能被动接收的时候，且需要多个回调绑定的时候，如接收用户的输入事件
      * `onClick` 和 `on('click')` 效果是等价的，区别是后者更自由一点，可以绑定多个操作
      * 如果只有一个数据消费者，即只需要一个函数就够了，那么确实没有区别

* 奇思妙想：借助事件机制划分代码
  * 设定好各个实例，实例就是主体、实体
  * 操作一定是对实例的操作，一个实例不应该在内部操作其它的实例，如果有这种需求就应该使用事件机制
  * 假设有一个**事件广场**的存在，实体把自己操作其它实体的需要的数据传递到这个广场，这个广场代为操作
  * 事件机制就是充当一个**事件广场**的角色，各个实例还是只做自己的事情

* 相比直接调用函数，事件机制相当于一种代码组织方式，可以方便的在关键的地方增删代码
* 事件机制有点类似泛型的概念，均是定义时不知道要做什么，调用时才知道做什么。
* 通过明确事件的主体和抽象级别来决定将代码放在哪里会更加合适。
  * 以脑图工具 tab按键操作创建一个节点并建立一个联系的来看，首先要更新数据层，数据层更新成功，才会更新画布， 更新画布的动作是在数据层中做还是应该在快捷键绑定界面做？
    * 放在数据层中做是不合适的，数据层中不应该有直接对画布信息的操作
    * 似乎放在操作监听的地方做比较合适，但是考虑到如果把快捷键理解为对某个动作的触发，我们可以把对画布的操作定义在一个独立的地方，可以未来我们可以调整快捷键，也可以实现对动作的复用。

## 如何保持数据一致性

* 一致性(Consistency) 需求，强调事务执行的前后，数据的状态满足预定的约束。如转账前后，转账双方钱的总数不发生变化。  
  * 具体实现 事务执行完成后需要业务代码验证，然后决定回滚和提交。
* 原子性(Atomicity) 实现，事务是不可分割的工作单位，只有成功和失败状态，没有中间状态。
  * 具体实现 回滚日志(undo log) 记录每一个操作，每一个操作都有一个对应的反操作，如果失败或回滚就从失败处执行反操作。
* 隔离性(Isolation) 实现，事务不可以并发执行，只能顺序执行。
  * 具体实现 锁
  * 具体实现 多版本并发控制(Multi Version Concurrency Control)，提供并发读取的能力，可以把数据划分为事务隔离级别：读已提交(Read Commited)和可重复读(Repeateable Read)，MVCC下有不同表现。
* 持久性(Durability) 实现，事务提交后数据写入持久存储。
  * 具体实现 通过把操作追加写入独立的磁盘文件(redo log，相当于队列)中，避免频繁的磁盘读写。

* 参考 [一文解析：MySQL事务ACID原理让你面试不再害怕](https://zhuanlan.zhihu.com/p/65438009)

## 范式

* [介绍命令式编程](http://codenugget.co/2015/03/05/declarative-vs-imperative-programming-web.html)
  * [login demo](https://jsfiddle.net/b00gizm/f011j2qo/1/)

## 异步操作

* 异步操作的实现方式
  * 回调函数传参
  * 事件监听、管理
  * promise，thenable
  * await/async 推荐的方式
  * co库 使用yield

* 异步操作的实现的共同点
  * 提前注册好函数，延迟执行（在需要的时候调用）

* 异步操作中遇到的问题
  * 由于网络延迟影响，收到数据的次序与发出次序不符

* 回调函数是一种简单、易于理解的实现异步的方法
* 且回调间的嵌套过多导致代码不可读，复用困难
* 回调函数的问题是不知道回调函数什么时候被执行，代码之间有产生了耦合。

## 性能

* 防抖 指定时间内没有调用后开始运行
* 节流 指定时间内多次调用记为一次运行

* 如果从非纯函数的角度理解，**对返回值的要求**和**函数中对外部数据的操作**构成了我们的**需求**。
  * 关于操作的定义。
    * 打印日志，改变外部控制台输出了信息，人也应该理解为是外部的一环
    * 如删除某个外部数组（包括传入参数）的元素

* 从纯函数的角度理解，**对返回值的要求**就是我们的**需求**。
  * **返回值的要求不同说明需求不同**
  * 纯函数不应该有任何对外部数据的操作，即不能有改变传入参数和全局变量的动作。
    * 如果是返回后再将返回值进行打印不属于函数中的操作
  * 纯函数的好处是:**需求是显而易见的**。
  * 纯函数是有局限性的，链式调用是非纯函数的应用。

* 同一个需求可以有不同的实现，不同的实现就会有差异，比如说步骤数量、运行时长
* 同一个实现加以微调和改动，又可以用到不同的需求上，即修改一下返回值
* 需求不同时，实现很可能也是不同的，如果对比不同需求的实现谁运行更快或者更慢，这种比较条件不唯一，是不具有意义的
* 只有在返回值明确的时候才能找到性能最优解，要**结合实际需求出发找到的性能最优解才是有意义的**。