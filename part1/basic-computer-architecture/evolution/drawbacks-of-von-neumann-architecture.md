1.2.1 冯诺伊曼架构的缺陷

前面一小节描述的简单架构有严重的缺陷。

首先，这种架构完全无法进行交互。程序员只能手工进行内存编辑，再以什么形式把内容可视化出来。在计算机发展早期，这种做法还比较直观，因为电路比较巨型，你甚至可以人肉去检查每一个 bit。

此外，这种架构对多任务不友好。想象一下，你的计算机正在执行一个非常慢的任务\(e.g. 控制一个打印机\)。而这种任务之所以慢是因为打印机设备要比计算机最慢的 CPU 还要慢。这种情况下 CPU 就只能等待设备有响应了才能继续工作，可能 99% 的时间都花在了等待上，这是赤裸裸地浪费资源\(CPU 时间\)。

然后，因为任何人可以执行任何指令，任何意料之外的事情都可能发生。操作系统的目的即是为了对资源\(例如外部设备\)进行管理，这样用户程序不会因为并发访问而搅得全局一团乱。也是由此，我们希望能够禁止用户程序执行一些输入输出指令或者系统管理指令。

另外的一个问题是内存和 CPU 在性能上有巨大的鸿沟。

在刚开始的时候，计算机设计比较简单：所有的部件被集成在一起。内存，总线，网络接口---所有的部件都是被同一波工程师团队创造的。每一部分都是为了适应特殊的模型而进行特化开发的。所以部件的设计没有考虑可替换性。这种情况下也没人会尝试去做出一个比其它部件快得多的部件，因为单个部件性能再好，也难以提升整体性能。

但是因为计算机架构多多少少稳定下来了，硬件开发者开始聚焦于计算机的不同部件。工程师为了市场效益自然会努力地提高其部件的性能。然而不是所有的部件想要提升性能都那么容易。这也就是 CPU 比内存快得越来越多的原因。想提升内存的速度也可以选择一些其它的底层元件，但在成本上就不能接受了\[12\]。
