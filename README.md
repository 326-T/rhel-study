## アカウント
### ログインやパスワードの設定
```bash
cat /etc/shadow
user03:$6$CSsXsd3rwghsdfarf:17933:0:99999:7:2:18113: 
```
- user03: ユーザーアカウントの名前。
- $6$CSsXsd3rwghsdfarf: ユーザーの暗号化されたパスワード。
- 17933: パスワードが最後に変更されたエポックからの⽇数。エポックは UTC タイムゾーンの1970-01-01。
- 0: ユーザーが最後にパスワードを変更してから、再度パスワードを変更できるようになるまでの最⼩⽇数。
- 99999: パスワードが期限切れになるまでにパスワードを変更せずに済む最⼤⽇数。空のフィールドは、パスワードが期限切れにならないことを意味します。
- 7: パスワードが期限切れになることをユーザーに警告する期限前の⽇数。
- 2: パスワードが期限切れになった⽇から、アカウントが⾃動的にロックされるまで、アクティビティがない⽇数。
- 18113: エポック以降でアカウントが期限切れになる⽇。空のフィールドは、アカウントが期限切れにならないことを意味します。
- この最後のフィールドは通常は空で、将来使⽤するために予約されています。

user01のパスワードポリシーを変更する。
```bash
chage -m 0 -M 90 -W 7 -I 14 user01
```

パスワードを変更する
```bash
passwd user01
```

90日後にパスワードを有効期限切れになるようにする
```bash
date -d "+90 days" +%F
2023-12-31
chage -E 2023-12-31 user01
```

全体に適用する。`/etc/login.defs`

---
### 特権権限をつける
- `user01`に特権権限をつける

  `/etc/sudoers.d/user01`
  ```
  user01 ALL=(ALL) ALL
  ```
- `consultants`グループに特権権限をつける

  `/etc/sudoers.d/consultants`
  ```
  %consultants ALL=(ALL) ALL
  ```
---

### ファイルアクセス
setgid(2)とあったら以下みたいにする。
```
chmod -R 2770 techdocs
```

---
## SELinux
ファイルコンテキスト
```
unconfined_u:object_r:httpd_sys_content_t:s0 /var/www/html/file2
SELinux User:Role:Type:Level File
```
### SELinuxモードの変更
```bash
getenforce
Enforcing

setenforce 0

getenforce
Permissive

setenforce Enforcing

getenforce
Enforcing
```

### SELinux コンテキストの変更
```bash
chcon -t httpd_sys_content_t /virtual
ls -Zd /virtual
unconfined_u:object_r:httpd_sys_content_t:s0 /virtual

restorecon -v /virtual
ls -Zd /virtual
unconfined_u:object_r:default_t:s0 /virtual
```

### SELinuxファイルコンテキストの一覧
```bash
semanage fcontext -l
semanage fcontext -a -t httpd_sys_content_t '/virtual(/.*)?'
```

### 演習用
エラーを確認
```bash
less /var/log/messages
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
sealert -l 35c9e452-2552-4ca3-8217-493b72ba6d0b
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
audit.logを検索
```bash
ausearch -m AVC -ts recent
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
ls -dZ /lab-content /var/www/html
 unconfined_u:object_r:default_t:s0 /lab-content
system_u:object_r:httpd_sys_content_t:s0 /var/www/html
```
コンテキストをデフォルトに戻す
```bash
semanage fcontext -a \
-t httpd_sys_content_t '/lab-content(/.*)?'

restorecon -R /lab-content/
```


---
## プロセスの強制終了

| シグナル        | 名前 | 定義                                                                                                                                                                                                                       |
| --------------- | ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1               | HUP  | Hangup: ターミナルの制御プロセスの終了を報告します。また、終了せず にプロセスの再初期化 (設定のリロード) を要求します。                                                                                                    |
| 2               | INT  | Keyboard interrupt:プログラムが終了します。ブロックすることも処 理することもできます。INTR (Interrupt) キーシーケンス (Ctrl+c) を押し て送信します。                                                                       |
| 3               | QUIT | Keyboard quit: SIGINT に類似。終了時にプロセスダンプを追加しま す。QUIT キーシーケンス (kbd:[Ctrl+\]) を押して送信します。                                                                                                 |
| 9               | KILL | Kill, unblockable:突然プログラムが終了します。ブロックも無視も 処理も不可能であり、致命的な状態で使用します。                                                                                                              |
| 15 デ フォル ト | TERM | Terminate: プログラムが終了します。SIGKILL と異なり、ブロック、無 視、または処理が可能です。プログラムに終了を要求する「クリーン」方 法。これにより、プログラムは終了前に重要な操作とセルフクリーンアッ プを完了できます。 |
| 18              | CONT | Continue: 停止した場合、再開するプロセスに送信されます。ブロックは 不可能です。処理した場合でも、常にプロセスを再開します。                                                                                                |
| 19              | STOP | Stop, unblockable:プロセスを一時停止します。ブロックすることも 処理することもできません。                                                                                                                                  |
| 20              | TSTP | Keyboard stop: SIGSTOP と異なり、ブロック、無視、または処理が可 能です。一時停止キーシーケンス (Ctrl+z) を押して送信します。                                                                                               |

---

## man コマンドの使い方

```bash
man ls
```

- ↑ / ↓: 上/下に一行スクロール
- PgUp / PgDn: 上/下に一画面スクロール
- q: man プログラムを終了
- /: 検索（検索後、n で次に進む、N で前に戻る）

---

## tuned

```bash
dnf install tuned
systemctl enable --now tuned
tuned-adm active
tuned-adm list
tuned-adm profile throughput-performance
tuned-adm active
```

## nice

nice 値のデフォルトは 10<br>
非特権ユーザは 19 までしか上げられない。

```bash
nice sleep 60 &
[1] 11

ps -o pid,comm,nice 11
PID COMMAND          NI
 11 sleep            10
```

```bash
nice -n 15 sleep 60 &
[1] 11

ps -o pid,comm,nice 11
PID COMMAND          NI
 11 sleep            15

renice -n 19 11
```

---

## crontab

- クーロンタブの編集

  ```bash
  crontab -e
  ```

- 一覧

  ```bash
  crontab -l
  ```

- 削除

  ```bash
  crontab -r
  ```

---

# パッケージのインストールと更新

## DNF

DNF は YUM の後継。

- パッケージの検索

  ```bash
  dnf list 'http*'
  dnf search all 'web server'
  ```

  - list: パッケージ名の検索
  - search all: 名前、サマリー、説明フィールドから検索

- アップデート
  ```bash
  dnf update
  ```
- インストール
  ```bash
  dnf install httpd
  ```
- アンインストール
  ```bash
  dnf remove httpd
  ```
- グループの検索
  ```bash
  dnf group list
  dnf group info "RPM Development Tools"
  ```
- インストール
  ```bash
  dnf group install "RPM Development Tools"
  ```

---

# ストレージの管理

## 手動マウント

- mount

  ```bash
  lsblk
  mount /dev/vda4/ /mnt/data
  ```

  UUID を取得して指定する方法もある。

  ```bash
  lsblk -fp
  mount UUID="efd314d0-b56e-45db-bbb3-3f32ae98f652" /mnt/data
  ```

- umount
  ```bash
  umount /mnt/data
  ```

## パーティションの作成

パーティションテーブルの表示

```bash
parted /dev/vda print
```

パーティショニングセッションの作成

```bash
parted /dev/vda
```

新しいディスクにパーテションテーブルを書き込む

```bash
# MBR
parted /dev/vdb mklabel msdos
# GPT
parted /dev/vdb mklabel gpt
```

パーティション作成後に認識されるまで待つコマンド

```bash
udevadm settle
```

パーティションの削除

```bash
[root@host ~]# parted /dev/vdb
GNU Parted 3.4
Using /dev/vdb
Welcome to GNU Parted! Type 'help' to view a list of commands. (parted)
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 5369MB
Sector size (logical/physical): 512B/512B Partition Table: gpt
Disk Flags:
Number Start End Size File system Name Flags 1 1049kB 1000MB 999MB xfs usersdata
(parted) rm 1
(parted) quit
Information: You may need to update /etc/fstab.
[root@host ~]#
```

## ファイルシステムの作成

```bash
# extf
mkfs.ext4 /dev/vdb1
# XFS
mkfs.xfs /dev/vdb1
```

## スワップ領域の作成

```bash
[root@host ~]# parted /dev/vdb
GNU Parted 3.4
Using /dev/vdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 5369MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:
Number  Start   End     Size    File system  Name  Flags
 1      1049kB  1001MB  1000MB               data
(parted) mkpart
Partition name? []? swap1
File system type? [ext2]? linux-swap
Start? 1001MB
End? 1257MB
(parted) print
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 5369MB
Sector size (logical/physical): 512B/512B Partition Table: gpt
Disk Flags:
Number  Start   End     Size    File system     Name   Flags
 1      1049kB  1001MB  1000MB                  data
2 1001MB 1257MB 256MB linux-swap(v1) swap1
(parted) quit
Information: You may need to update /etc/fstab.
[root@host ~]#
```

### スワップ領域の有効化

すぐに有効化されない。`swapon`する必要がある。無効化は`swapoff`。

```bash
swapon --show
swapon /dev/vdb2
swapon --show
swapoff /dev/vdb2
swapon --show
```

### スワップ領域の確認

```bash
free
              total        used        free      shared  buff/cache   available
Mem:        8142192     1890440      907984      379232     5343768     5684432
Swap:       1048572        1032     1047540
```

## ボリュームの作成

```bash
pvcreate /dev/vdb1 /dev/vdb2
vgcreate vg01 /dev/vdb1 /dev/vdb2
lvcreate -n lv01 -L 300M vg01
```

重複除外と圧縮を使用した論理ボリュームの作成

```bash
dnf install vdo kmod-kvdo
lvcreate --type vdo --name vdo-lv01 --size 5G vg01
mkdf -t xfs /dev/vg01/lv01
mkdir /mnt/data/
mount /mnt/data/
```

一覧表示

```bash
vgdisplay vg01
lvdisplay /dev/bf01/lv01
```

拡張

```bash
# 物理ボリュームの追加
vgextend vg01 /dev/vdb3
# 論理ボリュームの拡張
lvextend -L + 5000M /dev/vg01/lv01
# XFSファイルシステムを論理ボリュームの最大値で確保する
xfs_growfs /mnt/data
# EXT4ファイルシステムを論理ボリュームの最大値で確保する
resize2fs /dev/vg01/lv01
```

スワップの拡張

```bash
swapoff -v /dev/vg01/swap
lvextend -L +300M /dev/vg01/swap
mkswap /dev/vg01/wsap
swapon /dev/vg01/swap
```

ボリュームグループの削減

```bash
pvmove /dev/vdb3
vgreduce vg01 /dev/vdb3
```

削除

```bash
lvremove /dev/vg01/lv01
vgremove vg01
pvremove /dev/vdb1 /dev/vdb2
```

---

## Stratis

いったん割愛

---

# コントロールサービスとブートプロセス

## systemd

- 一覧

  ```bash
  systemctl list-units --type=service
  systemctl list-units --type=service --all
  ```

- 状態確認

  ```bash
  systemctl status sshd.service
  systemctl is-active sshd.service
  systemctl is-enabled sshd.service
  systemctl is-failed sshd.service
  ```

- 再起動とリロード

  ```bash
  systemctl stop sshd.service
  systemctl start sshd.service
  systemctl restart sshd.service
  ```

- ブート時に自動で起動するように
  ```bash
  systemctl enable sshd.service
  # --now は enable + restart と等価
  systemctl enable --now sshd.service
  ```

---

# ログの分析と保存

## journalctl

あとでまとめる

---

## 時刻の設定

```bash
tzselect
```

# ネットワークの管理

## IP アドレスの表示

```bash
ip link show
ip addr show ens3
ip -s link show ens3
```

## ルーティングテーブルの表示

```bash
ip route
ip -6 route
```

## トラフィックルートの追跡

```bash
tracepath access.redhat.com
```

## ポートとサービス間のトラブルシューティング

netstat の代わり

```bash
ss -ta
```

---

# コンテナ

## podman

対応表
|docker|podman|
|---|---|
|docker login docker.io|podman login docker.io|
|docker build|podman build|
|docker run|podman run|
|docker images|podman images|
|docker ps|podman ps|
|docker inspect|podman inspect|
|docker pull|podman pull|
|docker cp|podman cp|
|docker exec|podman exec|
|docker rm|podman rm|
|docker rmi|podman rmi|
|docker search|podman search|

podman のインストール

```bash
dnf install container-tools
```

## ネットワークの作成

```bash
# 作成
podman network create --subnet 10.89.1.0/24 --gateway 10.89.1.1 frontend
#　アタッチ
podman run -d --name db_01 --network frontend registry.lab.example.com/rhel8/mariadb-105
## もしくは
podman run -d --name db_01 registry.lab.example.com/rhel8/mariadb-105
podman network connect frontend db_01
```

---
## SSHの設定
ホスト側の設定ファイルは`/etc/ssh/sshd_conf`
```conf
PermitRootLogin no
Port 22
ClientAliveInterval 600
ClientAliveCountMax 0
```
