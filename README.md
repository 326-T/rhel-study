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
