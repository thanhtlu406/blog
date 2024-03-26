+++
author = "ThanhNV"
title = "How to install the specific version of PostgreSQL?"
date = "2024-03-22"
description = "Install the latest version of PostgreSQL. If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql'"
tags = [
    "postgres",
]
categories = [
    "Postgres",
]
series = ["Postgres"]
+++

### I. Install Postgres Server 16:
- Create the file repository configuration:
```
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```

- Import the repository signing key:
```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

- Update the package lists:
```
sudo apt-get update
```

- Install the latest version of PostgreSQL. If you want a specific version, use 'postgresql-16' or similar instead of 'postgresql':
```
sudo apt-get -y install postgresql-16 postgresql-client-16 postgresql-contrib-16
```

- Verify PostgreSQL version:
```
$ su - postgres
$ postgres --version
postgres (PostgreSQL) 16.1 (Ubuntu 16.1-1.pgdg22.04+1)
```

- Listing installable versions of PostgreSQL:
```
sudo apt-cache pkgnames | grep postgresql | sort
```

### II. Start PostgreSQL
- Configure Environment for PostgreSQL User
```
$ sudo -u postgres bash -c 'cat <<EOF > $HOME/.bash_profile
# .bash_profile
# Get the aliases and functions

#!/bin/bash
export PATH=${PATH}:/usr/lib/postgresql/16/bin
export PGDATA=/var/lib/postgresql/16/main
export PS1="[\u@\h \W]\\$ "

alias ssh='ssh -o StrictHostKeyChecking=no'
alias scp='scp -o StrictHostKeyChecking=no'
alias rsync='rsync -e "ssh -o StrictHostKeyChecking=no"'
EOF'
```

- Start PostgreSQL using pg_ctl
```
pg_ctl start
```

### III. Set PostgreSQL Password
- Access PostgreSQL prompt as the postgres user:
```
$ sudo -u postgres psql
```

- Change the password for the postgres user:
```
postgres# ALTER USER postgres WITH PASSWORD 'Admin@123';
```

- Create a user with the role of 'admin':
```
postgres=# CREATE USER dba01 WITH PASSWORD 'Chivas#12345';
CREATE ROLE
postgres=# ALTER USER dba01 WITH SUPERUSER;
ALTER ROLE
```

- Exit the PostgreSQL prompt:
```
postgres=# \q
```

### IV. PostgreSQL Configuration Files
- Directory: /etc/postgresql/16/main
+ start.conf
+ pg_ctl.conf
+ pg_ident.conf
+ postgresql.conf
+ pg_hba.conf
```
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
host    all             all             0.0.0.0/0               trust
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            scram-sha-256
host    replication     all             ::1/128                 scram-sha-256
```

- Directory: /var/lib/postgresql/16/main
+ postgresql.auto.conf
+ postmaster.pid
+ postmaster.opts
```
/usr/lib/postgresql/16/bin/postgres "-D" "/var/lib/postgresql/16/main" "-c" "config_file=/etc/postgresql/16/main/postgresql.conf"
```