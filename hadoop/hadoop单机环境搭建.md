# Hadoop�����滷���

<nav>
<a href="#һǰ������">һ��ǰ������</a><br/>
<a href="#������-SSH-���ܵ�¼">�������� SSH ���ܵ�¼</a><br/>
<a href="#��HadoopHDFS�����">����Hadoop(HDFS)�����</a><br/>
<a href="#��HadoopYARN�����">�ġ�Hadoop(YARN)�����</a><br/>
</nav>




## һ��ǰ������

Hadoop ���������� JDK����ҪԤ�Ȱ�װ����װ�������

+ [Linux �� JDK �İ�װ](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux��JDK��װ.md)



## �����������ܵ�¼

Hadoop ���֮����Ҫ���� SSH ����ͨѶ��

#### 2.1 ����ӳ��

���� ip ��ַ��������ӳ�䣺

```shell
vim /etc/hosts
# �ļ�ĩβ����
192.168.43.202  hadoop001
```

### 2.2  ���ɹ�˽Կ

ִ���������������ɹ��׺�˽�ף�

```
ssh-keygen -t rsa
```

### 3.3 ��Ȩ

���� `~/.ssh` Ŀ¼�£��鿴���ɵĹ��׺�˽�ף���������д�뵽��Ȩ�ļ���

```shell
[root@@hadoop001 sbin]#  cd ~/.ssh
[root@@hadoop001 .ssh]# ll
-rw-------. 1 root root 1675 3 ��  15 09:48 id_rsa
-rw-r--r--. 1 root root  388 3 ��  15 09:48 id_rsa.pub
```

```shell
# д�빫�׵���Ȩ�ļ�
[root@hadoop001 .ssh]# cat id_rsa.pub >> authorized_keys
[root@hadoop001 .ssh]# chmod 600 authorized_keys
```



## ����Hadoop(HDFS)�����



### 3.1 ���ز���ѹ

���� Hadoop ��װ�������������ص��� CDH �汾�ģ����ص�ַΪ��http://archive.cloudera.com/cdh5/cdh/5/

```shell
# ��ѹ
tar -zvxf hadoop-2.6.0-cdh5.15.2.tar.gz 
```



### 3.2 ���û�������

```shell
# vi /etc/profile
```

���û���������

```
export HADOOP_HOME=/usr/app/hadoop-2.6.0-cdh5.15.2
export  PATH=${HADOOP_HOME}/bin:$PATH
```

ִ�� `source` ���ʹ�����õĻ�������������Ч��

```shell
# source /etc/profile
```



### 3.3 �޸�Hadoop����

���� `${HADOOP_HOME}/etc/hadoop/ ` Ŀ¼�£��޸��������ã�

#### 1. hadoop-env.sh

```shell
# JDK��װ·��
export  JAVA_HOME=/usr/java/jdk1.8.0_201/
```

#### 2. core-site.xml

```xml
<configuration>
    <property>
        <!--ָ�� namenode �� hdfs Э���ļ�ϵͳ��ͨ�ŵ�ַ-->
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop001:8020</value>
    </property>
    <property>
        <!--ָ�� hadoop �洢��ʱ�ļ���Ŀ¼-->
        <name>hadoop.tmp.dir</name>
        <value>/home/hadoop/tmp</value>
    </property>
</configuration>
```

#### 3. hdfs-site.xml

ָ������ϵ������ʱ�ļ��洢λ�ã�

```xml
<configuration>
    <property>
        <!--�������������ǵ����汾������ָ�� dfs �ĸ���ϵ��Ϊ 1-->
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

#### 4. slaves

�������д����ڵ���������� IP ��ַ�������ǵ����汾������ָ���������ɣ�

```shell
hadoop001
```



### 3.4 �رշ���ǽ

���رշ���ǽ���ܵ����޷����� Hadoop �� Web UI ���棺

```shell
# �鿴����ǽ״̬
sudo firewall-cmd --state
# �رշ���ǽ:
sudo systemctl stop firewalld.service
```



### 3.5 ��ʼ��

��һ������ Hadoop ʱ��Ҫ���г�ʼ�������� `${HADOOP_HOME}/bin/` Ŀ¼�£�ִ���������

```shell
[root@hadoop001 bin]# ./hdfs namenode -format
```



### 3.6 ����HDFS

���� `${HADOOP_HOME}/sbin/` Ŀ¼�£����� HDFS��

```shell
[root@hadoop001 sbin]# ./start-dfs.sh
```



### 3.7 ��֤�Ƿ������ɹ�

��ʽһ��ִ�� `jps` �鿴 `NameNode` �� `DataNode` �����Ƿ��Ѿ�������

```shell
[root@hadoop001 hadoop-2.6.0-cdh5.15.2]# jps
9137 DataNode
9026 NameNode
9390 SecondaryNameNode
```



��ʽ�����鿴 Web UI ���棬�˿�Ϊ `50070`��

<div align="center"> <img width="700px" src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop��װ��֤.png"/> </div>


## �ġ�Hadoop(YARN)�����

### 4.1 �޸�����

���� `${HADOOP_HOME}/etc/hadoop/ ` Ŀ¼�£��޸��������ã�

#### 1. mapred-site.xml

```shell
# ���û��mapred-site.xml���򿽱�һ�������ļ������޸�
cp mapred-site.xml.template mapred-site.xml
```

```xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```

#### 2. yarn-site.xml

```xml
<configuration>
    <property>
        <!--���� NodeManager �����еĸ���������Ҫ���ó� mapreduce_shuffle ��ſ����� Yarn ������ MapReduce ����-->
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```



### 4.2 ��������

���� `${HADOOP_HOME}/sbin/` Ŀ¼�£����� YARN��

```shell
./start-yarn.sh
```



#### 4.3 ��֤�Ƿ������ɹ�

��ʽһ��ִ�� `jps` ����鿴 `NodeManager` �� `ResourceManager` �����Ƿ��Ѿ�������

```shell
[root@hadoop001 hadoop-2.6.0-cdh5.15.2]# jps
9137 DataNode
9026 NameNode
12294 NodeManager
12185 ResourceManager
9390 SecondaryNameNode
```

��ʽ�����鿴 Web UI ���棬�˿ں�Ϊ `8088`��

<div align="center"> <img width="700px" src="https://github.com/heibaiying/BigData-Notes/blob/master/pictures/hadoop-yarn��װ��֤.png"/> </div>