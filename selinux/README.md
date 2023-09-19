# SELinux

## ファイルコンテキスト

```
unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/file2
SELinux User:Role:Type:Level File
```

## SELinux モードの変更

```bash
$ getenforce
Enforcing
$ setenforce 0
$ getenforce
Permissive
$ setenforce Enforcing
$ getenforce
Enforcing
```

## SELinux コンテキストの変更

```bash
$ chcon -t httpd_sys_content_t /virtual
$ ls -Zd /virtual
unconfined_u:object_r:httpd_sys_content_t:s0 /virtual
$ restorecon -v /virtual
$ ls -Zd /virtual
unconfined_u:object_r:default_t:s0 /virtual
```

## SELinux ファイルコンテキストの一覧

```bash
$ semanage fcontext -l
$ semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'
```

## 演習用

エラーを確認

```bash
$ less /var/log/messages
...output omitted...
Apr 7 06:16:15 serverb setroubleshoot[26509]: failed to retrieve rpm info for /
lab-content/la
b.html
Apr 7 06:16:17 serverb setroubleshoot[26509]: SELinux is preventing /usr/sbin/
httpd from getattr access on the file /lab-content/lab.html. For complete SELinux
 messages run: sealert -l 35c9e452-2552-4ca3-8217-493b72ba6d0b
Apr 7 06:16:17 serverb setroubleshoot[26509]: SELinux is preventing /usr/sbin/
httpd from getattr access on the file /lab-content/lab.html
...output omitted...
```

指摘されたコマンドを実行

```bash
$ sealert -l 35c9e452-2552-4ca3-8217-493b72ba6d0b
SELinux is preventing /usr/sbin/httpd from getattr access on the file /labcontent/lab.html.
***** Plugin catchall_labels (83.8 confidence) suggests *******************
If you want to allow httpd to have getattr access on the lab.html file
Then you need to change the label on /lab-content/lab.html
Do
# semanage fcontext -a -t FILE_TYPE '/lab-content/lab.html'
where FILE_TYPE is one of the following:
...output omitted...
Additional Information:
Source Context system_u:system_r:httpd_t:s0
Target Context unconfined_u:object_r:default_t:s0
Target Objects /lab-content/lab.html [ file ]
Source httpd
Source Path /usr/sbin/httpd
Port <Unknown>
Host serverb.lab.example.com
Source RPM Packages httpd-2.4.51-7.el9_0.x86_64
Target RPM Packages
SELinux Policy RPM selinux-policy-targeted-34.1.27-1.el9.noarch
Local Policy RPM selinux-policy-targeted-34.1.27-1.el9.noarch
Selinux Enabled True
Policy Type targeted
Enforcing Mode Enforcing
Host Name serverb.lab.example.com
Platform Linux serverb.lab.example.com
 5.14.0-70.2.1.el9_0.x86_64 #1 SMP PREEMPT Wed Mar
 16 18:15:38 EDT 2022 x86_64 x86_64
Alert Count 8
First Seen 2022-04-07 06:14:45 EDT
Last Seen 2022-04-07 06:16:12 EDT
Local ID 35c9e452-2552-4ca3-8217-493b72ba6d0b
Raw Audit Messages
```

audit.log を検索

```bash
$ ausearch -m AVC -ts recent
...output omitted...
----
time->Thu Apr 7 06:16:12 2022
type=PROCTITLE msg=audit(1649326572.086:407):
 proctitle=2F7573722F7362696E2F6874747064002D44464F524547524F554E44
type=SYSCALL msg=audit(1649326572.086:407): arch=c000003e syscall=262 success=no
 exit=-13 a0=ffffff9c a1=7f7c8c0457c0 a2=7f7c887f7830 a3=100 items=0 ppid=10641
 pid=10731 auid=4294967295 uid=48 gid=48 euid=48 suid=48 fsuid=48 egid=48
 sgid=48 fsgid=48 tty=(none) ses=4294967295 comm="httpd" exe="/usr/sbin/httpd"
 subj=system_u:system_r:httpd_t:s0 key=(null)
type=AVC msg=audit(1649326572.086:407): avc: denied { getattr } for
 pid=10731 comm="httpd" path="/lab-content/lab.html" dev="vda4" ino=18192752
 scontext=system_u:system_r:httpd_t:s0 tcontext=unconfined_u:object_r:default_t:s0
 tclass=file permissive=0
```

コンテキストを比較

```bash
$ ls -dZ /lab-content /var/www/html
unconfined_u:object_r:default_t:s0 /lab-content
system_u:object_r:httpd_sys_content_t:s0 /var/www/html
```

コンテキストをデフォルトに戻す

```bash
$ semanage fcontext -a \
-t httpd_sys_content_t '/lab-content(/.*)?'
$ restorecon -R /lab-content/
```
