## 1,Remote SSH For vscode

### Ubuntu:

`sudo apt update`

`sudo apt install openssh-server -y`

`sudo systemctl enable ssh`

`sudo systemctl start ssh`

`systemctl status ssh`

### Configure VirtualBox Networking:

NAT settings

Go to the VM settings > Network > Advanced > Port Forwarding.

Add a rule:

Host Port: 2222 (or any available port)

Guest Port: 22

Host IP: (Leave blank)

Guest IP: (Leave blank)

ssh admin@127.0.0.1 -p 2222

## 2, vscode

`mkdir work`

`cd work`

`git clone https://github.com/mysql/mysql-server.git`

`git checkout 8.4`

`git pull`

`sudo apt-get update`

```
sudo apt-get install pkg-config libncurses5-dev libtirpc-dev bison flex libz-dev libssl-dev cmake build-essential gcc g++ libboost-all-dev libsasl2-dev libldap2-dev libudev-dev libfido2-dev libcbor-dev gdb -y
```

`cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug -DWITH_SSL=system -DCMAKE_INSTALL_PREFIX=/home/admin/work/mysql-server/build/install`

`cd build`

`make -j8`

`make install`

`cd /home/admin/work/mysql-server/build/install`

`mkdir etc`

`cd etc`

`nano my.cnf`

```
[mysqld]
basedir=/home/admin/work/mysql-server/build/install
datadir=/home/admin/work/mysql-server/build/install/data
bind-address=0.0.0.0
port=3306
socket=/home/admin/work/mysql-server/build/install/mysql.sock
pid-file=/home/admin/work/mysql-server/build/install/mysqld.pid
log-error=/home/admin/work/mysql-server/build/install/mysqld.log
[client]
socket=/home/admin/work/mysql-server/build/install/mysql.sock
```

`cd /home/admin/work/mysql-server/build/install`

`mkdir data`


`/home/admin/work/mysql-server/build/install/bin/mysqld --initialize --basedir=/home/admin/work/mysql-server/build/install --datadir=/home/admin/work/mysql-server/build/install/data`

`/home/admin/work/mysql-server/build/install/bin/mysqld --defaults-file=/home/admin/work/mysql-server/build/install/etc/my.cnf`

### Set the root user for local and remote access
```
ALTER USER 'root'@'localhost' IDENTIFIED BY '12345678';
CREATE USER 'root'@'%' IDENTIFIED BY '12345678';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
launch.json
```
{
    "version": "0.2.0",
    "configurations": [

      {
        "name": "MySQL debug",
        "type": "cppdbg",
        "request": "launch",
        "program": "${workspaceFolder}/build/install/bin/mysqld",
        "args": [
            "--defaults-file=${workspaceFolder}/build/install/etc/my.cnf",
            "--verbose"
        ],
        "cwd": "${workspaceFolder}",
        "environment": [],
        "MIMode": "gdb",
        "miDebuggerPath": "/usr/bin/gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing",
            "ignoreFailures": true
            
          }
        ]
      }
    ]
  }
```  
