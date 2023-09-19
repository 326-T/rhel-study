# パッケージのインストールと更新

## DNF

DNF は YUM の後継。

- パッケージの検索

  ```bash
  $ dnf list 'http*'
  $ dnf search all 'web server'
  ```

  - list: パッケージ名の検索
  - search all: 名前、サマリー、説明フィールドから検索

- アップデート
  ```bash
  $ dnf update
  ```
- インストール
  ```bash
  $ dnf install httpd
  ```
- アンインストール
  ```bash
  $ dnf remove httpd
  ```
- グループの検索
  ```bash
  $ dnf group list
  $ dnf group info "RPM Development Tools"
  ```
- インストール
  ```bash
  $ dnf group install "RPM Development Tools"
  ```
- 履歴の確認
  ```bash
  $ dnf history
  ```

## DNF にレポジトリを登録する

レポジトリの一覧の確認

```bash
$ dnf repolist all
```

レポジトリの登録

```bash
$ dnf config-manager --add-repo="https://dl.fedoraproject.org/pub/epel/9/Everything/x86_64/"
```

レポジトリに関する修正<br>
例えば gpgcheck=0 に設定するとか。

```bash
$ vim /etc/yum.repos.d/dl.fedoraproject.org/pub/epel/9/Everything/x86_64.repo
```
