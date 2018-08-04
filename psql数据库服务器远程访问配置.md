# psql数据库服务器远程访问配置


psql启动后首先查询psql安装位置：  
```shell
ps -ef | grep postgres
```  
这时候会返回若干postgres的位置，例如：  
```shell
/usr/iib/postgresql/10/bin/postgres
/var/lib/postgresql/10/main
config_file=/etc/postgresql/10/main/postgresql.conf
```  
找到.conf的配置文件路径。  

接着修改pg_hba.conf文件：  
```shell
sudo vim /etc/postgresql/10/main/pg_hba.conf
```  
这里注意使用sudo，不然会因为权限问题显示为空文件，接着修改为：  
```shell
host all all 127.0.0.1/32 trust
host all all 192.168.1.0/24 md5
host all all ::1/128 trust
```  
添加192.168.1.0使得远程IP可以访问，/24的含义是子网掩码0-255都可以访问。  

接着修改postgresql.conf，配置用户的访问权限：  
```shell
listen_addresses = '*'

port = 5432
max_connections = 100
```  
listen_addresses修改为所有，即'*'；psql默认端口是5432；最大连接数是100。
