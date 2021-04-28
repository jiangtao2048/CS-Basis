# Git

## 集中式与分布式

* ##### 集中式版本控制系统（Centralized Version Control Systems，简称 CVCS）

  有一个单一的集中管理服务器，保存所有文件的修订版本，协同工作的人们通过客户端连接到这台服务器，取出服务器提交更新。例如`SVN`

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426112500.png" style="width:500px"/>

* ##### 分布式版本控制系统（Distributed Version Control System，简称 DVCS）

  在这类系统中如`Git`，客户端并不只是提取最新版本的文件快照，而是把代码仓库完整的镜像下来，包括完整的历史记录。这样服务器发生故障时可以用任何一个镜像出来的本地仓库恢复。每一次克隆操作都是对代码仓库的完整备份。

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426113309.png" style="width:500px"/>

## Git原理

* ##### git采用直接记录快照，而非差异比较

  git和其它版本控制工具的主要差别在于git对待数据的方式。其它大部分系统以`文件变更列表`的方式存储信息，这类系统（如CVS）将它们存储的信息看作是`一组基本文件`和每个文件随时间`逐步累积的差异`（称为基于差异的版本控制）。

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426143229.png" style="width:700px"/>

  git把数据看作是对小型文件系统的一系列`快照`，每当你提交更新或保存项目状态时，git会对全部文件创建一个快照并保存这个快照索引，*未更改的文件会链接到上一次的快照*，git是通过快照流对版本迭代。

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426150802.png" style="width:700px"/>

* ##### 理解git中的快照

  git中快照类似于备份，但这个备份不是简单的复制粘贴，git会进行`处理压缩`，git能使用这个快照恢复原来的状态。git会根据当前内容生成一个`校验和`，将该`校验和`作为索引。每次提交检测到校验和变化，就会生成一个新的快照，未更改的文件则会链接到上一次的快照，这样就形成了一条链（在没有其它分支的情况）。git有一个`head指针`，这个指针可以移动，移动到哪个快照，就可以查看该快照当时的状态。

  

  首先这个快照并不是这次commit时项目中所有文件的拷贝，而是一个索引，通过该索引可以找到这次commit时的所有文件。git在每次commit时对所有文件进行扫描，如果某一个文件发生变化，git会对应生成一个新blob文件，该文件记录commit时发生变化文件的内容，文件名是对文件内容一次SHA-1运算得到的40位字符串。而`快照`就是`记录所有这次提交commit时所有blob文件名SHA-1 hash字符串集合`，因此就可以通过快照得到所有blob文件名从而找到所有文件，加载所有文件后就还原到了之前commit的项目状态。

  

  例如有三个版本，版本2改动了文件A，版本3改动了文件B

  

  **A、B、A1、B1可以理解位blob文件**

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426152909.png" style="width:500px"/>

  <img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426153003.png" style="width:500px"/>

  随着版本的迭代产生的blob文件越来越多，占用的内存会比较大，git权衡时间和空间利用率会进行优化存储，保存当前最新版本的整个文件，时间久远或者不经常使用的版本只保存`diff`，这样就达到了存储空间和读取加载速度的平衡。

## git工作流程

##### 工作区与版本库

新建一个仓库后，当前目录就成了`工作区`，工作区下有一个隐藏目录`.git`，它是`Git版本库`。Gti版本库分为`Stage暂存区`以及`History版本库`，History版本库存储所有分支信息，使用一个`HEAD指针`指向当前分支。

<img src="https://cdn.jsdelivr.net/gh/jiangtao2048/picture-bed/java-basis/20210426162743.png" style="width:700px"/>

```markdown
git add <files>  把文件的修改添加到暂存区
git commit 把暂存区的修改提交到当前分支，提交后暂存区就被清空了
git reset --<files> 使用当前分支上的修改覆盖暂存区，用来撤销最后一次git add files
git checkout --<files> 使用暂存区的修改覆盖工作目录，yong'lai
```









