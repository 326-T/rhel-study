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
