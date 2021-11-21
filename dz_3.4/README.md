# Домашнее задание 3.4

1. Сервис стартует и перезапускается корректно:
 
```bash
	vagrant@vagrant:~$ ps -e |grep node_exporter   
	1041 ?        00:00:00 node_exporter
	vagrant@vagrant:~$ systemctl stop node_exporter
	==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
	Authentication is required to stop 'node_exporter.service'.
	Authenticating as: vagrant,,, (vagrant)
	Password: 
	==== AUTHENTICATION COMPLETE ===
	vagrant@vagrant:~$ ps -e |grep node_exporter
	vagrant@vagrant:~$ systemctl start node_exporter
	==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
	Authentication is required to start 'node_exporter.service'.
	Authenticating as: vagrant,,, (vagrant)
	Password: 
	==== AUTHENTICATION COMPLETE ===
	vagrant@vagrant:~$ ps -e |grep node_exporter
  	1257 ?        00:00:00 node_exporter
	vagrant@vagrant:~$ 
```

Прописан конфигруационный файл:

```	
	vagrant@vagrant:/etc/systemd/system$ cat /etc/systemd/system/node_exporter.service
	[Unit]
	Description=Node Exporter
 
	[Service]
	ExecStart=/opt/node_exporter/node_exporter
	EnvironmentFile=/etc/default/node_exporter
 
	[Install]
	WantedBy=default.target
```


при перезапуске переменная окружения выставляется :

```bash
	agrant@vagrant:/etc/systemd/system$ sudo cat /proc/1809/environ
	LANG=en_US.UTF-8LANGUAGE=en_US:PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
	INVOCATION_ID=0fcb24d52895405c875cbb9cbc28d3ffJOURNAL_STREAM=9:35758MYVAR=some_value
```

2. 
 
```bash
	CPU:
	node_cpu_seconds_total{cpu="0",mode="idle"} 2401.03
   	node_cpu_seconds_total{cpu="0",mode="system"} 24.76
    	node_cpu_seconds_total{cpu="0",mode="user"} 4.35
    	process_cpu_seconds_total
    
	Memory:
    	node_memory_MemAvailable_bytes 
    	node_memory_MemFree_bytes
    
	Disk:
    	node_disk_io_time_seconds_total{device="sda"} 
    	node_disk_read_bytes_total{device="sda"} 
    	node_disk_read_time_seconds_total{device="sda"} 
    	node_disk_write_time_seconds_total{device="sda"}
    
	Network:
    	node_network_receive_errs_total{device="eth0"} 
    	node_network_receive_bytes_total{device="eth0"} 
    	node_network_transmit_bytes_total{device="eth0"}
    	node_network_transmit_errs_total{device="eth0"}
```
3.

`Netdata` установлена, но проброшен порт 9999, так как 19999 - занять на хостовой машине под локальный `netdata` 

информация с хостовой машины:

```bash
	~/vagrant$ sudo lsof -i :19999
	COMMAND   PID    USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
	netdata 5035 netdata    4u  IPv4 1005649      0t0  TCP localhost:19999 (LISTEN)
	~/vagrant$ sudo lsof -i :9999
	COMMAND     PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
	chrome     3128 dkhayrov   80u  IPv4 1110886      0t0  TCP localhost:37687->localhost:9999 (ESTABLISHED)
	VBoxHeadl 35570 dkhayrov   21u  IPv4 1003297      0t0  TCP *:9999 (LISTEN)
	VBoxHeadl 35570 dkhayrov   30u  IPv4 1113092      0t0  TCP localhost:9999->localhost:37687 (ESTABLISHED)
```

информация с vm машины:

```bash
	vagrant@vagrant:~$ sudo lsof -i :19999
	COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
	netdata 1467 netdata    4u  IPv4  28043      0t0  TCP *:19999 (LISTEN)
	netdata 1467 netdata   55u  IPv4  30568      0t0  TCP vagrant:19999->_gateway:38598 (ESTABLISHED)
```

4. 

Судя по выводу `dmesg` да, причем даже тип ВМ, так как есть соответсвующая строка:
```bash
    	agrant@vagrant:~$ dmesg |grep virtualiz
	[    0.002836] CPU MTRRs all blank - virtualized system.
	[    0.074550] Booting paravirtualized kernel on KVM
	[    4.908209] systemd[1]: Detected virtualization oracle.
```

Если сравнить с хостовой машиной то это становится очевидным:

```bash
	~/vagrant$ dmesg |grep virtualiz
	[    0.048461] Booting paravirtualized kernel on bare hardware
```

`... on bare hardware` - что означает на чистом железе.

5. 
```bash    
	vagrant@vagrant:~$ /sbin/sysctl -n fs.nr_open
	1048576
```

Это максимальное число открытых дескрипторов для ядра (системы), для пользователя задать больше этого числа нельзя (если не менять). 
Число задается кратное 1024, в данном случае `=1024*1024`. 

Но макс.предел ОС можно посмотреть так :
```bash
	vagrant@vagrant:~$ cat /proc/sys/fs/file-max
	9223372036854775807

	vagrant@vagrant:~$ ulimit -Sn
	1024
```

мягкий лимит (так же `ulimit -n`) на пользователя (может быть увеличен процессов в процессе работы)
```bash
	vagrant@vagrant:~$ ulimit -Hn
	1048576
```

жесткий лимит на пользователя (не может быть увеличен, только уменьшен)

Оба `ulimit -n` НЕ могут превысить системный `fs.nr_open`

6.
```bash
	root@vagrant:~# ps -e |grep sleep
   	1987 pts/2    00:00:00 sleep
	root@vagrant:~# nsenter --target 2020 --pid --mount
	root@vagrant:/# ps
    	PID TTY          TIME CMD
      	2 pts/0    00:00:00 bash
     	11 pts/0    00:00:00 ps
```
7.
Из предыдущих лекций ясно что это функция внутри `{}`, судя по всему с именем `:` , которая после опредения в строке запускает саму себя.
Пораждает два фоновых процесса самой себя, получается этакое бинарное дерево плодящее процессы 

```bash
[ 3099.973235] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope
[ 3103.171819] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-11.scope
```

Cистема на основании этих файлов в пользовательской зоне ресурсов имеет определенное ограничение на создаваемые ресурсов и соответсвенно при превышении начинает блокировать создание числа 

Если установить `ulimit -u 50` - число процессов будет ограниченно 50 для пользоователя. 
