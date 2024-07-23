## 1、Linux基础命令

- alias 给某个命令取别名

  使用 alias 命令来对 ls -la 取别名为cl

  ```
  alias cl='ls - la'
  ```

- date 直接输出日期与时间(设置)

- dpkg 软件包安装

- echo 回显，把你输入的字符输出到终端

```
$ echo "Hello Even"
 Hello Even
```

- sort 对文件的内容进行排序

```
$ sort max.c > sort.txt // > 重定向输出，
 // 把原本输出到标准输入文件的内容重定向到sort.txt文件中
```

- which 查看命令所在的位置

```
$ which cp
 /bin/cp
```

- uniq 去掉文件中重复项然后输出

```
$ uniq max.c
```

- | 管道

  可以用来链接两个命令

  命令1 | 命令2 --> 把命令1 的输出作为命令2的输入

```
$ ls -la | sort // 把 ls -la 的输出作为 sort 的输入
$ sort max.c | uniq // 先对文件进行排序，然后再通过uniq 进行去重复
```

- cat 显示文件的内容
- cp 复制文件
- find 查找文件
- diff 检查文件是否相同

​        该命令可以用来比较文件之间的异同， 并生成差异性文件，作为补丁使用（自行拓展）   

- file 查看文件的格式信息
- tail / head 查看文件的尾部 和 头 部
- wc 计算字符数 单词数 行数

**进程管理命令**

-   ps 获取当前进程状态

| ![image-20240506153323164](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506153323164.png) |
| :----------------------------------------------------------: |

- top 动态进程信息（类似进程管理器）
- kill 向某个进程发送信号

**压缩与解压命令**

tar的基础选项

```
1 -c：创建归档文件。

2 -x：释放归档文件。

3 -t：查看归档文件（或者压缩文件）

4 -f：指定要归档、压缩或者查看的文件的名称。

5 -v：显示命令执行过程。

6 -z: 使用gzip 压缩工具进行响应的压缩/解压

7 -j: 使用bz2 工具进行压缩与解压

8 -J: 解压xz文件可以使用该选项
```

```
$ tar -cf demo.tar a.out demo.c ls.txt max.c min.c sort.txt

 // 把 a.out demo.c ls.txt max.c min.c sort.txt 

 // 整合成 demo.tar 

// -c f 

 $ tar -cvf tar.tar * // 把当前路径下的所有文件进行归档生成为 tar.tar的文件
```

查看归档文件：

```
tar -tf tar.tar // 查看归档文件的内容
```

如何释放归档文件：

```
$ tar -xvf tar.tar -C abc
 // -C 是指定文件释放的路径
 // abc 就是当前目录下的abc目录
```

如何压缩与解压gzip格式

```
1 $ tar -czvf demo.tar.gz * // 把当前目录的所有文件进行归档并压缩为 demo.tar.gz 

2 $ tar -xzvf demo.tar.gz -C ~ // 把demo.tar.gz 进行解压到 ~ 目录中
```

如何压缩与解压bzip2格式：

```
1 $ tar -cjvf demo.tar.bz2 * // 把所有的文件使用bz2格式进行压缩

2 $ tar -xjvf demo.tar.bz2 -C ~ // 把demo.tar.bz2 进行解压到 ~ 目录中
```

## 2、库文件

库文件是计算机上的一类文件，提供给使用者一些开箱即用的变量、函数或类。库文件分为静态库和动态库

静态库和动态库的区别体现在程序的链接阶段：

- 静态库在程序的链接阶段被复制到了程序中；
- 动态库在链接阶段没有被复制到程序中，而是程序在运行时由系统动态加载到内存中供程序调用。

使用动态库系统只需载入一次，不同的程序可以得到内存中相同的动态库的副本，因此节省了很多内存，而且使用动态库也便于模块化更新程序。

库文件的命名：

- 必须使用lib作为前缀： 比如 libDeployPkg.so.0 / libhgfs.so.0 .....
- 静态库一般以 .a 为后缀 ， 动态库一般以.so为后缀
- 库文件会有不同的版本， 一般写在后缀后面， 比如 lib.a.so.0.1.2

| <img src="https://img-blog.csdnimg.cn/img_convert/28eff739eb4938752d6c2c6c643e0378.png" alt="img" style="zoom:80%;" /> |
| :----------------------------------------------------------: |

[【Linux】第六篇：静动态库文件的理解及实现_libc.so.6是什么库-CSDN博客](https://blog.csdn.net/qq_43041053/article/details/124417109#:~:text=1 静态库（.a）：程序在编译链接的时候把库的代码直接装载到可执行文件中。 程序运行的时候将不再需要第三方库。,打个比方：静态库如同私家车，停放的位置随意，用户可直接使用，缺点在于去哪儿都得带着这辆车，本体移动显得笨重。 2 动态库（.so）：程序在运行的时候才去链接动态库的代码，多个程序共享使用库的代码，依赖第三方库。 打个比方：动态库好比公交车，你只能去到固定的地方（公交车站台）才能使用（调用），而且可以多人共享，到达目的地后便可下车，不用去哪都带着这辆公交车，可做到本体轻量化，缺点在于一旦没有了这辆公交车，或者找不到公交站台，你什么事都办不了。)

## 3、MakeFile

[Makefile教程（绝对经典，所有问题看这一篇足够了）-CSDN博客](https://blog.csdn.net/weixin_38391755/article/details/80380786)

$@，$^，$<代表的意义分别是： 

- ​          他们三个是十分重要的三个变量，所代表的含义分别是：
- ​          $@--目标文件，$^--所有的依赖文件，$<--第一个依赖文件。

Makefile里主要包含了**五个东西**：显式规则、隐晦规则、变量定义、文件指示和注释。

1. 显式规则。显式规则说明了，如何生成一个或多的的目标文件。这是由Makefile的书写者明显指出，要生成的文件，文件的依赖文件，生成的命令。
2. 隐晦规则。由于我们的make有自动推导的功能，所以隐晦的规则可以让我们比较粗糙地简略地书写Makefile，这是由make所支持的。
3. 变量的定义。在Makefile中我们要定义一系列的变量，变量一般都是字符串，这个有点你C语言中的宏，当Makefile被执行时，其中的变量都会被扩展到相应的引用位置上。
4. 文件指示。其包括了三个部分，一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一样；还有就是定义一个多行的命令。有关这一部分的内容，我会在后续的部分中讲述。
5.  注释。Makefile中只有行注释，和UNIX的Shell脚本一样，其注释是用“#”字符，这个就像C/C++中的“//”一样。如果你要在你的Makefile中使用“#”字符，可以用反斜框进行转义，如：“\#”。

最后，还值得一提的是，在Makefile中的命令，必须要以[Tab]键开始。

| ![image-20240506155712638](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506155712638.png) |
| ------------------------------------------------------------ |

```
target = ./main
# target1 = ./arm-main
source = ./*.c
config = -lpthread -ljson-c -I /usr/local/include/json-c -Wall #-L /usr/local/lib

$(target):$(source)
	gcc $(source) -o $(target) $(config)
	# arm-linux-gcc $(source) -o $(target1) $(config)

clean:
	rm ./main
	# rm ./arm-main
```

$$
公式
				
					
				
				
						
				
			
				$$
					
				
				
				 xxxxxxxxxx M x¨ +H=u
				$$
$$

