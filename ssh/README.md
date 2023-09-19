## SSH の設定

ホスト側の設定ファイルは`/etc/ssh/sshd_conf`

```conf
PermitRootLogin no
Port 22
ClientAliveInterval 600
ClientAliveCountMax 0
```

### SSH の公開鍵をコピー

```shell
ssh-copy-id root@192.168.1.56
```
