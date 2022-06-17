# Домашнее задание к занятию "5.5. Оркестрация кластером Docker контейнеров на примере Docker Swarm"

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Любые вопросы по решению задач задавайте в чате учебной группы.

---

## Задача 1

Дайте письменые ответы на следующие вопросы:

- В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global?

Ответ: Сервис работающий в режиме replication будет задеплоин в конкретном количестве нод, которое было указано в свойстве replicas. Режим global предусматривает, что сервис будет задепдоин на всех нодах.
- Какой алгоритм выбора лидера используется в Docker Swarm кластере?

Ответ: The Raft Consensus Algorithm - алгоритм отвечающий за выбор лидера. Работает следующим образом: В случае, если необходимо выбрать лидера, то ноды, которые быстрее всех стали кандидатами(у всех нод есть свой таймаут, который выставляется рандомно) участвуют в голосовании, кто станет лидером. Нода-кандидат отправляет запрос на голос всем нодам, результат возвращается к ноде-кандидату. Какая нода получит больше голосов, та и станет лидером.
- Что такое Overlay Network?

Ответ:
Overlay Network - это специальный вид сети, сделанный для кластерных решений. Он объединяет несколько docker хостов одной сетью, снимая задачу маршрутизации на уровне ОС между контейнерами

## Задача 2

Создать ваш первый Docker Swarm кластер в Яндекс.Облаке

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
docker node ls
```

Ответ:
```
[root@node01 centos]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
ajbz1pcm8dl20evsxplf0h0a1 *   node01.netology.yc   Ready     Active         Leader           20.10.17
ndohzyu6vokef3i3esi15qs8r     node02.netology.yc   Ready     Active         Reachable        20.10.17
j2xc2jdi0folu20hqjww5g44k     node03.netology.yc   Ready     Active         Reachable        20.10.17
il0ayg3adiw5gh71rbczqp2sk     node04.netology.yc   Ready     Active                          20.10.17
umbbysc09zsem9vllwc1zzg0z     node05.netology.yc   Ready     Active                          20.10.17
m5sgh15xxuobdvp9nf2s6jxnm     node06.netology.yc   Ready     Active                          20.10.17
[root@node01 centos]# 
```
## Задача 3

Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
docker service ls
```

Ответ:
```
[root@node01 centos]# docker service ls
ID             NAME                                MODE         REPLICAS   IMAGE                                          PORTS
j4ev4h3wm017   swarm_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0    
ydy28nkugrhc   swarm_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
sssrkqit959d   swarm_monitoring_cadvisor           global       6/6        google/cadvisor:latest                         
iut89bauei8a   swarm_monitoring_dockerd-exporter   global       6/6        stefanprodan/caddy:latest                      
k1g5sai9udu8   swarm_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4           
og0nq4dd9838   swarm_monitoring_node-exporter      global       6/6        stefanprodan/swarmprom-node-exporter:v0.16.0   
p78e30mh451r   swarm_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0       
qrbk9s7bqglb   swarm_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0 
```
## Задача 4 (*)

Выполнить на лидере Docker Swarm кластера команду (указанную ниже) и дать письменное описание её функционала, что она делает и зачем она нужна:
```
# см.документацию: https://docs.docker.com/engine/swarm/swarm_manager_locking/
docker swarm update --autolock=true
```

