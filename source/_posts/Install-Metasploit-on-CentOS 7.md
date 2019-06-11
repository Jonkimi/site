---
title: CentOS 7安装 Metasploit
date: 2018-05-16 18:27:40
tags: [CentOS, Metasploit]
description: 本文简单介绍了在 CentOS 7 上使用 Metasploit 的源代码进行安装与配置
---
<!-- ## Install metasploit on CentOS 7 -->

# Steps

1. Get source code, Enter into code directory

```shell
git clone git://github.com/rapid7/metasploit-framework.git
cd metasploit-framework
```

2. Install preparation, install Ruby and set it default

```shell
yum install yum-utils -y
yum-builddep -y ruby
rvm install ruby-2.5.1
rvm use 2.5.1 --default
```

3. Install rvm

```shell
curl -L get.rvm.io | bash -s stable
source /etc/profile.d/rvm.sh
```

4. Check output if you have gpg key check error, execute this command

```shell
gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

5. Bundler install

```shell
gem install rails
bundle install
```

6. Init and start postgresql

```shell
postgresql-setup initdb
systemctl start postgresql
```

7. Create postgresql user and database for metasploit

```shell
sudo -u postgres -i createuser msfuser -P
sudo -u postgres -i createdb -O msfuser msfdb
```

8. Config metasploit db

```shell
cp config/database.yml.example config/database.yml
```

9. Edit `database.yml`

```yaml
production: &pgsql
  adapter: postgresql
  database: msfdb
  username: msfuser
  password: msfpass
  host: localhost
  port: 5432
  pool: 5
  timeout: 5
```

10. Add  config to `/var/lib/pgsql/data/pg_hba.conf`

```conf
host   msfdb            msfuser      127.0.0.1/32          md5
```

11. Restart postgresql

```shell
systemctl restart postgresql.service
```

12. Start

```shell
./msfconsole
```

## Reference

\[1\] [https://gist.github.com/oogali/bc8ff245894c696c4dfa](https://gist.github.com/oogali/bc8ff245894c696c4dfa)
\[2\] [http://www.hi-roy.com/2015/12/28/centos7%E5%AE%89%E8%A3%85Metasploit%E6%A1%86%E6%9E%B6](http://www.hi-roy.com/2015/12/28/centos7%E5%AE%89%E8%A3%85Metasploit%E6%A1%86%E6%9E%B6)
\[3\] [https://blog.csdn.net/nixawk/article/details/44724755](https://blog.csdn.net/nixawk/article/details/44724755)
