@[TOC](Linux指令)

# 问题
1. 查看进程运行状态的指令、查看内存使用情况的指令、tar解压文件的参数（后面部分有详细介绍）
	>①`ps -l | grep PID`用来查看某PID进程的状态
②`free -m`查看内存使用情况
③以下五个命令中必选一个：     
`-c`: 建立压缩档案      &emsp;`-x`：解压       &emsp;`-t`：查看内容       &emsp;`-r`：向压缩归档文件末尾追加文件       &emsp;`-u`：更新原压缩包中的文件 
以下几个参数是可选的：      
`-z`：有gzip属性的       &emsp;`-j`：有bz2属性的       &emsp;`-Z`：有compress属性的      &emsp;`-v`：显示所有过程       &emsp;`-O`：将文件解开到标准输出
补充：查看进程运行状态、查看内存使用情况的指令均可以使用`top`指令

<p>

2. 修改文件权限
~~*Linux是多用户系统，文件权限是防止系统被恶意篡改的途径之一。*~~ 
Linux文件的基本权限有9个，分别是owner/group/others三种身份各自有自己的read/write/execute权限（读、写与执行）。
	
	可以用`ls -l 文件`来查看该文件所属情况，例：`-rw-r--r-- 1 root root 73 Mar  3 20:11 hello.c`给出了hello.c文件的创建者，创建者归属的用户组，相应的还列出了文件大小、创建日期和时间、文件名。

	修改权限的指令：`chmod`
	每一个权限设置都可以用数值表示：**r = 4, w = 2, x = 1**，当这些值加在一起，总数就是用于指定的权限。例：设置用户可以读写，用户组只读，其他只读，则权限设置就是644。
	```html
	root@ubuntu:~# ls -l hello.c
	-rw-r--r-- 1 root root 73 Mar  3 20:11 hello.c
	root@ubuntu:~# chmod 644 hello.c
	root@ubuntu:~# ls -l hello.c
	-rw-r--r-- 1 root root 73 Mar  3 20:11 hello.c
	```
	可以看到同组的用户被限制掉写的权限。
	补充：若使用`chmod -R xyz`，-R会进行递归的持续变更，即同此目录下的所有文件都会变更

<p>

3. 常用的Linux命令
	1. `cd`命令：用于切换当前目录
	2. `ls`命令：查看当前文件与目录
	3. `grep`命令：常用于分析一行的信息，若当中有我们需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工。
	4. `cp`命令：复制命令（后面部分有详细介绍）
	5. `mv`命令：移动文件或文件夹命令
	6. `rm命令`：删除文件或文件夹命令
	7. `ps命令`：查看进程情况
	8. `kill命令`：向进程发送终止信号
	9. `tar命令 `：对文件进行打包，调用gzip或bzip对文件进行压缩或解压
	10. `cat命令`：查看文件内容，与less、more功能相似
	11. `top命令`：查看操作系统的信息，如进程、CPU占有率、内存信息等
	12. `pwd命令`：用于显示工作目录

<p>

4. 如何以root权限运行某个程序
	`sudo chown root app(文件名)` 输入密码
	`sudo chmod u+s app(文件名)`
	输入上面两条指令后即可。

<p>

5. 软链接与硬链接的区别
从几个方面作答。
	1. 定义不同
	软链接又叫符号链接，这个文件包含了另一个文件的路径名。可以是任意文件或目录，可以链接不同文件系统的文件。
	硬链接就是一个文件的一个或多个文件名。把文件名和计算机文件系统使用的节点号链接起来。因此我们可以用多个文件名与同一个文件链接，这些文件名可以在同一目录或者不同目录。
	2. 限制不同
	硬链接只对已存在的文件进行创建，不能交叉文件系统进行硬链接的创建；
	软链接可对不存在的文件或目录创建软链接，可交叉文件系统；
	3. 创建方式不同
	硬链接不能对目录进行创建，只可对文件创建；
	软链接可对文件或目录创建；
	4. 影响不同
		删除一个硬链接并不影响其他有相同inode的文件。
		删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软链接被称为死链接（即dangling link，若被指向路径文件被重新创建，死链接可恢复成正常的软链接）。
		
<p>

6. 静态库和动态库怎么制作及使用，它们的区别
 	1. 静态库的生成：
		`gcc -c hello.c`先 生成.o文件
 		`ar -cr libhello.a hello.o`创建libhello.a静态库

		静态库的使用：
		`gcc main.c -lhello -o sttaticLibrary`
		main.c和libhello.a静态库链接，生成target执行文件；
		`-lhello`是我们生成的.a文件砍头去尾的写法，-L是指告诉gcc编译器先从-L指定的路径去找静态库，默认是从/usr/lib/或者/usr/local/lib/去找；
		staticLibrary是最后想生成的文件名
		
		
 	2. 生成动态库的指令：
		`gcc -shared -fPIC -o libhello.so hello.o`
		`-shared`：制定生成动态库
		`-fPIC`：该选项作用于编译阶段，在生成目标文件时就得使用该选项，以生成位置无关代码
 		
 		动态库的使用：
 		`gcc  main.c -lhello -L ./ -o dynamicDepot`
 		`-lhello`是我们生成的.so文件砍头去尾的写法，-L是指告诉gcc编译器先从-L指定的路径去找动态库，默认是从/usr/lib/或者/usr/local/lib/去找；
 		./ 是当前路径的意思
 		dynamicDepot是最后想生成的文件名

	3. 区别
		1. 速度：静态库代码装载的速度快，执行速度略比动态库快
		2. 内存：动态库更加节省内存，可执行文件体积比静态库小很多
		3. 加载时期：静态库在编译时加载，动态库在运行时加载
		4. 后缀名：生成的静态库链接，在Windows下以.lib为后缀，Linux下以.a为后缀；生成的动态库链接，在Windows下以.dll为后缀，Linux下以.so为后缀。

<p>
 		
 7. GDB常见的调试命令，什么是条件断点，多进程下如何调试
	1. GDB调试：gdb调试的是可执行文件，在gcc编译时家人如-g，告诉gcc在编译时加入调试信息，这样、gdb才能调试这个被编译的文件`gcc -g test.c -o test`（后面部分有详细介绍）
	`gdb test.out`进入gdb调试
 	GDB命令格式：
 		1. `quit`：退出gdb，结束调试
	 	2. `list`/`l`：查看程序源代码
		 		`list 5, 10`：显示5-10行代码
		 		`list test.c:5, 10`：显示源文件5-10行的代码，在调试多个文件时使用
		 		`list get_sum`：显示get_sum函数周围的代码
				`list test.c gei_sum`：显示源文件test.c的get_sum函数周围的代码，调试多个文件时使用
		3. `reverse-search`：字符串用来从当前行向前查找第一个匹配的字符串
		4. `run`/`start`：程序开始执行
		5. `help list/all`：查看帮助信息
		6. `break`/`b 行号`：设置断点，一开始进入调试，初始只有一个断点在main函数
		7. `watch条件表达式`：条件表达式发生改变时程序就会停下来
		8. `next`/`n`：继续执行下一条语句，会把函数当做一条语句执行
		9. `step`：继续执行下一条语句，会跟踪进入函数，一次一条地执行函数内代码
		[linux下gdb调试常用命令汇总](https://blog.csdn.net/qq_30911665/article/details/72957386)
	
	2. 条件断点：break if条件，以条件表达式设置断点
	3. 多进程下如何调试：用set follow-fork-mode child调试子进程
		&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;或者set follow-fork-mode parent父进程
	
<p>

8. 什么是大端小端，如何判断大小端
*数据在内存中存放，有大端模式和小端模式。*
 小端模式（little-endian）：**低的有效字节存储在低的存储器地址**。小端一般为主机字节序，常用的x86结构是小端模式，很多架构如ARM、DSP都为小端模式。
 大端模式（big-endian）：**高的有效字节存储在低的存储器地址**。大端为网络字节序，KEIL C51就是大端模式。
 补充：有些ARM处理器可由硬件决定选择大端模式还是小端模式。

	判断的话，之前的一篇文章里有整理过三种。
 	1. 使用联合体进行判断
		①联合体的各个成员都是公用内存
		②联合体的成员不管是在哪个架构上默认都是从低地址存储的，这点非常重要。
	```cpp
	union endian{
		char a;
		int b;
	}test;
	
	int main()
	{
		test.b = 0x87654321;
		printf("0x%p\n", test.a );
		return 0;
	}
	```
	可以判断出所用的架构是小端的。因为test.a是联合体在内存中的第一个字节（就是在内存地址中的最低的地址），打印出了0x21说明数据的逻辑低位存储在了内存地址的低位，这就是小端了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2e13b04feb5d431e8271395de4621fdf.png)
	2. 利用指针进行判断
	```cpp
	static int m = 0x9876543210;
	char *p = (char*) &m;
	
	void test(){   
	    printf("P  :0x%p: %x\n", p, *p);
	    printf("P+1:0x%p: %x\n", p+1, *(p+1));
	    printf("P+2:0x%p: %x\n", p+2, *(p+2));
	    printf("P+3:0x%p: %x\n", p+3, *(p+3));
	}
	```
		低字节10放在低地址，高字节76放在高地址，与小端模式相符。
	![在这里插入图片描述](https://img-blog.csdnimg.cn/cd26e992b93d45db963cca81447e3dea.png)
	
	3. 强制类型转换
	```cpp
		static int m = 0x9876543210;
		char p = (char)m;
		 
		void test(){   
			printf("P: 0x%p\n", p);
		}
	```
	![在这里插入图片描述](https://img-blog.csdnimg.cn/d4613885c28840d2935f4833f2b56168.png)
	
	补充介绍：
		1. 在进行网络通信时是否需要进行字节序转换？
		相同字节序的平台在进行网络通信时可以不进行字节序转换，但是跨平台进行网络数据通信时必须进行字节序转换。
		原因如下：网络协议规定接受得到的第一个字节是高字节，存放到低地址，所以发送时会先首先去低地址取数据的高字节。小端模式的多字节数据在存放时，低地址存放的是低字节，发送方网络协议函数发送时会首先去低地址取数据（想要获取高字节但真正取得的是低字节），接收方网络协议模式接收时会将接收到的第一个字节存放到低地址（想接收高字节但真正接收的是低字节），所以最后双方都正确的收发了数据。在相同平台进行通信时，如果双方都进行了转换，虽然最终能够正确的收发数据，但是所做的转换是没有意义的，造成了资源的浪费。而不同平台进行通信时必须进行转换，不转换会造成错误的收发数据，==字节序转换函数==会根据当前平台的存储模式做出正确的转换，如果当前平台是大端，则直接返回不进行转换，如果当前平台是小端，会将接收得到的网络字节序进行转换。
		2. 网络字节序
		网络上传输的数据都是字节流，对于一个多字节数值，发送端在进行网络传输的时候，会遇到先传递哪个字节的问题；接收端在接收第一个字节时，会遇到要将这个字节作为高位字节处理还是低位字节处理的问题。TCP/IP协议规定：把接收到的第一个字节当作高位字节看待，这就要求发送端发送的第一个字节是高位字节；而在发送端发送数据时，发送的第一个字节是该数值在内存中的起始地址处对应的那个字节，也就是说，多字节数值在发送前，在内存中应该是以大端模式存放的。所以说，**网络字节序是大端字节序**。比如：我们经过网络发送整型数据0x12345678时，在x86平台中，它是以小端模式发送存放的，在发送之前需要使用系统提供的字节序转换函数htonl()将其转换成大端模式存放的数值。

<p>

9. 进程调度算法有哪些
	1. 答案：
		1. 先来先服务调度算法
		2. 短作业（进程）优先调度算法
		3. 高优先级 优先调度算法
		4. 时间片轮转法
		5. 多级反馈队列调度算法

	2. 补充：
		1. 先来先服务调度算法：每次调度都是从后备作业（进程）队列中选择一个或者多个最先进入该队列的作业（进程），将它们调入内存，为它们分配资源、创建进程，然后放入就绪队列。
		2. 短作业（进程）优先调度算法：短作业优先（SJF）的调度算法是从后备队列中选择一个或者若干个估计运行时间最短的作业（进程），把它们调入内存运行。
		3. 高优先级 优先调度算法：当把该算法用于作业调度时，系统将从后备队列中选择若干个优先级最高的作业装入内存。当用于进程调度时，该算法是把处理机分配给就绪队列中优先级最高的进程。
		4. 时间片轮转法：系统将所有的就绪进程按先来先服务算法的原则，排成一个队列。每次调度时，把CPU分配给队首进程，并令其执行一个时间片。时间片的大小从几ms到几百ms。当执行的时间片用完时，由一个计时器发出时钟中断请求，调度进程便据此信号来停止该进程的执行，并将它送往就绪队列的末尾。然后，再把处理机分配给就绪队列中新的队首进程，同时也让它执行一个时间片。（平均周转时间、平均带权周转时间）
		5. 多级反馈队列调度算法：综合前面多种调度算法。
	
	3. 拓展
	在这些调度算法中，有抢占式和非抢占式的区别。
		1. 非抢占式优先权算法
		在这种方式下，系统一旦把处理机分配给就绪队列中优先权最高的进程后，该进程便一直执行下去，直至完成；或因发生某事件使该进程放弃处理机时，系统方可再将处理机重新分配给另一优先权最高的进程。这种调度算法主要用于批处理系统中；也可用于某些对实时性要求不严的实时系统中。
		2. 抢占式优先权调度算法
		在这种方式下，系统同样是把处理机分配给优先权最高的进程，使之执行。但在其执行期间，只要又出现了另一个其优先权更高的进程，进程调度程序就立即停止当前进程(原优先权最高的进程)的执行，重新将处理机分配给新到的优先权最高的进程。因此，在采用这种调度算法时，是每当系统中出现一个新的就绪进程 i 时，就将其优先权 Pi 与正在执行的进程j 的优先权 Pj 进行比较。如果Pi ≤ Pj，原进程Pj便继续执行；但如果是Pi > Pj，则立即停止Pj的执行，做进程切换，使 i 进程投入执行。显然，这种抢占式的优先权调度算法能更好地满足紧迫作业的要求，故而常用于要求比较严格的实时系统中，以及对性能要求较高的批处理和分时系统中。
		3. 区别
		非抢占式（Non-preemptive）：让进程进行知道结束或阻塞的调度方式，容易实现，适合专用系统，不适合通用系统。
		抢占式（Preemptive）：允许将逻辑上可继续运行的进程 在运行过程中暂停的调度方式，可防止单一进程长时间独占，CPU系统开销大（降低途径：硬件实现进程切换或者扩充主存以贮存大部分程序）。

<p>

10. 简述操作系统如何申请以及管理内存
	1. 操作系统如何管理内存：
		1. 物理内存：物理内存有四个层次：寄存器、高速缓冲、主存、磁盘。
			寄存器：速度最快，量少，价格贵
			高速缓冲：次之
			主存：再次之
			磁盘：速度最慢，量多，价格便宜
		操作系统会对物理内存进行管理，有一个部分称为**内存管理器（memory manager）**，它的主要工作是有效的管理内存，记录哪些内存正在使用，在进程需要的时候分配内存以及在进程完成时回收内存。
		2. 虚拟内存：操作系统为每一个进程分配一个独立的地址空间 ，但是是虚拟内存。虚拟内存与物理内存之间存在映射关系，通过页表寻址完成虚拟内存和物理地址的转换。
	
	2. 操作系统如何申请内存
	从操作系统角度看，进程分配内存有两种方式，分别由两个系统调用完成：**brk**和**mmap** 

<p>

11. 简述Linux系统态与用户态，什么时候会进入系统态
	1. 内核态（系统态）与用户态：是操作系统的两种运行级别。内核态拥有最高权限，可以访问所有系统指令；用户态只能访问一部分指令。
	2. 什么时候进入内核态：共有三种方式：**a. 系统调用&emsp;b. 异常&emsp; c. 设备中断**。其中，系统调用是主动的，另外两种是被动的。
	3. 为什么区分报内核态和用户态：在CPU的所有指令中，有一些指令是非常危险的，如果错用，将导致整个系统崩溃。比如：清内存、设置时钟等。所以区分内核态和用户态是出于安全考虑的。

<p>

12. 简述LRU（最近最久未使用）置换算法及其实现方式
	1. LRU算法：LRU算法用于缓存淘汰，思路是将缓存中最近最少使用的对象删除掉。
	2. 实现方式：利用链表和hashmap
		当需要插入新的数据项的时候，如果新数据项在链表中存在（一般称为命中），则把该节点移到链表头部，如果不存在，则新建一个节点，放到链表头部，若缓存满了，则把链表最后一个节点删除即可。
		在访问数据的时候，如果数据项在链表中存在，则把该节点移到链表头部，否则返回-1。这样一来在链表尾部的节点就是最近最久未访问的数据项。
 	给出C++实现的代码

	```cpp
		class LRUCache{
			list<pair<int, int>> cache;//创建双向链表 
			unordered_map<int, list<pair<int, int>>::iterator> map;//创建哈希表
			int cap;//cache的最大容量
			
		public:
			LRUCache(int capcity):cap(capcity){
				
			} 
			
			int get(int key){//获取键值key所对应的val值 
				if(map.count(key) > 0){//存在，继续下一步 
					//auto temp = *map[key];
					pair<int, int>temp = *map[key];
					cache.erase(map[key]);//删除表中原有的节点 
					map.erase(key); 
					cache.push_front(temp);//将节点插入到链表头部 
					map[key] = cache.begin();//映射头部
					return temp.second; //返回键key对应的val值 
				}
				return -1;
			}
			
			void put(int key, int value){
				if(map.count(key) > 0){//存在，则删除旧节点 
					cache.erase(map[key]);
					map.erase(key);
				}
				else if(cap == cache.size()){//如果cache已达最大容量 
					auto temp = cache.back();//记录最后一个节点 
					map.erase(temp.first);//删除map中映射到该数据的键 
					cache.pop_back();//删除链表的最后一个位置 
				}
				cache.push_front(pair<int, int>(key, value));//插入新的节点到头部 
				map[key] = cache.begin();//映射头部，新建映射 
			}
		};
	```

<p>

13. 一个线程占多大的内存
	一个linux的线程大概占8M的内存。
	解析：Linux的栈是通过缺页来分配内存的，不是所有栈地址空间都分配了内存。因此，8M是最大的消耗，实际内存消耗只会略大于实际需要的内存（内部损耗，每个在4k以内）。

14. 什么是页表，以及为什么要有页表
	页表是虚拟内存的概念。操作系统 虚拟内存到物理内存的映射表，就被称为页表。
	原因：不可能每个虚拟内存的字节都对应到物理内存的地址，这张表将大得真正的物理地址也放不下，于是操作系统引入了页（Page）的概念。进行分页，这样可以减少虚拟内存页对应物理内存页的映射表的大小。
	
	解析：如果将每一个虚拟内存的字节都对应到物理内存的地址，每个条目最少需要8字节（32位虚拟地址->32位物理地址），在4G内存的情况下，就需要32GB的空间来存放对照表，这样这张表就大得真正的物理地址也放不下了，于是操作系统引入了页的概念。
	在系统启动时，操作系统将整个物理内存以4k为单位，划分为各个页。之后进行内存分配时，都以页为单位，那么虚拟内存页对应物理内存页的映射表就大大减小了，4G内存只需要8M的映射表即可，一些进程没有使用到的虚拟内存，也并不需要保存映射关系，而且Linux还为大内存设计了多级页表，可以进一步减少内存消耗。
	
<p>

15. 简述操作系统中的缺页中断
	1. 缺页异常：malloc和mmap函数在分配内存时只是建立了进程虚拟地址空间，并没有分配虚拟内存对应的物理内存。当进程访问这些没有建立映射关系的虚拟内存时，处理器自动触发一个 **缺页异常，引发缺页中断**。
	2. 缺页中断：缺页异常后将产生一个缺页中断，此时操作系统会根据页表的**外存地址**在外存中找到所缺的那一页，将其调入**内存**。
	
		补充：缺页中断和一般中断一样，需要经历四个步骤：保护CPU现场、分析中断原因、转入缺页中断处理程序、恢复CPU现场，继续执行。
		缺页中断和一般中断的区别：①在指令执行期间产生和处理缺页中断信号&emsp;②一条指令在执行期间，可能发生多次 缺页中断&emsp;③缺页中断返回的是执行产生中断的指令，而一般中断返回的是执行下一条指令。

<p>

16. 简述虚拟内存分布，什么时候会由用户态陷入内核态
	1. 虚拟内存分布：			![在这里插入图片描述](https://img-blog.csdnimg.cn/dbcc4694ba7e4a36a78038f028a1449e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAZXJjaV9mYzIzMzY=,size_20,color_FFFFFF,t_70,g_se,x_16)
	用户空间：
	（1）**代码段.text**：存放程序执行代码的一块内存区域。只读，代码段的头部还会包含一些只读的常数变量，如字符串常量等。
	（2）**数据段.data**：存放程序中已初始化的全局变量和静态变量的一块内存区域。
	（3）**BSS段.bss**：存放程序中未初始化的和置零的全局变量和静态变量的一块内存区域。
	（4）可执行程序在运行过程中又会多出两个区域：**堆和栈**。
			&emsp;&emsp;堆区：动态申请内存。堆从低地址向高地址增长。
			&emsp;&emsp;栈区：存储局部变量，函数参数值。栈从高地址向低地址增长，是一块连续的空间。
	（5）共享区，位于堆和栈中间。
	
	2. 什么时候进入内核态：一共有三种方式：a. 系统调用 &emsp;b. 异常 &emsp; c.设备中断。其中，系统调用是主动的，另外两种是被动的。

<p>

17. 简述以下虚拟内存和物理内存，为什么要用虚拟内存，虚拟内存的优缺点
	1. 物理内存：物理内存有四个层次，分别是寄存器、高速缓冲、主存、磁盘。
		操作系统会对物理内存进行管理，有一个部分称为**内存管理器（memory manager）**，它的主要工作是有效的管理内存，记录哪些内存是正在使用的，在进程需要的时候分配内存以及在进程完成后回收内存。
	2. 虚拟内存：操作系统为每一个进程分配一个独立的地址空间，但是是虚拟内存。虚拟内存和物理内存存在映射关系，通过页表寻址完成虚拟地址和物理地址的转换。
	3.  为什么要使用虚拟内存：因为早期的内存分配方法存在以下问题：
		（1）进程地址空间不隔离，会导致数据被随意修改
		（2）内存使用效率低
		（3）程序运行的地址不确定，操作系统随机为进程分配内存空间，所以程序运行的地址是不确定的
	4. 使用虚拟内存的好处：
		（1）扩大地址空间。每个进程独占一个4G空间，虽然真实物理没那么多
		（2）内存保护：防止不同进程对物理内存的争夺，可以对特定内存地址提供写保护，防止恶意篡改
		（3）可以实现内存共享，方便进程通信
		（4）可以避免内存碎片，虽然物理内存可能不连续，但映射到虚拟内存可以是连续的
		5. 使用虚拟内存的缺点：
		（1）虚拟内存需要额外构建数据结构，占用空间
		（2）虚拟内存到物理内存的转换，增加了执行时间
		（3）页面换入换出耗时
		（4）一页如果只有一部分数据，浪费内存

<p>

18. 虚拟内存到物理内存的映射
	**操作系统为每一个进程维护了一个虚拟地址到物理地址的映射关系的数据结构，叫页表**。页表中的每一项都记录了这个页的基地址。
	**三级页表转换方法**：（两步）
		1. 逻辑地址转线性地址：段起始地址 + 段内偏移地址 = 线性地址
		2. 线性地址转物理地址：
			（1）每一个32位线性地址被划分为三部分：页目录索引（DIRECTROY，10位）、页表索引（TABLE，10位）、页内偏移（OFFSET，12位）
			（2）从**cr3**中取出进程的页目录地址（操作系统调用进程时，这个地址被装入寄存器中）
				页目录地址 + 页目录索引 = 页表地址
				页表地址 + 页表索引 = 页地址
				页地址 + 页内偏移 = 物理地址
						![在这里插入图片描述](https://img-blog.csdnimg.cn/63641656f2d24b1bb6e5b43a55b13e54.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAZXJjaV9mYzIzMzY=,size_18,color_FFFFFF,t_70,g_se,x_16)
			按照以上两步法，就完成了一个三级页表从虚拟地址向物理地址的转换

<p>

19. 说说堆栈溢出是什么，会怎么样
	堆栈溢出就是不顾堆栈中分配的局部数据块大小，向该数据块写入了过多的数据，导致数组越界。常指调用堆栈溢出，本质上是一种数据结构的满溢情况。堆栈溢出可以分为两个方面：堆溢出和栈溢出。
	1. 堆溢出：比如不断new一个对象，一直创建新对象而不进行释放，最终导致内存不足。将会报错：OutOfMemory Error。
	2. 栈溢出：一次函数调用中，栈中将被依次压入：参数，返回地址等，而方法如果递归比较深或者陷入死循环，就会导致栈溢出。将会报错：StackOverflow Error。

<p>

20. 简述操作系统中mmalloc的实现原理
malloc底层实现：当开辟的空间小于128k时，调用brk()函数；当开辟的空间大于128k时，调用mmap()。malloc采用的是内存池的管理方式，以减少内存碎片。先申请大内存作为堆区，然后将堆区分为多个内存块。当用户申请内存时，直接从堆区分配一块合适的空闲块。采用隐式链表将所有空闲块连接，每一个空闲块记录了一个未分配的、连续的内存地址。






<p>
<p>

# 补充
## tar命令详解
[tar命令的详细解释](https://blog.csdn.net/eroswang/article/details/5555415)
参数：
- c ：建立一个压缩文件的参数指令(create 的意思)；
- x ：解开一个压缩文件的参数指令！
- t ：查看 tarfile 里面的文件！
**特别注意，在参数的下达中， c/x/t 仅能存在一个！不可同时存在！**
因为不可能同时压缩与解压缩。
- z ：是否同时具有 gzip 的属性？亦即是否需要用 gzip 压缩？
- j ：是否同时具有 bzip2 的属性？亦即是否需要用 bzip2 压缩？
- v ：压缩的过程中显示文件！这个常用，但不建议用在背景执行过程！
- f ：使用档名，请留意，在 f 之后要立即接档名，不要再加参数！例如使用`tar -zcvfP tfile sfile`就是错误写法，正确的写法是`tar -zcvPf tfile sfile`
- p ：使用原文件的原来属性（属性不会依据使用者而变）
- P ：可以使用绝对路径来压缩
- N ：比后面接的日期(yyyy/mm/dd)还要新的才会被打包进新建的文件中
例：
> 仅打包，不压缩：`tar -cvf end.tar /end`
> 打包后，以gzip压缩：`tar -zcvf end.tar.gz /end`
> 打包后，以bz2压缩：`tar -jcvf end.tar.bz2 /end`
> &emsp;*以上指令在执行的时候会显示一个警告信息：# tar: Removing leading `/' from member names，这是关于绝对路径的特使设定。*
> 
> 查阅end.tar.gz包内有哪些文件：`tar -ztvf  end.tar.gz`
> 将压缩文档在指定位置解压：先来到指定目录下：`cd /root/end`，再解压`tar -zxvf /root/end.tar.gz`
> 只解开压缩包里的部分文件：`tar -zxvf /root/end.tar.gz root/end/hello.c`，注意这句命令里有很多小细节，比如'/'。
> 保留原文件的属性：加上'p'：`tar -zxvf end.tar.gz /root/end`
> 只备份比2022/3/2新的文件`tar -N '2022/03/02' -zcvf end.tar.gz /root/end`

<p>
<p>

## cp命令详解
[cp命令](https://blog.csdn.net/weixin_42832313/article/details/94389698)
cp命令的基本格式：`cp [选项] 源文件 目标文件`
选项：
`-a`：相当于-d、-p、-r选项的集合
`-d`：如果源文件为软链接（对硬链接无效），则复制出的目标文件也为软链接
`-i`：询问，如果目标文件已经存在，则会询问是否覆盖
`-l`：把目标文件建立为源文件的硬链接文件，而不是复制源文件
`-s`：把目标文件建立为源文件的软链接文件，而不是复制源文件
`-p`：复制后目标文件保留源文件的属性（包括所有者、所属组、权限和时间）~~有时候这些文件的时间可能是一个重要参数，需要保留。~~ 
`-r`：递归复制，用于复制目录
`-u`：若目标文件比源文件有差异，则使用该选项可以更新目标文件，此选项可用于对文件的升级和备用

- 对软链接、硬链接的简单解释：软链接像快捷方式，方便我们打开源文件，这一点在windows中深有体会，而在Linux（多用户的操作系统）里，用户写一个脚本、程序等，没有完成，保存后等下次有时间继续写，但是其他用户有可能将其未写完的东西当成垃圾清理掉，这时对这个程序做一个硬链接，利用硬链接的同步更新，就可以防止别人误删源文件了。硬链接就等于cp拷贝加同步更新。

例：
> 如果需要改名复制则：`cp 源文件 /目录/新文件名`
> 复制目录：`cp -r /源目录/ /目标目录/`
补充：cp命令默认执行的是`cp -i`的别名，如果复制的目标位置已经存在同名的文件，则会提示是否覆盖。

<p>
<p>
<p>

# 基础指令

## Linux下gcc编译生成.out，.o，.a，.so文件
[参考链接](https://blog.csdn.net/zyhse/article/details/105228959)

### 文件类型关联介绍
.out是可执行文件，相当于win上的exe
.o是编译中间目标文件，相当于win上的.obj
.a是静态库，多个.o链接得到，用于静态链接
.so是共享库，用于动态链接，相当于win上的,dll

### .out可执行文件的生成
编译cpp：`gcc hello.c -o a.out`
执行.a.out：`./a.out`

### .o目标文件生成
生成.o文件：`gcc -c hello.c`
得到hello.o，因为是中间文件，所以无法执行

### 静态库生成
1. 创建静态库
由.o文件创建静态库，静态库文件名的命名规范是以lib为前缀，紧接着跟静态库名，扩展名为.a
例：将创建的静态库命名为hello，则静态库文件名就是libhello.a
生成.a文件：`ar -cr libhello.a hello.o`
结果可以由命令`ll`查看，找到libhello.a
2. 调用静态库

```cpp
#include <stdio.h>
void hello();
int main()
{
    hello();
    return 0;
}
```
编译main.cpp，静态链接libhello.a
`gcc main.cpp libhello.a -o main.out`

### so共享库生成
1. 创建so
由.o文件创建动态库文件，动态库文件名命名规范和静态库文件名命名规范类似，也是在动态库名字前加前缀lib，但其文件扩展名为.so
例：创建动态库名为hello，则动态库文件名就是libhello.so
生成so文件：
- 生成hello.o时，要使用-fPIC选项：
`gcc -c -fPIC hello.c`
- 再执行生成so命令：
`gcc -shared -fPIC -o libhello.so hello.o`
结果可以由命令`ll`查看，找到libhello.so
2. 调用so
- 先将文件libhello.so移动到目录/usr/lib中

- 编译main.c：`gcc -o main.out main.c -L -lhello`
- 运行main.out：`./main.out`

<p>

## 批量移动目录下所有文件到指定目录
两种方法：
1. 批量拷贝到指定目录，再删除目标文件
`cp -r /root/start/ /root/end/`
`rm -rf /root/start/`

2. 压缩文件，再移动后解压
`cd /root/start/`
`zip -r start.zip *`
`mv start.zip /root/end/`
`unzip start.zip`
`rm -rf /root/start/`
