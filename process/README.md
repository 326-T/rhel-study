# プロセスと最適化

## プロセスの強制終了

| シグナル        | 名前 | 定義                                                                                                                                                                                                                       |
| --------------- | ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1               | HUP  | Hangup: ターミナルの制御プロセスの終了を報告します。また、終了せず にプロセスの再初期化 (設定のリロード) を要求します。                                                                                                    |
| 2               | INT  | Keyboard interrupt:プログラムが終了します。ブロックすることも処 理することもできます。INTR (Interrupt) キーシーケンス (Ctrl+c) を押し て送信します。                                                                       |
| 3               | QUIT | Keyboard quit: SIGINT に類似。終了時にプロセスダンプを追加しま す。QUIT キーシーケンス (kbd:[Ctrl+\]) を押して送信します。                                                                                                 |
| 9               | KILL | Kill, unblockable:突然プログラムが終了します。ブロックも無視も 処理も不可能であり、致命的な状態で使用します。                                                                                                              |
| 15 デ フォル ト | TERM | Terminate: プログラムが終了します。SIGKILL と異なり、ブロック、無 視、または処理が可能です。プログラムに終了を要求する「クリーン」方 法。これにより、プログラムは終了前に重要な操作とセルフクリーンアッ プを完了できます。 |
| 18              | CONT | Continue: 停止した場合、再開するプロセスに送信されます。ブロックは 不可能です。処理した場合でも、常にプロセスを再開します。                                                                                                |
| 19              | STOP | Stop, unblockable:プロセスを一時停止します。ブロックすることも 処理することもできません。                                                                                                                                  |
| 20              | TSTP | Keyboard stop: SIGSTOP と異なり、ブロック、無視、または処理が可 能です。一時停止キーシーケンス (Ctrl+z) を押して送信します。                                                                                               |

---

## tuned

```bash
dnf install tuned
systemctl enable --now tuned
tuned-adm active
tuned-adm list
tuned-adm profile throughput-performance
tuned-adm active
```

---

## nice

nice 値のデフォルトは 10<br>
非特権ユーザは 19 までしか上げられない。

```bash
nice sleep 60 &
[1] 11

ps -o pid,comm,nice 11
PID COMMAND          NI
 11 sleep            10
```

```bash
nice -n 15 sleep 60 &
[1] 11

ps -o pid,comm,nice 11
PID COMMAND          NI
 11 sleep            15

renice -n 19 11
```
