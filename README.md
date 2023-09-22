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
- [ ] chmod, chown はいいとして、そのディレクトリに後で作られたファイルにも適用するのってどうするんだっけ？facl でした。
- [ ] umask でデフォルトのパーミッションを決める
- [ ] シェルが使えないユーザとは
- [ ] ホームディレクトリに対するデフォルトパーミッション<br>
      デフォルトのパーミッションをファイルとフォルダ別で
- [x] `nmcli con mod ens3 ipv4.address 192.168.1.10`
      はダッシュいらない
- [ ] pvcreate, vgcreate, lvcreate
- [ ] pvcreate できん。vg はパンパン。lsblk でデバイス探すべきだったぽい
- [ ] コンテナでデーモンプロセス作る
- [ ] autofs
- [ ] chronyc
- [ ] ntp
- [ ] 多分できたけど find コマンドの使い方
- [ ] SELinux のポート付与はできた。ファイルアクセスは多分できた。
- [x] crontab 秒指定できない
- [ ] dnf で設定すれば yum も設定される？
- [ ] デフォルトのレポジトリって?
- [ ] tuned-adm の永続化ってどうやるの？tuned-adm recommend でデフォルトを指定すればよかったらしい
- [ ] デフォルトコンテキストポリシーは正規表現でやるやつ

## わかったけどできてないかも

- [ ] find で所有者を絞る
- [ ] find で

## 復習してて次出そうなやつ

- [ ] anacron
- [ ] 一時ファイルの管理
