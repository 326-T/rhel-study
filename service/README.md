# コントロールサービスとブートプロセス

## systemd

- 一覧

  ```shell
  systemctl list-units --type=service
  systemctl list-units --type=service --all
  ```

- 状態確認

  ```shell
  systemctl status sshd.service
  systemctl is-active sshd.service
  systemctl is-enabled sshd.service
  systemctl is-failed sshd.service
  ```

- 再起動とリロード

  ```shell
  systemctl stop sshd.service
  systemctl start sshd.service
  systemctl restart sshd.service
  ```

- ブート時に自動で起動するように
  ```shell
  systemctl enable sshd.service
  # --now は enable + restart と等価
  systemctl enable --now sshd.service
  ```
