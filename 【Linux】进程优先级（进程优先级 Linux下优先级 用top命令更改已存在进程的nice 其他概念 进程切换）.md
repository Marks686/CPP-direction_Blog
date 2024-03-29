---

# 进程优先级

我们作为使用者一般不关心优先级，它跟我们的调度器有很大的关系，调度器是为了跟均衡的调度进程。


> 什么叫做优先级？


优先级和权限是两码事，权限决定的是能还是不能的问题，优先级前提是已经能了，决定的是能的时候先做还是后做的问题。
**优先级就是先还是后获得某种资源的能力，先获得优先级高。**




> 为什么存在优先级？


因为资源太少了。系统可能存在几十个进程，但CPU只有一个，系统可能存在十几二十几个进程都要访问网卡，但是网卡只有一个。所以在任何系统里硬件资源一定是少数，软件资源要用硬件资源的跟多，所以要争，即确认优先级，排队。



# Linux下优先级

> Linux优先级的特点：


优先级本质就是PCB里面的一个整数数字（也可能是几个），在Linux中是用两个整数来确定优先级的，这两个整数叫`PRI`和`NI`。

首先写一个进程：


![在这里插入图片描述](https://img-blog.csdnimg.cn/0ee58b8cdde5404b984779cd443ba921.png)



用`ps -la`就可以查到我这个用户所能查到的指令

![在这里插入图片描述](https://img-blog.csdnimg.cn/14bc2122ee8a41b39ebeaa9b8b3c1ebe.png)


Linux支持进程运行中，进行优先级调整，调整的策略就是更改nice完成的。
我们可以看到Linux下的优先级是80，那么它一般的范围是多少呢？优先级受nice值的影响，换句话说nice值的范围决定最终优先级的范围。
所以我们运行一个进程，然后去调整它的优先级，下面试着调一下：

## 用top命令更改已存在进程的nice：

先改一下代码，并运行，
![在这里插入图片描述](https://img-blog.csdnimg.cn/d77e96b802e147e2b0aa30891b9a325f.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/826a79d22186462e804c3b3a867599a8.png)


第一步先top，
![在这里插入图片描述](https://img-blog.csdnimg.cn/0c463c8ea1514af4b83f3c6dccac98e0.png)





然后输入r，r就是rnice的意思，rnice就是重新设置，默认的进程的pid就是29788 ，我要改的是21837，

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e3a306812ca44eaa345486edf4f916e.png)

所以就输入21837，
![在这里插入图片描述](https://img-blog.csdnimg.cn/ebd1d1a48f7b4acda3d90f1150e2106b.png)

回车后发生改变：

![在这里插入图片描述](https://img-blog.csdnimg.cn/ef7a93b44fef4d39a87b5a14f0583109.png)

就变成了 `Renice PID 21837 to value`，所以我们此时就相当于调整了nice值，一般在优先级当中，优先级的PRI数字越小，代表优先级程度越高。
此时给nice值改成 -100：
![在这里插入图片描述](https://img-blog.csdnimg.cn/79d866a111514d789eb51a5faccdaaa9.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/cf3f1502940348a5aac408da1d56ac6a.png)




设置的时候，调优先级本来就会影响调度器的效率，所以在设置的时候可能要进行权限的设置。所以我们sudo top：

再去操作，`Renice PID 21837 to value -100`,然后查看进程优先级：

![在这里插入图片描述](https://img-blog.csdnimg.cn/b31b2103142344b9a44591c1eed98b2e.png)

PRI优先级变成了60，优先级被调高了。



我们继续操作：`Renice PID 21837 to value 100`

![在这里插入图片描述](https://img-blog.csdnimg.cn/e59d60eae59d4f8b8e5c8bd0f5b30d3e.png)
此时nice值变成了19。得到下结论：nice的范围


**`nice [-20,19]`**
所以我们的nice值有上下限。

继续操作：`Renice PID 21837 to value 100`

![在这里插入图片描述](https://img-blog.csdnimg.cn/87cbb3271c2a491ca7ebe89d7bbcba09.png)

PRI的优先级为89，如果是按照 最终优先级 = 老的优先级 + nice就发现出现了问题，按理优先级应该为 99 + 19 ，但是结果是89，那么就得出另一个结论：**每一次设置的时候，老的优先级都是从80开始的。这样就更好的限制了最终优先级一定在 [80-20, 80+19]** ，不会出现奇怪的情况。


#  其他概念

> 竞争性：资源有限必须确定优先级。





> 独立性：多进程运行，多进程运行期间互不干扰，各进程独享自己的资源。

修改代码：
![在这里插入图片描述](https://img-blog.csdnimg.cn/99bf6b22934245ee978a0b9c80f1b3e7.png)

刚开始子进程肯定是在跑的，跑一会就退了，

![在这里插入图片描述](https://img-blog.csdnimg.cn/2b82a0e0956b4ab49ec3fda65ea2c3f1.png)

发现子进程退出了， 变僵尸了，但是父进程还在跑，这就证明进程具有独立性。代价就是花费跟多的资源。

> 并行：

对我们来讲，我们的笔记本一般就一个CPU，不考虑多核，当我们只有一个CPU的时候，任何时刻我们只能有**一个进程正在运行**。有的大的云服务器，双CPU，那么意味着它在任何一个时刻，它可以有两个进程同时运行，这种同时在运行的进程我们称为并行。

> 并发：

![在这里插入图片描述](https://img-blog.csdnimg.cn/46eff678de9247fbadc8b3ebfea239ba.png)

那我们发现我们计算机好像有多个进程在跑，只有一个CPU的时候，这个进程不是在CPU跑完了才能被从CPU拿下来，当代计算机采用的是时间片轮转的策略，也就是说，我们不管进程未来执行完花多少时间，我们只给这一次10ms的时间让进程占有CPU，那么时间到了，进程就必须从CPU剥离下来，要么等吧，要么就去进程队列尾部重新排队。**多进程在同一个CPU下我们采用进程不断切换的方式**，那么就可以让一个系统内的一个单CPU的计算机，采用进程切换的方式**在一个时间段内，同时让多个进程代码可以推进的行为叫做并发**。

# 进程切换

进程在切换的时候，可以让多个代码同时推进，那么如何切换呢？


一个CPU内部存在大量的寄存器，虽然寄存器很多，但是只有一套硬件寄存器，这些寄存器里有用户可见和用户不可见的（状态寄存器、权限寄存器），当计算机调度某个进程的时候，CPU把这个进程的PCB的地址加载进寄存器，CPU里的寄存器就能找到进程的PCB也就能找到代码和数据。pc/eip （指令计数器）是一个关键的寄存器。对我们来讲CPU一直做着三件事情：**1.取指令 2.分型指令 3.执行指令**，这里的指令就是我们代码中的指令，所以eip中就保存着当前正在执行指令的下一条指令的地址，相当于CPU里面有一个专门用来标识下一次从当前进程什么位置去读去对应代码或指令的寄存器。读进去之后会参与计算，计算的时候也有一些寄存器参与计算（eb、ec、ed、ex），当我们的进程在运行的时候一定会产生非常多的临时数据，这份数据属于当前进程。
CPU内部虽然只有一套寄存器硬件，但是，**寄存器里面保存的数据，是属于当前进程的。**

**寄存器硬件 != 寄存器内的数据**


![在这里插入图片描述](https://img-blog.csdnimg.cn/ea7b70292d9b45ecac5f405cfbff9274.png)
进程在运行的时候占有CPU，但是进程不是一直要占有到进程结束。例如：`while(1)`。进程在运行的时候都有自己的时间片。因为进程有时间片，所以每个进程在单CPU上跑的时候，有可能出现进程没跑完就被拿下去，当一个进程没跑完被拿下去了，肯定要考虑下次被拿回来。
举个在校生当兵的例子：
离开学校，保留学籍，上下文保护
回到学校，回复血迹，上下文恢复
为什么要保留和恢复？

保留的手段，恢复是目的。所以，完成保留和恢复，就相当于进程完成了一次切换。学校就是CPU，我就是进程，那么部队就相当于某种等待队列，所以当进程从CPU被剥离的时候，首先要做的就是**保留CPU的内寄存器的数据**，注意不是寄存器，而是寄存器里的数据。

**进程在切换的时候要进行进程的上下文保护（CPU的内寄存器的数据），当进程在恢复运行的时候，要进行上下文的恢复。**


在任何时刻，CPU里面的寄存器里面的数据，看起来是在大家都能看到的寄存器上，但是，寄存器内的数据，只属于当前运行的进程！

寄存器被所有进程共享，寄存器内的数据，是每一个进程各自私有的------上下文数据。
