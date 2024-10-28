# Задание 1 - настройка hadoop кластера

## Настройка окружения

1. Для работы hadoop нужна Java
Устанавливаем Java на все 4 машины поочередно (по умолчанию в Ubuntu она не установлена)

`$ sudo apt update`<br>
`$ sudo apt install openjdk-11-jdk -y`

Проверка успешной установки

`$ java --version`

`openjdk 11.0.24 2024-07-16`<br>
`OpenJDK Runtime Environment (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1)`<br>
`OpenJDK 64-Bit Server VM (build 11.0.24+8-post-Ubuntu-1ubuntu324.04.1, mixed mode, sharing)`<br>

2. Качаем **hadoop 3.4** на джамп-ноду под пользователем hadoop и размножаем на все машины

`$ wget https://dlcdn.apache.org/hadoop/common/hadoop-3.4.0/hadoop-3.4.0.tar.gz`

`$ scp hadoop-3.4.0.tar.gz team-1-nn:/home/hadoop`<br>
`$ scp hadoop-3.4.0.tar.gz team-1-dn-00:/home/hadoop`<br>
`$ scp hadoop-3.4.0.tar.gz team-1-dn-01:/home/hadoop`<br>

3. У пользователя hadoop настраиваем переменные окружения в файле ~/.profile и активируем

`$ export HADOOP_HOME=/home/hadoop/hadoop-3.4.0`<br>
`$ export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64`<br>
`$ export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin/:$HADOOP_HOME/sbin`

`$ source ~/.profile`

4. Для гарантии в ~/hadoop-3.4.0/etc/hadoop/hadoop-env.sh прописывем

`JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64`

**Повторяем на всех 4 машинах**

## Настройка конфигурационных файлов Hadoop

1. Настраиваем конфигурацию на неймноде

core-site.xml (в папке $HADOOP_HOME/etc/hadoop/):

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

2. Размножам эти 3 файла на team-9-dn-00 и team-9-dn-01

```
$ scp core-site.xml team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/
$ scp core-site.xml team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/

$ scp hdfs-site.xml team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/
$ scp hdfs-site.xml team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/

$ scp workers team-9-dn-00:/home/hadoop/hadoop-3.4.0/etc/hadoop/
$ scp workers team-9-dn-01:/home/hadoop/hadoop-3.4.0/etc/hadoop/
```

## Форматирование файловой системы

`$ hadoop-3.4.0/bin/hdfs namenode -format`<br>

Запуск: 
`$ hadoop-3.4.0/sbin/start-dfs.sh`    (остановить - stop-dfs.sh)<br>

```
Starting namenodes on [team-9-nn]
Starting datanodes
team-9-nn: datanode is running as process 85697.  Stop it first and ensure /tmp/hadoop-hadoop-datanode.pid file is empty before retry.
team-9-dn-00: datanode is running as process 62138.  Stop it first and ensure /tmp/hadoop-hadoop-datanode.pid file is empty before retry.
team-9-dn-01: datanode is running as process 61912.  Stop it first and ensure /tmp/hadoop-hadoop-datanode.pid file is empty before retry.
Starting secondary namenodes [team-9-nn]
```

## Проверка, что все работает

можно проверить (jps это типа java ps) неймнода

```
hadoop@team-9-nn:~/hadoop-3.4.0$ jps
85697 DataNode
86189 NameNode
86653 Jps
86508 SecondaryNameNode
```

датаноды

```
hadoop@team-9-dn-00:~$ jps
62342 Jps
62138 DataNode
```
```
hadoop@team-9-dn-01:~$ jps
61912 DataNode
62092 Jps
```

## Настройка reverse-proxy на джамп ноде

идем на джамп ноду. там уже есть nginx. (если его нет, то применить `sudo apt install nginx`)

1. Копируем из под sudo 

`$ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/nn`

2. Делаем из под рута 2 правки в конфиге

```
server {
	listen 9870 default_server;
    ...

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		#try_files $uri $uri/ =404;
		proxy_pass http://team-9-nn:9870;
	}

    ...
}
```

3. Делаем символьную ссылку в папку доступных

`$ sudo ln -s /etc/nginx/sites-available/nn /etc/nginx/sites-enabled/nn`
 
4. Перезапуск инджиникса

`$ sudo systemctl reload nginx`

5. Проверяем в браузере: http://176.109.91.11:9870/index.html

![1](https://github.com/user-attachments/assets/6fa91b5f-881f-45cd-aff6-b12c36431eef)


Сверка с заданием и комментарии

![1-task](https://github.com/user-attachments/assets/c540f524-b15d-4661-bf64-554574c9ae12)

Все необходимые команды и файлы конфигурации описаны в соответствии с ТЗ, кластер развернут полностью.
На неймноде периодически происходит уход в safe-mode из-за того что на ней мало ресурсов, но это уже объективные ТУ.
Можно было бы убрать с хоста namenode функционал датаноды, но все делалось в четком соответствии с ТЗ (разбор ДЗ)
Также можно было бы настроить подмену ссылок intranet в выдаче hadoop чтобы не светить снаружу структуру нашей инфрасети и сделать 
их рабочими, но эта за дача очевидно за рамками курса. 

**По этой работе рассчитываем на максимальный балл**

