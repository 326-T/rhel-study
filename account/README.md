# アカウント

## アカウント設定

### ログインやパスワードの設定

```bash
$ cat /etc/shadow
user03:$6$CSsXsd3rwghsdfarf:17933:0:99999:7:2:18113:
```

- user03: ユーザーアカウントの名前。
- $6$CSsXsd3rwghsdfarf: ユーザーの暗号化されたパスワード。
- 17933: パスワードが最後に変更されたエポックからの⽇数。エポックは UTC タイムゾーンの 1970-01-01。
- 0: ユーザーが最後にパスワードを変更してから、再度パスワードを変更できるようになるまでの最⼩⽇数。
- 99999: パスワードが期限切れになるまでにパスワードを変更せずに済む最⼤⽇数。空のフィールドは、パスワードが期限切れにならないことを意味します。
- 7: パスワードが期限切れになることをユーザーに警告する期限前の⽇数。
- 2: パスワードが期限切れになった⽇から、アカウントが⾃動的にロックされるまで、アクティビティがない⽇数。
- 18113: エポック以降でアカウントが期限切れになる⽇。空のフィールドは、アカウントが期限切れにならないことを意味します。
- この最後のフィールドは通常は空で、将来使⽤するために予約されています。

user01 のパスワードポリシーを変更する。

```bash
$ chage -m 0 -M 90 -W 7 -I 14 user01
```

パスワードを変更する

```bash
$ passwd user01
```

90 日後にパスワードを有効期限切れになるようにする

```bash
$ date -d "+90 days" +%F
2023-12-31
$ chage -E 2023-12-31 user01
```

全体に適用する。`/etc/login.defs`

---

### 特権権限をつける

- `user01`に特権権限をつける

  `/etc/sudoers.d/user01`

  ```
  user01 ALL=(ALL) ALL
  ```

- `consultants`グループに特権権限をつける

  `/etc/sudoers.d/consultants`

  ```
  %consultants ALL=(ALL) ALL
  ```

---

## ファイルアクセス

setgid(2)とあったら以下みたいにする。
setuid(4), stickybit は 0。

```
chmod -R 2770 techdocs
```

---

# ACL

もしかしたら古い試験範囲かも

- ユーザにパーミッション付与
  ```bash
  $ setfacl -m u:user:rwx /path/to/file
  $ setfacl -Rm u:user:rwx /path/to/directory
  ```
- グループにパーミッション付与
  ```bash
  $ setfacl -m g:group:rwx /path/to/file
  $ setfacl -Rm g:group:rwx /path/to/directory
  ```
- ユーザからパーミッション削除
  ```bash
  $ setfacl -x u:user /path/to/file
  ```
- 全てのユーザからパーミッション削除
  ```bash
  $ setfacl -b /path/to/file
  ```
