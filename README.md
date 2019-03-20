# bigdata
1 建立虚拟机
（1）修改用户名和密码
     useradd hadoop
       passwd hadoop
(2) 修改主机名
      vi /etc/sysconfig/network
        内容
    NETWORKING=yes
     HOSTNAME=master
(3)可以用主机名互相访问
    192.168.190.200(主机ip) master
     192.168.190.201(从机器） slave1

(4)配置静态ip地址：
vi /etc/sysconfig/network-scripts/ifcfg-em1
内容：
DEVICE="em1"
BOOTPROTO="static"
NM_CONTROLLED="yes"
ONBOOT="yes"
#TYPE="Ethernet"
#下面为静态IP地址
IPADDR=192.168.1.109
NETMASK=255.255.0.0
#下面为网关IP地址
GATEWAY=192.168.1.1
DNS1=8.8.8.8
（5） 构建ssh
rpm -qa | grep rsync
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@master
ssh master 登陆验证
（6）安装java jdk
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel

注：如果安装的为64位服务器需要下载名字带src的hadoop包，并编译安装，此过程较为复杂，可以尝试安装32位Centos直接下载安装，修改配置文件即可使用
尾部为tar.gz的包为编译好的包
hadoop 安装步骤

事先：打开安装目录
vi /home/hadoop/etc/hadoop
1 修改core-site.xml
<configuration>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>file:/home/hadoop/hadoop-2.8.5/tmp</value>
                <description>Abase for other temporary directories.</description>
        </property>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        <property>
                <name>io.file.buffer.size</name>
                <value>131072</value>
        </property>
</configuration>

2 修改hdfs-site.xml,hadoop-env.sh

one:
<configuration>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>master:9001</value>
        </property>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/home/hadoop/hadoop-2.8.5/dfs/name</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/home/hadoop/hadoop-2.8.5/dfs/data</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>
        <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
        </property>
</configuration>
two:
export JAVA_HOME=绝对路径

3 修改maperd-site.xml

<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>master:10020</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>master:50030</value>
        </property>
        <property>
                <name>mapreduce.reduce.memory.mb</name>
                <value>4096</value>
        </property>
</configuration>
4 修改slaves


5修改yarn-site.xml

<configuration>

<!-- Site specific YARN configuration properties -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

        <property>
                <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>

        <property>
                <name>yarn.resourcemanager.address</name>
                <value>master:8032</value>
        </property>

        <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>master:8030</value>
        </property>

        <property>
                <name>yarn.resourcemanager.resource-traker.address</name>
                <value>master:8031</value>
        <property>

        <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>master:8033</value>
        </property>

        <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>master:8088</value>
        </property>
</configuration>
7 ：可以将hadoop home填入/etc/profile中

     export HADOOP_HOME=绝对路径
     export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin




