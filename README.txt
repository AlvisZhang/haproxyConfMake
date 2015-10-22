# haproxyConfMake
多个 {hostname}.conf 拼接成 HaProxy.conf 

用于维护 haproxy.cfg 文件，以后增加新 HTTP 服务时，必须用脚本来维护相关反向代理设置，不要再直接编辑 haproxy.cfg 文件，相关操作步骤如下：

以 增加 x.y.com 为例，
1. 增加并编辑 /etc/haproxy/conf.d/x.y.com.conf 文件
-----------------------------------------------------------
[frontend]
hdr[] = a.x.y.com
hdr[] = b.x.y.com
hdr_end[] = x.y.com     # 相关的 http host name，支持两种写法 ，hdr[] 完全匹配，hdr_end[] 匹配后缀

[backend]
server[] = backend_1:80      # 对应的相关后台Server主机名，不要用 IP ，必须用物理机或者对应虚拟机的 hostname，相关 DNS 解析在 /etc/hosts 文件里维护
server[] = backend_2:80

[httpchk]
cmd = HEAD / HTTP/1.1\r\nHost:\ x.y.com     # 相关 http 会话检测用到的命令，如果删除则只对 80 端口做检查来判断是否存活

-----------------------------------------------------------

2. 执行 haproxyConfigMake 脚本，其会在 /etc/haproxy/ 目录下生成 haproxy.cfg.out 文件，可用 haproxy -c -f haproxy.cfg.out 是否有效，然后替换 haproxy.cfg 后重启 haproxy。
亦可直接执行 # /etc/haproxy/haproxyConfigMake -o /etc/haproxy/haproxy.cfg; service haproxy reload 来完成。
