# ストレージの管理

## パーティション

`parted`コマンドを使用する。

### GPT パーティションの作成

```bash
$ parted /dev/vdb
(parted) mklabel
新しいディスクラベル? gpt
(parted) mkpart
パーティションの名前? []? userdata
ファイルシステムの種類? [ext2]? xfs
開始? 2048s
終了? 1000MB
(parted) print
...
番号  開始     終了    サイズ  ファイルシステム  名前  フラグ
1    1049kB  1000MB  999MB                 userdata
(parted) quit
$ udevadm settle
```

### MBR パーティションの作成

```bash
parted /dev/vdb
(parted) mklabel
新しいディスクラベル? msdos
(parted) mkpart
パーティションの種類? primary
ファイルシステムの種類? [ext2]? xfs
開始? 2048s
終了? 1000MB
(parted) print
...
番号  開始     終了    サイズ  ファイルシステム  フラグ
1    1049kB  1000MB  999MB
(parted) quit
$ udevadm settle
```

### パーティションの削除

```bash
parted /dev/vdb
(parted) print
...
番号  開始     終了    サイズ  ファイルシステム  フラグ
1    1049kB  1000MB  999MB
(parted) rm 1
(parted) quit
```

---

## ファイルシステムの作成

```bash
# extf
mkfs.ext4 /dev/vdb1
# XFS
mkfs.xfs /dev/vdb1
```

---

## マウント

### mount

```bash
lsblk
mount /dev/vda4/ /mnt/data
```

### umount

```bash
umount /mnt/data
```

### UUID を取得して永続化する方法

```bash
lsblk -fp
echo "UUID=efd314d0-b56e-45db-bbb3-3f32ae98f652 /mnt/data xfs defaults 0 0"
```

---

## スワップ領域の作成

### パーティションの作成

```bash
parted /dev/vdb
(parted) mkpart
パーティションの種類? swap1
ファイルシステムの種類? [ext2]? linux-swap
開始? 2048s
終了? 1000MB
(parted) print
...
番号  開始     終了    サイズ  ファイルシステム  フラグ
1    1049kB  1000MB  999MB
(parted) quit
$ udevadm settle
```

### スワップ領域のフォーマット

```bash
$ mkswap /dev/sdb2
```

### スワップ領域の有効化

すぐに有効化されない。`swapon`する必要がある。無効化は`swapoff`。

```bash
swapon --show
swapon /dev/sdb2
swapon --show
swapoff /dev/sdb2
swapon --show
```

### スワップ領域の確認

```bash
free
              total        used        free      shared  buff/cache   available
Mem:        8142192     1890440      907984      379232     5343768     5684432
Swap:       1048572        1032     1047540
```

---

# 論理ボリューム

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
lvextend -L +5000M /dev/vg01/lv01
# XFSファイルシステムを論理ボリュームの最大値で確保する
xfs_growfs /mnt/data
# EXT4ファイルシステムを論理ボリュームの最大値で確保する
resize2fs /dev/vg01/lv01
```

スワップの拡張

```bash
swapoff -v /dev/vg01/swap
lvextend -L +300M /dev/vg01/swap
mkswap /dev/vg01/swap
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

# stratis

論理ボリュームをより便利にした形。

## stratis のインストール

```bash
$ dns install stratis-cli stratised
$ systemctl enable --now stratised
```

## pool の作成

```bash
$ stratis pool create pool1 /dev/vdb
$ stratis pool list
```

## pool に追加

```bash
$ stratis pool add-data pool1 /dev/vdc
$ stratis blockdev list pool1
```

## pool から filesystem を作成

```bash
$ stratis filesystem create pool1 fs1
$ stratis filesystem list
```

## mount

```bash
$ mount /dev/stratis/pool1/fs1 /mnt/device
```
