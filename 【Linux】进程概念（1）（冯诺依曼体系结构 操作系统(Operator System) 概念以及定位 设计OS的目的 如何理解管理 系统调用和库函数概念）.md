# 前言

# 一、冯诺依曼体系结构


![](https://img-blog.csdnimg.cn/04a43b4362fe4cd9966ceddd7c52e1c5.png)
>**这里的存储器指的是谁？内存，内存这个设备有一个特性叫做掉电易失，和内存本身的结构有关系。**

磁盘：是一种外存，这种设备拥有永久性存储的能力，我们一般创建的文件都在里面存储，它在体系里面属于外设的一种，外设通常被称为输入设备或者输出设备。典型的键盘是输入设备，显示器是输出设备，摄像头在计算机里算输入设备，有很多设备只是输入或者输出，但是还有很多设备既是输入设备又是输出设备，最典型的就是磁盘和网卡。我们用C/C++向磁盘种特定文件当中进行文本或二进制写入，写入的本质就是把我的数据写到对应的外设磁盘上，当然我们也可以把数据从磁盘读到内存，对我们来讲这个这个过程就是数据的读取和写入。


外设是相对于内存和CPU（运算器+控制器+其他 = CPU）说的，CPU这个设备有一个特点，就是快，存储器就是内存，特点是较快。外部设备它的特点是较慢，这些从计算机的角度考量的，可以理解为CPU是按纳秒计时的，内存是按微秒计时，外设是毫秒甚至秒。是数量级上的差距。
CPU是负责计算的，内存是用来存储的，磁盘可以用来永久存储。



---

>**CPU要计算，那它需不需要“数据”？需要**

我们不要以为CPU这个设备很聪明，它只是快，CPU内部做什么计算不是由CPU决定的，是由我们自己写的代码或者操作系统的代码决定的，它是被动执行我们的代码，来为我们做我们想要的计算。“数据”就是我们提供的。CPU必须先认识，识别人的指令，CPU有自己的指令集，CPU内部的指令集是以硬件的方式在内部处理好了，所以我们写代码，编译（形成二进制可执行程序）本质就是把代码翻译成CPU懂的指令集，然后让CPU在内部进行工作，这也是编译器存在的根本原因。

指令周期往CPU上放数据，放代码，也是由硬件驱动去完成的。一旦把CPU放在计算机里那么计算机硬件就不断的给PC指针或者IP寄存器指令，然后CPU就开始跑。

CPU的数据从哪来？从内存里，CPU在读取和写入的时候，在数据层面只和内存打交道，主要是为了提高整机效率，内存里面天然是没有所谓的数据，从根上看数据在磁盘上，CPU需要数据，我们可以在CPU要数据前可以预先把数据从外设搬入内存。比如说：开机的时候，我们在开机的时候把操纵系统加载到内存，就是把数据从磁盘加载到内存，然后让CPU读取。换句话说各忙各的，CPU之和内存打交道，而内存和外设打交道也可以和CPU打交道，中间的内存相当于一个大大的缓存，可以用来适配CPU和外设速度不匹配的问题。

操作系统帮我们各种各样的策略，例如：帮我们把数据从磁盘加载到内存里，加载多少，不要的数据的处理，所以所谓的计算机就是硬件和软件的完美结合。

把外设的数据搬到内存和把内存的数据搬到外设，这个过程我们称为IO的过程，即INPUT/OUTPUT，IO就是存储器和外设之间的工作。



> **结论：在数据层面**
* CPU不和外设直接打交道，只和内存直接打交道。
* 所有的外设，有数据需要载入，只能载入到内存种，内存写出，也一定是写到外设种。

---

有了上面结论看看下面的问题：
>**程序要运行为什么必须先加载到内存？**

因为将来CPU执行我的代码，访问我的数据，只能从内存中读取（体系结构规定），所以必须把数据从磁盘加载到内存当中。体系结构这么规定就是为了提高计算机效率也就是我们上面说的那些。

CPU内部由运算器控制器，控制器就是用来响应外部设备的一些请求的，对数据和设备就绪状态的捕捉。运算器常见的运算有两类，一类就是数学上的算数运算，加减乘除包括判断，还有一类是逻辑运算，例如：逻辑与逻辑或。

如果还想深入了解硬件相关知识，推荐去看书籍 **《深入理解计算机系统》**



>**我和朋友进行QQ聊天，发送了“你好”，“你好”这个数据是如何在不同电脑中流动的？(不考虑网络)**

我的电脑是一台冯诺依曼体系结构的电脑，朋友也是，输入设备就是我的键盘，我按键盘了，数据先存到内存中，我们发的“你好”，这个消息在经过网络发送的时候，需要经过CPU计算（加密算法），写到内存中，然后定期刷新到输出设备，这里的输出设备就是网卡，当然我给朋友发消息，同时我的显示器上也有消息显示，所以就是把数据刷新两份，显示器一份，网卡一份，网卡把数据发送经过网络对应到我朋友的计算机里，一定是她计算机的网卡读到了数据，网卡也一定先把读到的数据拿到内存当中，然后经过朋友电脑的CPU内部解密，写到内存里，变成“你好”，然后刷新到我朋友的输出设备，即显示器。

打开QQ程序并登录，本质就是将QQ程序加载到了内存，CPU在执行QQ程序的代码！（加密算法等等）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20bd829d593641e8affc8c242819e1bf.png)
如果我们把需求换一下，不是发“你好”，而是发文件呢？文件的数据流如何走呢？

先把文件从磁盘搬到内存，说白了读到QQ里，QQ再加密之后把文件写到内存里，再刷新到网卡，网卡传送，对方拿到文件后读到内存里，再解密写到内存，刷新到磁盘上，所以其实就是从磁盘到磁盘。我们就把软件现象从硬件角度理解了。

# 二、操作系统(Operator System)

## 概念以及定位
>**什么是操作系统？**

关于操作系统我们学到的东西只知道一个概念，操作系统本质上是一个进行软硬件资源**管理**的**软件**。


---
## 设计OS的目的

>**为什么要有操作系统？**

操作系统要做的工作很多，主要是对软硬件资源管理，操纵系统要管理硬件要先访问到硬件，包括操作系统内模块， **四大软件管理模块：1.进程管理 2.文件系统 3.内存管理 4.驱动管理**，我们后期重点学习前三种管理模块，操作系统再往上是用户，对我们来讲，操作系统管理好软硬件资源是为了我们用户，操作系统通过合理的管理软硬件资源（手段），为用户提供良好的（稳定的、高效的、安全的）执行环境（目的）。


---

## 如何理解管理



管理者，对重大事宜有决策的权力。决策是要有依据的。管理者不需要和被管理者直接交互，依旧能够把被管理对象管理起来！那如何做到的？管理的本质：是对数据做管理！决策的依据就是从数据来的。管理者和被管理者不直接交互，管理者通过被管理者的数据来进行管理。
管理者如何拿到对应的数据呢？
管理者和被管理者之间有一个执行者，当管理者做好决策之后，执行者执行命令，和被管理者接触，拿到数据交给管理者（根据数据做决策）。


>**操作系统怎么做到管理的？**

在计算机中，操作系统就是一个管理软件，被管理者就是硬件，操作系统管理硬件不需要直接和硬件直接交互，只需要把硬件的数据做管理就好了，通过的就是操作系统和硬件之间的驱动。底层的硬件数据在操作系统的授意之下将数据拿到操作系统内部，然后操作系统进行决策由驱动去执行。硬件都要有对应的驱动程序的。所以我们明白了**操作系统可以通过相应的驱动程序对硬件做管理。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/95fc3e7dfca84f2e93147a4ee31098b1.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/de2a703dad4a44448a6842605c3a1294.png)
## 总结

**所有的“管理”，本质逻辑都是：先描述，再组织。**

操作系统对硬件管理，就是对硬件里的描述对象的结构体做管理或者对数据结构进行管理。
![在这里插入图片描述](https://img-blog.csdnimg.cn/eeefcaf6f4c44a66b9b480e5d77613d8.png)

> **结论：**
> 1.管理的本质：对数据管理。
2.管理的方法：先描述，再组织。


---

![在这里插入图片描述](https://img-blog.csdnimg.cn/2e8e3b16a3194d569a635a5c05e97daf.png)
**我们所有的软硬件都必须通过系统接口访问。**
我们直接使用接口很麻烦，因为调用系统调用接口必须得了解操作系统，不了解操作系统有很多选项是看不懂的，所以对我们来讲很麻烦，所以操作系统往上还有许多的软件层，有一种叫做 `shell`，它是帮助用户来进行基本指令输入输出的，还有一类叫库，`shell`是为了满足用户的指令操作，库是为了完成用户的编程操作。

## 系统调用和库函数概念
* 在开发角度，操作系统对外会表现为一个整体，但是会暴露自己的部分接口，供上层开发使用，这部分由操作系统提供的接口，叫做系统调用。
* 系统调用在使用上，功能比较基础，对用户的要求相对也比较高，所以，有心的开发者可以对部分系统调用进行适度封装，从而形成库，有了库，就很有利于更上层用户或者开发者进行二次开发。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c2311d48a8eb4fcfa48edfec60d62cdc.png)





我们用`printf`向显示器打印，根据冯诺依曼体系结构，显示器是输出设备，我们调用`printf`其实是用的是系统调用，是操作系统帮我们打到显示器上的。只是我们把数据交给了操作系统而已！我们把用C/C++写的代码，程序跑就是在系统调用之上在进行调用，然后再在操作系统内部做。`shell`本身有各种各样的命令，所有的命令或多或少都会访问硬件，比如说`ls` 、`touch`，`touch` 本质就是在磁盘里创建文件，`ls`就是把特定目录下所有的文件（在磁盘上），读到内存里，并显示到显示器上，它从一个输入设备--磁盘，将数据读取到内存然后经过处理，变成输出内容显示到显示器上，这一系列都是硬件操作。
 


![在这里插入图片描述](https://img-blog.csdnimg.cn/9b8aa6868b1d4d47a496384c83f3e741.png)
