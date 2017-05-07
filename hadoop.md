# [Go to home page] [1]
<center>2017-5-2</center><br>

## [简单的的vim命令](http://www.cnblogs.com/jeakon/archive/2012/08/13/2816802.html)
## [linux常用命令]（http://www.weixuehao.com/archives/25）
## [linux命令查找网站](http://man.linuxde.net/)

# CentOS 下安装hadoop<br>

## 一、安装Vmware 12
> 1. 官网下载[VMware-Workstation-Full-*.bundle][2]
> 2. sudo ssh./VMware-Workstation-Full-*.bundle
> 3. 破解：破解工具[VMware12.Keymaker][3] 
> 4. 根据提示安装

## 二、安装CentOS 7
> 1. 下载[镜像][4]
> 2. 新建虚拟机，根据提示操作（注意选择安装GNOME桌面），设置主机名为CentOSMaster点击安装
> 3. 设置root密码和添加hadoop用户（设置为管理员）
> 4. 等待安装，完成后重启，连接网络，完成配置 
> 5. 语言选择汉语（pinyin）

## 三、 安装hadoop集群
#### 参考教程：
#### 单机/伪分布式：<http://www.powerxing.com/install-hadoop-in-centos/>
#### 分布式集群：<http://www.powerxing.com/install-hadoop-cluster/>

>### 1. 创建hadoop用户(如果没有)

```markdown
1. su                               # 上述提到的以 root 用户登录
2. useradd -m hadoop -s /bin/bash   # 创建新用户hadoop
3. passwd hadoop                    #设置密码
4. visudo                           #增加管理员权限
```
找到 root  ALL=(ALL)  ALL 这行,下一行增加:hadoop ensp; ensp; ALL=(ALL)  ensp;ensp; ALL<br>


>### 2. 安装Java环境(在hadoop用户下)

1. 安装openjdk<br>
```markdown
    sudo yum install java-1.7.0-openjdk java-1.7.0-openjdk-devel
```
2. 配置JAVA_HOME<br>
```markdown
    vim ~/.bashrc
```
    在文件最后面添加如下单独一行（指向 JDK 的安装位置)<br>
```markdown
    export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk
```
3. 使配置生效<br>
```markdown
    source ~/.bashrc
```
4. 检验是否配置成功<br>
```markdown
   echo $JAVA_HOME  #检验变量值
   java -version 
   %JAVA_HOME/bin/java -version
```
如果java -version 和 %JAVA_HOME/bin/java -version一样表示成功,否则看5<br><br>
5. 如果和以前的jdk版本冲突的:<br>
    查找当前的安装的jdk版本<br>
```markdown
    rpm -q |grep java
```  
    删除openjdk版本意外的版本<br>
```markdown
    rpm -e --nodeps java版本的名称
```  

>### 3.安装配置hadoop2集群

1. 下载hadoop压缩包，选择[hadoop-2.x.y.tar.gz][5]文件,这里我选择的是2.6.1版本<br><br>
2. 解压<br>
```markdown
    sudo tar -zxf ~/下载/hadoop-2.6.1.tar.gz -C /usr/local    # 解压到/usr/local中
    cd /usr/local/  #打开/usr/local目录
    sudo mv ./hadoop-2.6.1/ ./hadoop  # 将文件夹名改为hadoop
    sudo chown -R hadoop:hadoop ./hadoop  # 修改文件权限，冒号后没有空格
```
4. 显示版本<br>
```markdown
    cd  /usr/local/hadoop
    ./bin/hadoop version
```
5. 配置环境变量<br>
```markdown
    gedit ~/.bashrc (vim ~/.bashrc)
```
    在文件中添加：<br>
```markdown
    #Hadoop Environment Variables
    export HADOOP_HOME=/usr/local/hadoop
    export HADOOP_INSTALL=$HADOOP_HOME
    export HADOOP_MAPRED_HOME=$HADOOP_HOME
    export HADOOP_COMMON_HOME=$HADOOP_HOME
    export HADOOP_HDFS_HOME=$HADOOP_HOME
    export YARN_HOME=$HADOOP_HOME
    export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
    export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```
6. 使配置生效<br>
```markdown
    source ~/.bashrc
    hadoop version #验证
```
7. 关闭虚拟机，克隆两个虚拟机，命名为CentOSSlave1,CentOSSlave2,注意要选择完整克隆<br><br>
8. 依次打开CentOSMaster,CentOSSlave1,CentOSSlave2,查看各自的ip,(ens**下的inet内容)<br>
```markdown
    ifconfig
```   
9. 修改各自的主机名(Matser，Slave1，Slave2)：<br>
在master节点上：<br>
```markdown
    sudo hostnamectl  set-hostname Master
```
在Slave1节点上：<br>
```markdown
    sudo hostnamectl  set-hostname Slave1
```
在Slave2节点上：<br>
```markdown
    sudo hostnamectl  set-hostname Slave2
```
10. 修改ip映射（三个节点都要修改）：<br>
```markdown
    sudo vim /etc/hosts
```
    在末尾加上:<br>
```markdown
   ip1   Master
   ip2   Slave1
   ip3   Slave2
```
11. 设置开机启动网络<br>
    修改 /etc/sysconfig/network-scripts/ifcfg-ens*（具体文件名每个人有可能不同）,将最后一行的ONBOOT 改为yes    
```markdown
    vim  /etc/sysconfig/network-scripts/ifcfg-ens33  #我的文件名称为ifcfg-ens33
``` 
12. 通过在终端分别执行ping Master，ping Slave1，ping Slave2,看是否能通，ctrl+C停止<br><br>
13. 主节点Master使用ssh无密钥登陆节点（注意ssh登陆的用户名）<br><br>
    a. 首先生成 Master 节点的公匙，在 Master 节点的终端中执行：<br>
```markdown
       su hadoop               #登陆到hadoop用户,所有操作都使hadoop用户的行为
       cd ~/.ssh               # 如果没有该目录，先执行一次ssh Master
       rm ./id_rsa*            # 删除之前生成的公匙（如果有）
       ssh-keygen -t rsa       # 一直按回车就可以
```
    b. 让Master节点需能无密码ssh本机，在 Master 节点上执行：<br>
```markdown
       cat ./id_rsa.pub >> ./authorized_keys
       chmod 600 ./authorized_keys    # 修改文件权限
```
      完成后可执行 ssh Master 验证一下（可能需要输入 yes，成功后执行 exit 返回原来的终端）。<br><br>
    c. 将上公匙传输到 Slave1 节点(Slave2也是一样操作将Slave1改成Slave2):<br>
```markdown
       scp ~/.ssh/id_rsa.pub hadoop@Slave1:/home/hadoop/<br><br>
``` 
    d. 在Slave1和Slave2节点上 操作：<br>
```markdown
       mkdir ~/.ssh       # 如果不存在该文件夹需先创建，若已存在则忽略
       cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
       rm ~/id_rsa.pub    # 用完就可以删掉了
````
    e. 在Master节点上ssh Slave1和Slave2，验证是否能连接上<br><br>
```markdown
    ssh Slave1
    
    ssh Slave2
```
14. 在Master节点上操作，cd /usr/local/hadoop/etc/hadoop,进入root模式<br>
    a. 修改slaves文件,将localhost注释，添加Slave1,换行，Slave2<br><br>
```markdown
    vim slaves
```
    b. 修改core-site.xml<br>
    ```markdown
    <configuration>
           <property>
                <name>fs.defaultFS</name>
                <value>hdfs://Master:9000</value>
            </property>
            <property>
                <name>hadoop.tmp.dir</name>
                <value>file:/usr/local/hadoop/tmp</value>
                <description>tmp directories</description>
            </property>
    </configuration>
    ```
    c. 修改hdfs-site.xml,其中的dfs.replication的value根据Slave的个数填写<br>
    ```html
     <configuration>
            <property>
                    <name>dfs.namenode.secondary.http-address</name>
                    <value>Master:50090</value>
            </property>
            <property>
                    <name>dfs.replication</name>
                    <value>2</value>
            </property>
            <property>
                    <name>dfs.namenode.name.dir</name>
                    <value>file:/usr/local/hadoop/tmp/dfs/name</value>
            </property>
            <property>
                    <name>dfs.datanode.data.dir</name>
                    <value>file:/usr/local/hadoop/tmp/dfs/data</value>
            </property>
      </configuration>
    ````
    d. 重命名 mapred-site.xml.template为mapred-site.xml,并修改mapred-site.xml为：<br>
    ```html
    <configuration>
            <property>
                    <name>mapreduce.framework.name</name>
                    <value>yarn</value>
            </property>
            <property>
                    <name>mapreduce.jobhistory.address</name>
                    <value>Master:10020</value>
            </property>
            <property>
                    <name>mapreduce.jobhistory.webapp.address</name>
                    <value>Master:19888</value>
            </property>
    </configuration>
    ````
    e. 修改yarn.site.xml为：<br>
    ```html
    <configuration>
            <property>
                    <name>yarn.resourcemanager.hostname</name>
                    <value>Master</value>
            </property>
            <property>
                    <name>yarn.nodemanager.aux-services</name>
                    <value>mapreduce_shuffle</value>
            </property>
    </configuration>
    ```
15. 配置好后,将Master上的/usr/local/hadoop文件夹复制到各个节点上。如果有临时文件和日志文件先删除,在Master节点上执行:<br>
```markdown
    cd /usr/local
    sudo rm -r ./hadoop/tmp                    # 删除 Hadoop 临时文件
    sudo rm -r ./hadoop/logs/*                 # 删除日志文件
    tar -zcf ~/hadoop.master.tar.gz ./hadoop   # 先压缩再复制
    cd ~
    scp ./hadoop.master.tar.gz Slave1:/home/hadoop
```
    如果有其他节点再执行：scp ./hadoop.master.tar.gz Slave(n):/home/hadoop<br><br>
16. 分别在slave节点上执行<br>
```markdown
    sudo rm -r /usr/local/hadoop    # 删掉旧的（如果存在）
    sudo tar -zxf ~/hadoop.master.tar.gz -C /usr/local
    sudo chown -R hadoop /usr/local/hadoop   #给hadoop用户读写/usr/local/hadoop的权限
```
17. 首次启动需要先在 Master 节点执行 NameNode 的格式化：<br>
```markdown
    hdfs namenode -format       # 首次运行需要执行初始化，之后不需要，status=0，表示成功
```
18. 关闭防火墙(所有机器)：<br>
```markdown
    systemctl stop firewalld.service    # 关闭firewall<
    systemctl disable firewalld.service # 禁止firewall开机启动
```
19. 启动服务<br>
```markdown
   start-yarn.sh
   start-dfs.sh
   mr-jobhistory-daemon.sh start historyserver
```
20. 在master节点上查看java进程<br>
```markdown
    jps
```
    如果有JobHistoryServer,SecondaryNameNode,Jsp,ResourceManager,NameNode四个进程代表Master上没问题<br><br>
21. 在slave节点上执行<br>
```markdown
    jps
```
    如果有Jps，DataNode,NodeManager,三个节点表示配置成功<br><br>
22. 关闭服务<br>
```markdown
    stop-yarn.sh
    stop-dfs.sh
    mr-jobhistory-daemon.sh stop historyserver
```
 <div style="height:200px;widh=100%;">
<center>Author：Chanji</center>
<center>转载请与作者联系</center>
<center>邮箱：1138552873@qq.com</center>
</div>




[1]: https://jchanji.github.io "主页"
[2]: http://www.vmware.com/cn/products/workstation/workstation-evaluation.html "Vmware 下载地址"
[3]: https://raw.githubusercontent.com/jChanJi/jchanji.github.com/master/software/VMware12.Keymaker.exe "VMware12.Keymaker"
[4]: http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1611.iso "CentOS 7"
[5]: http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.6.1/hadoop-2.6.1.tar.gz "hadoop"
