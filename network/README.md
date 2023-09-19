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
