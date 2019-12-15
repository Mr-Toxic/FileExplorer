# FileExplorer
基于 java swing 开发的资源管理器。使用 java swing 美化包beautyeye对原生的 java swing 优化。

#实习内容
0、实现一个文件系统
（1）功能要求 
      a）界面上显示树形目录结构
b）根节点是“我的电脑”
    c）“我的电脑”下有几个盘符（C、D、E等）就有几个子节点，递归显示文件系统下的所有文件信息（分支可以是目录也可以是文件，叶子节点都是文件）
    d)能够创建目录、创建文件、删除目录、删除文件、复制文件、粘贴文件
   
（2）界面要求：界面要友好，让用户操作使用起来非常方便

# ——————————————————————————





『阶段性项目实训一』

T0：实现一个文件系统





—— FileExplorer V2.0

姓名：
班级：
学号：
 

一、实习题目与要求：
实现一个文件系统
（1）功能要求 
    a）界面上显示树形目录结构
b）根节点是“我的电脑”
    c）“我的电脑”下有几个盘符（C、D、E等）就有几个子节点，递归显示文件系统下的所有文件信息（分支可以是目录也可以是文件，叶子节点都是文件）
    d)能够创建目录、创建文件、删除目录、删除文件、复制文件、粘贴文件
   
（2）界面要求：界面要友好，让用户操作使用起来非常方便

二、需求分析
   （0）、一点想法
初拿到这题的时候，都会觉得，这个简单！做过！
但是！想想上学期的java作业我的“FileExplorer V1.0”，用java swing 写的，没什么架构，功能单一，甚至有些功能上还存在着潜在的缺陷。所以这次我打算头开始写。
选择语言方面，由于考虑安全性的JavaScript 已逐渐抛弃的本地文件访问，所以做成静态网页就显得不合乎实际。由于题目要求未“我的电脑”，属于桌面应用程序。所以放弃了Android。剩下三种方式，① python + QT ② C++ 和 QT ③Java 和 Swing。
因为用Java的经验相对丰富，加之之前有用它做过“FileExplorer V1.0”，所以我选择了方式③。

几个方面：
i. 首先是文件结构，改用MVC结构，同样基于java 编程 和Swing GUI。按编程项目规范优化目录结构（ src(m,v,c),  image,  doc,  lib,  … ）
ii. 其次是界面美化，做出“扁平化效果”，以显得更简洁。
iii. 另外，多线程思想。不会使文件复制移动的时候GUI假死，增加进度条显示，窗口多开等等。
vi. 异常处理的完善，和信息校验等，保证程序相对健壮。
v.  尝试使用算法，如递归实现删除文件夹，尝试BFS做一个文件的搜索


（1）、问题描述
  结合题目，类比windows资源管理器，分析主要核心功能有：
		①文件的复制，剪切，粘贴。（多线程）
        ②文件（夹）的删除。（递归算法实现）
		③新建文件，文件夹。
其他功能及扩展功能：
		文件（夹）属性查看；在某一目录下根据文件名查找文件（夹）；程序的“多开”；根据给定的目录跳转到指定文件（夹）；目录的上一页（前进），下一页（后退）；菜单栏的实现；通过键盘的快捷键操作；
（2）、系统环境
由于java是基于JVM的，所以运行无特别系统限制。通用于windows。linux上需要对代码稍加修饰。
（3）、运行要求
JDK 1.8及以上java环境；
依赖的外部包：
		Beautyeye_inf.jar     ——java swing 风格包。

三、软件设计与实现
（1）、数据结构与存储结构的设计
	由于本系统主要是对文件操作，需要的数据即硬盘文件，硬盘文件采用的树状目录结构。所以可以打造一颗“JTree”，用来存储文件结构；通过“JTable”显示文件夹下的所有文件及一些基本信息。
	理论上，可以通过递归的方法将文件初始化进Jtree，但实际操作，会因为硬盘文件内容众多，导致JVM内存不足，或者运行出来异常卡顿，程序容易死掉，无法实现相关功能，所以需要为了更好的效果，需要另寻它法。
	这里我才用的动态加载目录节点的信息。当左侧的JTree的节点被点击时，分析目录结构，更新左下角的信息“该文件夹下有多少子项目”，实现预加载。当结点被展开时，所有子项目被添加到点击的节点上，达到节点的更新。节点折叠时，移除所有子节点，以减少资源占用，同时更方便更新UI。

（2）、算法设计

	①文件的定位。树层序遍历的思想和寻找根节点到叶子节点的思想。首先对需要定位的文件路径进行分析，切割成小块，每一块代表一个节点名称（文件名称），每次遍历一级目录，如果遇到对应的文件，则将该节点展开。依次循环，处理完所有“块”之后，即可定位到目标文件，同时完成根节点到目标文件的动态构建。

	②文件的搜索。搜索有三种方式：一种是仅对当前目录低一级的子项目搜索，时间复杂度O(n)；第二种是对当前文件夹所有子文件进行搜索，需要递归，可以选择BFS，或者DFS的算法实现；第三种是全硬盘下的文件搜索。（二，三方法最好采用多线程，避免搜索过多占用主进程CPU资源，页面假死）

    ③文件的复制（核心）。采用字节流的方式，每次循环：先读入一定数量的文件字节，然后写入另一个文件，之后累加计算已经复制的百分比，更新进度条进swing的EventQueue队列，进入下一次循环。

    多线程调用方式：主进程中再新开一个进程，执行自己写的paste()函数，paste()函数首先开一个创建JProgressBar的窗口Gui进程run0()并通过
SwingUtilities.invokeLater(run0);
将其加入Swing的EventQueue()中并执行。然后创建一个用来更新Ui的进程run()等待调用,然后paste()开始在while(){}中执行文件的读写，每读写一次，执行一次run()，Ui即可更新一次。
	首先在外部定义两个进程。一个是JProgressBar的窗口Gui进程,另一个是copy后台进程。

线程调用关系：
![Image text](https://raw.githubusercontent.com/Mr-Toxic/FileExplorer/master/image-folder/1_thread.png)