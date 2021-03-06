4.7.2 深入地址翻译

图 4-2 反映了地址的翻译过程。

![](/assets/4-2.jpg)

_**Figure 4-2**.虚拟地址翻译概要_

首先我们从 cr3 中拿到表的起始地址。这张表叫作 **PML4 \(Page Map Level 4\)**。从 PML4 中获取元素遵循下面的流程：

* 51:12 位是由 cr3 提供的
* 11:3 位是虚拟地址的 47:39 位
* 最后三位填零

PML4 表的条目一般被称为 PML4E\(entry\)。下一个从页目录指针表中获取条目的步骤与前面的类似：

* 51:12 位是由选中的 PML4E 提供的
* 11:3 位是虚拟地址的 38:30 位
* 最后三位填零

进程在两张表中遍历，最终我们取到了页帧的地址\(精确地讲，是它的 51:12 位\)。物理地址将使用取到的这些位和直接从虚拟地址中取得的 12 位来拼成完整的地址。

我们需要这么多次的内存读取么？是的，看起来比较笨。然而，感谢页地址缓存，TLB，我们大多数时候访问的都是已经被翻译过的内存地址和已经被记忆的页。我们只需要再在这个基础上加上页内的偏移，加法本身神速。

由于 TLB 是联合缓存；在给定页虚拟地址的前提下，它可以非常快地提供给我们翻译后的页地址。

注意翻译页可能被缓存起来以加速访问。图 4-3 详细地说明了页表条目的格式。

![](/assets/4-3.gif)

_**图 4-3**.Page table entry_

P           在物理内存中

W          可写\(允许写入\)

U           用户级\(可以在 ring3 下访问\)

A           被访问过

D           脏页\(页被载入后源数据被修改了---e.g.例如从磁盘读取的数据\)

EXB      禁止执行位\(Execution-Disabled Bit，在该页上禁止执行指令\)

AVL      可用\(Available，对操作系统开发者来说\)

PCD     页缓存禁用\(Page Cache Disable\)

PWT    通路写入\(Page Write-Through，指写入到页时，直接越过 cache\)

如果 P 没有设置，那么访问该页就会引起一个编码为 \#PF\(Page Fault\) 的中断。操作系统可以处理这个中断，并加载对应的页。这样也可以实现文件和内存映射时的懒加载。文件会在需要的时候才被加载到内存。

操作系统使用 W 这一位来保护页不被修改。如果我们需要在进程间共享代码和数据时就需要这一位，而避免页的拷贝。带有 W 标记的共享页可以用来在进程之间交换数据。

操作系统相关的页会清除 U 位的标记。如果我们从 ring3 下访问清除了 U 标记的页，会引起一个中断。在没有了段保护的情况下，虚拟内存成为了最后的内存防守策略。

---

■On segmentation faults 一般来说，段错误会在尝试访问没有权限的内存时触发\(e.g.写入到 read-only 的内存\)。为了防止访问禁止访问的内存，我们可以把它们当作是没有合法的权限，所以这种 case 就可以认为是权限非法的一个特殊 case。

---

EXB\(也称为 NX\) 位是禁止代码执行的。DEP\(Date Execution Prevention\) 技术就是基于这一位的。当一个程序被执行时，其输入的一部分可以被存在栈上或者数据段中。恶意用户可以通过利用程序的弱点将编码后的指令混杂到输入中，然后执行它们。然而，如果数据和栈段的页被标记上 EXB 的话，没有任何指令可以从其中执行。.text 段则是需要可被执行的，不过这个段一般会被 W 位保护起来，使其不被修改。

