# OTUS.Lesson36

Тестовый стенд:
4 VM (2CPU, 2Gb Ram, 8Gb HDD, OS Centos7)

1. взять стенд https://github.com/erlong15/vagrant-bind 
добавить еще один сервер client2
завести в зоне dns.lab имена:
web1 - смотрит на клиент1
web2  смотрит на клиент2
завести еще одну зону newdns.lab
завести в ней запись
www - смотрит на обоих клиентов

2. настроить split-dns
клиент1 - видит обе зоны, но в зоне dns.lab только web1
клиент2 видит только dns.lab

Домашнее задание:
1. Запускаем плейбук DNS.yml
    После выполнения проверяем DNS запросы

Client1:
```
[root@client1 ~]# dig @10.200.3.221 ns01.dns.lab +short
10.200.3.221
[root@client1 ~]# dig @10.200.3.221 ns02.dns.lab +short
10.200.3.222
[root@client1 ~]# dig @10.200.3.221 web1.dns.lab +short
10.200.3.223
[root@client1 ~]# dig @10.200.3.221 web2.dns.lab +short
10.200.3.224
[root@client1 ~]# dig www.newdns.lab +short
10.200.3.224
10.200.3.223

```
Client2:
```
[root@client2 ~]# dig @10.200.3.222 ns01.dns.lab +short
10.200.3.221
[root@client2 ~]# dig www.newdns.lab +short
10.200.3.223
10.200.3.224
[root@client2 ~]# dig @10.200.3.222 ns02.dns.lab +short
10.200.3.222
[root@client2 ~]# dig @10.200.3.222 web1.dns.lab +short
10.200.3.223
[root@client2 ~]# dig @10.200.3.222 web2.dns.lab +short
10.200.3.224
```