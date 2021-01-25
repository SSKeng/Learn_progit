# Git内幕

## 底层命令和高层命令

~~~shell
$ls -F1
HEAD          #指向当前已检出的分支
config        #特定配置项
description   #GitWeb使用，gerrit关联Gitweb
hooks/        #客户端和服务端钩子脚本
info/         #全局性排除文件
objects/      ####个人数据库所有内容####
refs/         #存储指针，指向提交对象
index         #保存暂存区信息

~~~

~~~shell
#将数据内容保存到.git中，并返回键值。
git hash-object 
-w :存储数据对象
--stdin: 从stdin出读取内容

#输出为40个字符的SHA-1散列值
$echo "test content" | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4

#将内容从git取出来，-p内容类型 -t获得Git对象类型
$git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
test content
~~~

## Git对象

相同的内容，输出键值一样。一个文件对应一个键值，不能将多个文件保存在一起，并且一次提交对应一个文件的快照。

将数据内容保存到文件中。数据内容被保存在路径：.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4 

文件内容，这种类型的对象叫做blob对象。

### 树对象

解决存储文件名的问题，还可以将多个文件保存在一起。树对象对应于Unix的目录项，blob对象对应于文件内容。

树对象包含的是指针（指向blob对象或者子树）

~~~shell
$git cat-file -p master^{tree}
100644 blob fjslfdslfjlsfjslkfjldsjflsdfj README
100644 blob jlksdfjoidfjowefjlewkjfoejowf Rakefile
040000 tree jdfkldjfeijfksfklsdjflksdjflk lib   #lib子目录是一个指向其他树对象的指针
$git cat-file -p jdfkldjfeijfksfklsdjflksdjflk
100644 blob fjkslfjiekkjflskjfsl simplegit.rb
~~~

Git 通常会根据暂存区的状态或索引创建一个树对象，并据此生成一系列树对象。

~~~shell
#添加到暂存区，创建索引
#100644 是普通文件 100755：可执行文件； 120000：符号链接
$git update-index --add --cacheinfo 100644 83baae61804e65cc73a7201a7252750c76066a30 test.txt
#将暂存区写成一个树对象
$git write-tree
d8329fc1cc938780ffdd9f94e0d364e0ea74f579
#将树对象读入暂存区
$git read-tree --prefix=bak d8329fc1cc938780ffdd9f94e0d364e0ea74f579
~~~

### 提交对象

用于指定SHA-1值，保存快照，记录保存快照时间，以及为什么保存。

~~~
$echo 'first commit' | git commit-tree d8329f
7ba1604fef6bdd15d7adc1ade0c0039e6dd13671
~~~



