# Задание 1 - настройка hadoop кластера

для работы hadoop нужна java
ставим java на все 4 машины поочередно (было в пререквизитах что она стоит но по умолчанию в убунту это не так)

`$ sudo apt update`<br>
`$ sudo apt install openjdk-11-jdk -y`

проверим работу

`$ java --version`

`openjdk 11.0.24 2024-07-16`<br>
`OpenJDK Runtime Environment (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1)`<br>
`OpenJDK 64-Bit Server VM (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1, mixed mode, sharing)`<br>

качаем **hadoop 3.4** на джамп-ноду под пользователем hadoop

`$ wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz`

и размножаем на все машины

`$ scp hadoop-3.4.0.tar.gz team-1-nn:/home/hadoop`<br>
`$ scp hadoop-3.4.0.tar.gz team-1-dn-00:/home/hadoop`<br>
`$ scp hadoop-3.4.0.tar.gz team-1-dn-01:/home/hadoop`<br>

у пользователя hadoop настраиваем переменные окружения в файле ~/.profile

`$ export HADOOP_HOME=/home/hadoop/hadoop-3.4.0`<br>
`$ export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64`
`$ export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin/:$HADOOP_HOME/sbin`

активируем

`$ source ~/.profile`

еще Саттар говорит что нужно для гарантии в ~/hadoop-3.4.0/etc/hadoop/hadoop-env.sh прописать

`JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64`

**все это делаем на всех 4 машинах**

далее сначала на неймноде

Настройка конфигурационных файлов Hadoop core-site.xml (в папке $HADOOP_HOME/etc/hadoop/):

`
<configuration><br>
  <property><br>
    <name>fs.defaultFS</name><br>
    <value>hdfs://team-9-nn:9000</value><br>
  </property><br>
</configuration><br>
`
hdfs-site.xml

`
<configuration><br>
  <property><br>
    <name>dfs.replication</name><br>
    <value>3</value><br>
  </property><br>
</configuration><br>
`
workers

`
team-9-nn<br>
team-9-dn-00<br>
team-9-dn-01<br>
`

после этого размножам эти 3 файла на team-9-dn-00 и team-9-dn-01

`scp core-site.xml team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>
`scp core-site.xml team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>

`scp hdfs-site.xml team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>
`scp hdfs-site.xml team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>  

`scp workers team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>
`scp workers team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/` <br>



