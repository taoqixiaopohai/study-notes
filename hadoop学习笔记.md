# 1 hadoop两大核心：分布式存储、分布式计算；

# 2 hadoop安装使用
## 2.1 创建hadoop用户
+ su              # 以 root 用户登录
+ useradd -m hadoop -s /bin/bash   # 创建新用户hadoop，并使用 /bin/bash 作为shell
+ passwd hadoop	# 为hadoop用户创建密码
+ visudo		# 为 hadoop 用户增加管理员权限: 找到 root ALL=(ALL) ALL 这行,然后在这行下面增加一行内容：hadoop ALL=(ALL) ALL

## 2.2 设置hadoop用户无密码登录
+ su hadoop	# 切换至hadoop用户
+ cd ~/.ssh/      # 若没有该目录，请先执行一次ssh localhost
+ ssh-keygen -t rsa	# 会有提示，都按回车就可以
+ cat id_rsa.pub >> authorized_keys	# 加入授权
+ chmod 600 ./authorized_keys	# 修改文件权限

## 2.3 安装JDK 配置JAVA环境
+ 安装JDK的步骤省略	# 下载、解压
+ su hadoop	# 切换至hadoop用户
+ vim ~/.bashrc	# 配置java环境：在文件最后面添加如下单独一行
export  JAVA_HOME=jdk安装路径
+ source ~/.bashrc    # 使变量设置生效

## 2.4 安装hadoop
+ 下载hadoop压缩包
+ sudo tar -zxf hadoop压缩包路径 -C /usr/local    # 解压到/usr/local中
+ cd /usr/local/
+ mv ./hadoop-3.1.2/ ./hadoop            # 将文件夹名改为hadoop
+ sudo chown -R hadoop:hadoop ./hadoop        # 修改文件权限

## 2.5 hadoop伪分布式配置
&#8195;&#8195; 注：Hadoop 可以在单节点上以伪分布式的方式运行，Hadoop 进程以分离的 Java 进程来运行，节点既作为 NameNode 也作为 DataNode，同时，读取的是 HDFS 中的文件。

### 2.5.1 设置 HADOOP 环境变量
+ su hadoop	# 切换至hadoop用户
+ vim ~/.bashrc	# 配置hadoop环境变量，在文件的最后追加以下内容：
```
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```
+ source ~/.bashrc	# 使设置的环境变量立即生效

### 2.5.2修改配置文件core-site.xml
+ vim /usr/local/hadoop/etc/hadoop/core-site.xml
+ 在`<configuration>`标签中插入以下内容：
```
<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/usr/local/hadoop/tmp</value>
    <description>Abase for other temporary directories.</description>
</property>
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
</property>
```

### 2.5.3 修改配置文件hdfs-site.xml
+ vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml
+ 在`<configuration>`标签中插入以下内容：
```
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/tmp/dfs/name</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/tmp/dfs/data</value>
</property>
```

### 2.5.4 执行 NameNode 的格式化
+ cd /usr/local/hadoop
+ ./bin/hdfs namenode -format

&#8195;&#8195; 成功的话，会看到 “successfully formatted” 和 “Exitting with status 0” 的提示，若为 “Exitting with status 1” 则是出错

### 2.5.4 开启 NaneNode 和 DataNode 守护进程
+ cd /usr/local/hadoop
+ ./sbin/start-dfs.sh

&#8195;&#8195; 若出现如下 SSH 的提示 “Are you sure you want to continue connecting”，输入 yes 即可;

&#8195;&#8195; 启动完成后，可以通过命令 jps 来判断是否成功启动，若成功启动则会列出如下进程: “NameNode”、”DataNode”和SecondaryNameNode
