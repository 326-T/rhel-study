## man コマンドの使い方

```bash
man ls
```

- ↑ / ↓: 上/下に一行スクロール
- PgUp / PgDn: 上/下に一画面スクロール
- q: man プログラムを終了
- /: 検索（検索後、n で次に進む、N で前に戻る）

## 時刻の設定

```bash
tzselect
```

## ファイル容量を知りたい

```bash
$ df
Filesystem     512-blocks      Used Available Capacity iused      ifree %iused  Mounted on
/dev/disk3s1s1  965595304  25348232 714783480     4%  348619 3573917400    0%   /
devfs                 404       404         0   100%     702          0  100%   /dev
/dev/disk3s6    965595304        40 714783480     1%       0 3573917400    0%   /System/Volumes/VM
/dev/disk3s2    965595304  18685552 714783480     3%    1063 3573917400    0%   /System/Volumes/Preboot
/dev/disk3s4    965595304   1200072 714783480     1%     269 3573917400    0%   /System/Volumes/Update
/dev/disk1s2      1024000     12328    987824     2%       1    4939120    0%   /System/Volumes/xarts
/dev/disk1s1      1024000     12616    987824     2%      33    4939120    0%   /System/Volumes/iSCPreboot
/dev/disk1s3      1024000      1512    987824     1%      68    4939120    0%   /System/Volumes/Hardware
/dev/disk3s5    965595304 202095488 714783480    23% 1673515 3573917400    0%   /System/Volumes/Data
map auto_home           0         0         0   100%       0          0  100%   /System/Volumes/Data/home
/dev/disk3s1    965595304  25348232 714783480     4%  356052 3573917400    0%   /System/Volumes/Update/mnt1
```

# ログの分析と保存

## journalctl

あとでまとめる

---

## 初回受験時に詰まったところ

- [x] 画面が操作しづらすぎる。問題文とコンソールを Split View したい
- [x] トラックパッド非対応。有線マウス必須。
- [x] chmod, chown はいいとして、そのディレクトリに後で作られたファイルにも適用するのってどうするんだっけ？
  ```bash
  setfacl -b /group_dir
  setfacl -mR g:group:rwx /group_dir
  ```
- [x] umask でデフォルトのパーミッションを決める
- [x] シェルが使えないユーザとは
      
   ```sh
   sudo useradd -s /sbin/nologin username
   or
   sudo usermod -s /sbin/nologin username
   ```
- [x] ホームディレクトリに対するデフォルトパーミッション<br>
      デフォルトのパーミッションをファイルとフォルダ別で
- [x] `nmcli con mod ens3 ipv4.address 192.168.1.10`
      はダッシュいらない
- [x] pvcreate, vgcreate, lvcreate
- [x] pvcreate できん。vg はパンパン。lsblk でデバイス探すべきだったぽい
- [x] コンテナでデーモンプロセス作る
- [x] autofs
- [x] chronyc, ntp

  デーモンがchronyd、chronycコマンドを使うか、/etc/chrony.confのserverかpool行を編集する。

- [x] 多分できたけど find コマンドの使い方
  ```
  sudo find / -user simone -type f 2>/dev/null | xargs -I {} cp {} /path/to/target_directory/
  ```

- [ ] SELinux のポート付与はできた。ファイルアクセスは多分できた。
- [x] crontab 秒指定できない
- [x] dnf で設定すれば yum も設定される？設定される。
- [x] デフォルトのレポジトリって?

  enabledを見る。
  `/etc/yum.repos.d/~`のファイルで確認して
- [x] tuned-adm の永続化ってどうやるの？

  そもそも揮発性じゃない。<br>
- [ ] デフォルトコンテキストポリシーは正規表現でやるやつ

## わかったけどできてないかも

- [x] find で所有者を絞る
  ```bash
  find / -user simone -type f 2>/dev/null
  ```

## 復習してて次出そうなやつ

- [ ] anacron
- [ ] 一時ファイルの管理
