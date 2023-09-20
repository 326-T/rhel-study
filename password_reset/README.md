# root パスワードのリセット

1. rescue カーネルを起動する
   1. 再起動
   1. `Esc`を押して GRUB 画面を表示する
   1. rescue をハイライトして`e`を押す
   1. linux と表示されている行の最後に`rd.break`を追記
   1. `Ctrl + x`を押して変更した設定でブート
1. `/sysroot`を読み込みと書き込み可能にして再マウント
   ```bash
   $ mount -o remount,rw /sysroot
   ```
1. `chroot jail`に移動
   ```bash
   $ chroot /sysroot
   ```
1. root のパスワードを変更
   ```bash
   $ passwd root
   ```
1. SELinux の完全な再ラベル付けが実行されるように設定
   ```bash
   $ touch /.autolabel
   ```

---

# boot プロセスのデバッグ

mount が上手くいってないことが出題としては多そう。

1. rescue カーネルを起動する
   1. 再起動
   1. `Esc`を押して GRUB 画面を表示する
   1. rescue をハイライトして`e`を押す
   1. linux と表示されている行の最後に`systemd.unit=emergency.target`を追記
   1. `Ctrl + x`を押して変更した設定でブート
1. マウントを確認
   ```bash
   $ mount
   ```
1. ReadOnly が問題なら再マウントする
   ```bash
   $ mount -o remount,rw /
   ```
1. 不正なマウントポイントなら`etc/fstab`から削除する
   ```bash
   $ mount -a
   $ vim /etc/fstab
   ```
