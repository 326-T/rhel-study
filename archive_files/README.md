# アーカイブ、圧縮と解凍

## tar

```bash
# アーカイブ
tar cvf archive.tar folder/
# 解凍
tar xvf archive.tar
# アーカイブして圧縮
tar czvf archive.tar.gz folder/
# 解凍してアーカイブ解除
tar xzvf archive.tar.gz
```

tar のオプション
|オプション|説明|
|---|---|
|v|詳細|
|f|ファイル名指定|
|c|アーカイブを作成|
|x|アーカイブを展開|
|z|gzip を使用|
|j|bzip2 を使用|

## gzip

```bash
# 圧縮
gzip archive.tar
# 解凍
gunzip archive.tar.gz
```
