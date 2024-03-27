+++
author = "ThanhNV"
title = "How to Install PostgreSQL 9.6 on CentOS 7?"
date = "2024-03-26"
description = "Step-by-step guide to installing PostgreSQL 9.6 on CentOS 7"
tags = [
    "postgres",
]
categories = [
    "Postgres",
]
series = ["Postgres"]
+++

### Install Postgres Server 9.6
- Install CentOS SCLo RH repository:
```
$ wget -c http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-postgresql96-postgresql-libs-9.6.19-1.el7.x86_64.rpm

$ sudo rpm -Uvh rh-postgresql96-postgresql-libs-9.6.19-1.el7.x86_64.rpm

$ sudo yum install centos-release-scl-rh

$ sudo yum install rh-postgresql96-postgresql-libs
```

- Install rh-postgresql96-postgresql-syspaths:
```
$ wget -c http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-postgresql96-postgresql-syspaths-9.6.19-1.el7.x86_64.rpm
$ sudo rpm -Uvh rh-postgresql96-postgresql-syspaths-9.6.19-1.el7.x86_64.rpm
$ sudo yum install rh-postgresql96-postgresql-syspaths
```

- Install a PostgreSQL contrib extension:
```
$ wget http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-postgresql96-postgresql-contrib-9.6.19-1.el7.x86_64.rpm
$ rpm -Uvh rh-postgresql96-postgresql-contrib-9.6.19-1.el7.x86_64.rpm
$ sudo yum install rh-postgresql96-postgresql-contrib
```

- Download and install rh-postgresql96-postgresql-server package:
```
$ wget -c http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/r/rh-postgresql96-postgresql-server-9.6.19-1.el7.x86_64.rpm
$ sudo rpm -Uvh rh-postgresql96-postgresql-server-9.6.19-1.el7.x86_64.rpm
```

- Install PostgreSQL and runtime packages via yum:
```
$ sudo yum update -y 
$ sudo yum install rh-postgresql96-postgresql rh-postgresql96-runtime
```

- Initialize the PostgreSQL database:
```
$ sudo yum install mlocate -y

$ sudo updatedb

$ sudo locate initdb
/opt/rh/rh-postgresql96/root/usr/bin/initdb
/opt/rh/rh-postgresql96/root/usr/share/locale/cs/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/de/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/es/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/fr/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/it/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ja/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ko/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/pl/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/pt_BR/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ru/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/sv/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/tr/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/zh_CN/LC_MESSAGES/initdb-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/man/man1/initdb.1.gz
/usr/libexec/initscripts/legacy-actions/rh-postgresql96-postgresql/initdb

$ sudo /usr/libexec/initscripts/legacy-actions/rh-postgresql96-postgresql/initdb
```

### Start and Enable PostgreSQL Service

- Start PostgreSQL service:
```
$ sudo systemctl start rh-postgresql96-postgresql
```

- Enable PostgreSQL to start on boot:
```
$ sudo systemctl enable rh-postgresql96-postgresql
```

### Enable pg_ctl status/stop/restart
- Find the location of libpq.so.rh-postgresql96-5
```
$ find / -name libpq.so.rh-postgresql96-5
```

- Create symbolic links for libpq.so.rh-postgresql96-5:
```
$ ln -sf /opt/rh/rh-postgresql96/root/usr/lib64/libpq.so.rh-postgresql96-5 /usr/lib/libpq.so.rh-postgresql96-5
$ ln -sf /opt/rh/rh-postgresql96/root/usr/lib64/libpq.so.rh-postgresql96-5 /usr/lib64/libpq.so.rh-postgresql96-5
```

### Configure Environment for PostgreSQL User
```
$ sudo locate pg_ctl
/opt/rh/rh-postgresql96/root/usr/bin/pg_ctl
/opt/rh/rh-postgresql96/root/usr/share/locale/cs/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/de/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/es/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/fr/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/it/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ja/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ko/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/pl/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/pt_BR/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/ru/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/sv/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/tr/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/locale/zh_CN/LC_MESSAGES/pg_ctl-9.6.mo
/opt/rh/rh-postgresql96/root/usr/share/man/man1/pg_ctl.1.gz
```

```
$ sudo -u postgres bash -c 'cat <<EOF > $HOME/.bash_profile
# .bash_profile
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi
# User specific environment and startup programs

PGDATA=/var/opt/rh/rh-postgresql96/lib/pgsql/data
export PGDATA

export PATH="$PATH:/opt/rh/rh-postgresql96/root/usr/bin"
export PS1="[\u@\h \W]\\$ "
EOF'
```

### Create symbolic links for the PostgreSQL data directory
```
$ su - postgres
$ ln -sf /var/opt/rh/rh-postgresql96/lib/pgsql/data /pg_data

$ cp -prn /pg_data/data/pg_xlog /tmp && rm -rf /pg_data/data/pg_xlog
$ ln -sf /pg_xlog /pg_data/data/pg_xlog
```

### Set PostgreSQL Password
- Access PostgreSQL prompt as the postgres user:
```
$ sudo -u postgres psql
```

- Change the password for the postgres user
```
postgres# ALTER USER postgres WITH PASSWORD 'Admin@123';
```

- Exit the PostgreSQL prompt
```
postgres=# \q
```