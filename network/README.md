# ネットワークの管理

## ネットワーク情報の確認

### IP アドレスの表示

```bash
$ ip link show
$ ip addr show ens3
$ ip -s link show ens3
```

### ルーティングテーブルの表示

```bash
$ ip route
$ ip -6 route
```

### トラフィックルートの追跡

```bash
$ tracepath access.redhat.com
```

### ポートとサービス間のトラブルシューティング

netstat の代わり

```bash
$ ss -ta
```

---

## NetworkManager サービス

### 情報の表示

```bash
# デバイス情報
$ nmcli dev status
# 接続情報
$ nmcli con show
```

---

## nmcli

### hostname

```bash
$ sudo hostname new_hostname
$ sudo hostnamectl set-hostname new_hostname
```

### ip address

```bash
$ sudo nmcli con mod eth0 ipv4.address "192.168.1.2/24"
$ sudo nmcli con down eth0 && sudo nmcli con up eth0
```

### gateway

```bash
$ sudo nmcli con mod eth0 ipv4.dns "8.8.8.8,8.8.4.4"
$ sudo nmcli con down eth0 && sudo nmcli con up eth0
```

### DNS

```bash
$ sudo nmcli con mod eth0 ipv4.gateway "192.168.1.1"
$ sudo nmcli con down eth0 && sudo nmcli con up eth0
```
