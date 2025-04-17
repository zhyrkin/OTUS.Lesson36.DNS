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
2. настроить split-dns
    Запускаем плейбук DNS-split.yml
После окончания работы проверяем: 
На client1:
```
[root@client1 ~]# ping www.newdns.lab
PING www.newdns.lab (10.200.3.223) 56(84) bytes of data.
64 bytes from client1.client1 (10.200.3.223): icmp_seq=1 ttl=64 time=0.031 ms
^C
--- www.newdns.lab ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.031/0.031/0.031/0.000 ms
[root@client1 ~]# ping web1.dns.lab
PING web1.dns.lab (10.200.3.223) 56(84) bytes of data.
64 bytes from client1.client1 (10.200.3.223): icmp_seq=1 ttl=64 time=0.037 ms
64 bytes from client1.client1 (10.200.3.223): icmp_seq=2 ttl=64 time=0.065 ms
64 bytes from client1.client1 (10.200.3.223): icmp_seq=3 ttl=64 time=0.070 ms
^V64 bytes from client1.client1 (10.200.3.223): icmp_seq=4 ttl=64 time=0.046 ms
64 bytes from client1.client1 (10.200.3.223): icmp_seq=5 ttl=64 time=0.019 ms
^C
--- web1.dns.lab ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4001ms
rtt min/avg/max/mdev = 0.019/0.047/0.070/0.019 ms
[root@client1 ~]# ping web2.dns.lab
ping: web2.dns.lab: Name or service not known
[root@client1 ~]# 
```
На client2:
```
[root@client2 ~]# ping www.newdns.lab
ping: www.newdns.lab: Name or service not known
[root@client2 ~]# ping web1.dns.lab
PING web1.dns.lab (10.200.3.223) 56(84) bytes of data.
64 bytes from 10.200.3.223 (10.200.3.223): icmp_seq=1 ttl=64 time=0.271 ms
^C
--- web1.dns.lab ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.271/0.271/0.271/0.000 ms
[root@client2 ~]# ping web2.dns.lab
PING web2.dns.lab (10.200.3.224) 56(84) bytes of data.
64 bytes from client2.client2 (10.200.3.224): icmp_seq=1 ttl=64 time=0.032 ms
64 bytes from client2.client2 (10.200.3.224): icmp_seq=2 ttl=64 time=0.035 ms
^C
--- web2.dns.lab ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.032/0.033/0.035/0.006 ms
[root@client2 ~]# 
```