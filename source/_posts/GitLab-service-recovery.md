---
title: GitLab 服务维护
date: 2019-11-05 11:46:33
tags: [GitLab]
description: GitLab 服务维护记录
---

# GitLab 服务恢复过程回顾

1. GitLab 服务访问出现 500 错误

HTTP 状态码 500 - 服务异常

`gitlab-ctl`命令不可用，说明已经被卸载，即`gitlab-ce`被卸载

解决方案：重新安装`gitlab-ce`



2. 安装后启动失败页面访问`502`错误。

HTTP 状态码 502 - 网关错误，可判断访问的服务没有正常启动



`gitlab-ctl status` 查看服务启动状态

`gitlab-ctl tail <service-name>` 实时打印服务日志

`gitlab-ctl reconfigure` 重新启动服务查看失败日志

`gitlab-rake gitlab:check`

`sudo systemctl start gitlab-runsvdir`



```log
[2019-09-21T00:28:22+08:00] ERROR: Running exception handlers
[2019-09-21T00:28:22+08:00] ERROR: Exception handlers complete
[2019-09-21T00:28:22+08:00] FATAL: Stacktrace dumped to /opt/gitlab/embedded/cookbooks/cache/chef-stacktrace.out
[2019-09-21T00:28:22+08:00] FATAL: Please provide the contents of the stacktrace.out file if you file a bug report
[2019-09-21T00:28:22+08:00] FATAL: Mixlib::ShellOut::ShellCommandFailed: bash[migrate gitlab-rails database] (gitlab::database_migrations line 54) had an error: Mixlib::ShellOut::ShellCommandFailed: Expected process to exit with [0], but received '1'
---- Begin output of "bash"  "/tmp/chef-script20190921-15879-1dpmqb0" ----
STDOUT: rake aborted!
Your current database version is too old to be migrated. You should upgrade to GitLab 11.11.0 before moving to this version. Please see https://docs.gitlab.com/ee/policy/maintenance.html#upgrade-recommendations
/opt/gitlab/embedded/service/gitlab-rails/lib/tasks/migrate/schema_check.rake:13:in `block in <top (required)>'
/opt/gitlab/embedded/service/gitlab-rails/lib/tasks/gitlab/db.rake:49:in `block (3 levels) in <top (required)>'
/opt/gitlab/embedded/bin/bundle:23:in `load'
/opt/gitlab/embedded/bin/bundle:23:in `<main>'
```



通过命令查看，知道存在服务启动失败；`reconfigure`时日志报数据库迁移错误，官方要求升级需要一步，猜测当前默认安装版本过高导致以为低于`11.11.0`版本，导致数据库升级失败。

解决方案：优先解决`reconfigure`错误，尝试安装`11.11.0`版本解决数据库迁移失败，使用[命令](https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/xenial/gitlab-ce_11.11.0-ce.0_amd64.deb)`sudo apt-get install gitlab-ce=11.11.0-ce.0`安装



3. 安装且`reconfigure`显示数据库迁移成功后仍`502`

`gitlab-ctl status` 查看服务启动状态

`gitlab-ctl tail <service-name>` 实时打印服务日志，查看未启动服务状态

`/var/log/gitlab/<service-name>`下查看日志

`unicorn` 服务未启动，查看日志`unicorn/unicorn_stderr.log`端口被占用

```log
Errno::EADDRINUSE: Address already in use - bind(2) for 127.0.0.1:9090
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/socket_helper.rb:164:in `bind'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/socket_helper.rb:164:in `new_tcp_server'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/socket_helper.rb:144:in `bind_listen'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/http_server.rb:241:in `listen'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/http_server.rb:851:in `block in bind_new_listeners!'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/http_server.rb:851:in `each'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/http_server.rb:851:in `bind_new_listeners!'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/lib/unicorn/http_server.rb:140:in `start'
  /opt/gitlab/embedded/lib/ruby/gems/2.5.0/gems/unicorn-5.4.1/bin/unicorn:126:in `<top (required)>'
  /opt/gitlab/embedded/bin/unicorn:23:in `load'
  /opt/gitlab/embedded/bin/unicorn:23:in `<top (required)>'
```



`alertmanager` 服务未启动，查看日志`alertmanager/current`里`error`级别日志，

```log
2019-09-21_05:16:34.20223 level=error ts=2019-09-21T05:16:34.201528572Z caller=main.go:202 msg="unable to initialize gossip mesh" err="create memberlist: Failed to get final advertise address: No private IP address found, and explicit IP not provided"
```

解决方案：

修改`/etc/gitlab/gitlab.rb`恢复`unicorn`服务的默认端口

在官方找到无私网IP的[解决方法](https://gitlab.com/gitlab-org/omnibus-gitlab/issues/3705)修改`/etc/gitlab/gitlab.rb`，添加配置

```rb
alertmanager['flags'] = {
    'cluster.advertise-address' => "127.0.0.1:9093"
#   'web.listen-address' => "#{node['gitlab']['alertmanager']['listen_address']}"
#   'storage.path' => "#{node['gitlab']['alertmanager']['home']}/data"
#   'config.file' => "#{node['gitlab']['alertmanager']['home']}/alertmanager.yml"
}
```

使用`gitlab-ctl reconfigure`生效新配置



