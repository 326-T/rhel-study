# NFS について

## エクスポートされた NFS ディレクトリのマウント

```bash
mount -t nfs -o rw,sync server:/export /mountpoint
```

### 永続マウント

```
$ vim /etc/fstab

server:/export /mountpoint nfs rw,soft 0 0
```

---

## 自動マウント

1. 自動マウンタサービスの設定

   ```bash
   $ sudo dnf install autofs nfs-utils
   ```

1. マスターマップの作成

   ```bash
   $ sudo vim /etc/auto.master.d/demo.autofs

   /shares /etc/auto.demo
   ```

1. 関節マップの作成

   ```bash
   sudo vim /etc/auto.demo

   work -rw,sync serverb:/shares/work
   # wildcardも可能
   * -rw,sync serverb:/shares/work
   ```

1. 自動マウンタサービスの開始

   ```bash
   $ sudo systemctl enable --now autofs
   ```
