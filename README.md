# МОВС - введение в платформы данных

Решение домашних заданий

## Исходные данные
даны 3 виртуальные машины с предустановленной Ubuntu

192.168.1.38 джамп-нода (team-9-jn)<br>
192.168.1.39 нейм-нода (team-9-nn)<br>
192.168.1.40 дата-нода (team-9-dn-00)<br>
192.168.1.41 дата-нода (team-9-dn-01)<br>

на каждой есть пользователь team с паролем и sudo

[**задача 1**](https://github.com/shiltsov/MOBC-bigdata/tree/main/hadoop) - установить и развернуть hadoop-кластер <br>
[**задача 2**](https://github.com/shiltsov/MOBC-bigdata/tree/main/yarn) - установить и настроить YARN<br>
[**задача 3**](https://github.com/shiltsov/MOBC-bigdata/tree/main/hive) - установить и настроить HIVE<br>

## команда
Рубашевский Кирилл<br>
Мишина Дарья<br>
Горбатова Татьяна<br>
Гераськина Надежда<br>
Шильцов Дмитрий

## предварителоьная настройка

на каждой из машин создаем пользователя hadoop и генерируем ключи

`$ sudo adduser hadoop` <br>
`$ ssh-keygen`

на каждой из машин редактируем файл /etc/hosts 

`$ sudo vim /etc/hosts`

делаем его таким:

`192.168.1.38 team-9-jn`<br>
`192.168.1.39 team-9-nn`<br>
`192.168.1.40 team-9-dn-00`<br>
`192.168.1.41 team-9-dn-01`<br>


на неймноде собираем все ключи со всех 4 машин в одном файле **authorized_keys**
копируя в него последовательно строка за строкой далее этот файл размножаем на
все 4 машины

`scp ~/.ssh/authorized_keys team-1-nn:/home/team/.ssh`<br>
`scp ~/.ssh/authorized_keys team-1-dn-0:/home/team/.ssh`<br>
`scp ~/.ssh/authorized_keys team-1-dn-1:/home/team/.ssh`<br>



