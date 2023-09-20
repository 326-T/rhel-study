# firewalld

1. Firefox を開いて [https://localhost:9090](https://localhost:9090)にアクセス
1. ログイン
1. `Turn on administrative access`をクリック
1. Networking タブをクリック
1. Edit rules and zones をクリック
1. public zone のセクションの Add service をクリック
1. Filter services を使用して https サービスのチェックボックスをクリック。

# SELinux

まずは動かない理由を確認

```bash
$ systemctl status httpd.service
$ sudo sealert -a /var/log/audit/audit.log
```

適切なポートタイプを探す

```bash
$ sudo semanage port -l | grep 'http'
```

ポート 1001/TCP に http_port_t をアタッチ

```bash
$ sudo semanage port -a -t http_port_t -p tcp 1001
```

httpd の有効化

```bash
$ sudo systemctl enable --now httpd
```

firewall について以下の確認

- デフォルトゾーンは public か
  ```bash
  $ firewall-cmd --get-default-zone
  public
  $ sudo firewall-cmd --set-default-zone public
  ```
- ポート 1001/TCP が public に加えられているか
  ```bash
  sudo firewall-cmd --permanent --zone=public --list-all
  sudo firewall-cmd --permanent --zone=public --add-port=1001/TCP
  ```
