# コントロールサービスとブートプロセス

## systemd

- 一覧

  ```bash
  systemctl list-units --type=service
  systemctl list-units --type=service --all
  ```

- 状態確認

  ```bash
  systemctl status sshd.service
  systemctl is-active sshd.service
  systemctl is-enabled sshd.service
  systemctl is-failed sshd.service
  ```

- 再起動とリロード

  ```bash
  systemctl stop sshd.service
  systemctl start sshd.service
  systemctl restart sshd.service
  ```

- ブート時に自動で起動するように
  ```bash
  systemctl enable sshd.service
  # --now は enable + restart と等価
  systemctl enable --now sshd.service
  ```
