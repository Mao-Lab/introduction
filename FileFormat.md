# 使いやすいファイルフォーマット
## 基本的な.datファイル

* テーブル構造を意識
    - 1行のデータフォーマットは統一されているか
    - カラム同士の区切り文字は汎用的な語になってしまっていないか
        * その語を使ってsplitした際に，欲しいデータは常に得られそうか

* cut, sort, uniq, awk といったコマンドを併用しやすい形式になっているか
    - 各種コマンドについては，該当まとめを参照
    - 数値データは一つのデータとして分離していた方が，後々計算に利用しやすい
        * e.g. name \t data_name:score → name \t data_name \t score

* 自然言語処理のデータはスパースなものが多いため，往々にして転置インデックスなども有効

## jsonファイル
TSV(タブ区切りテキスト)やCSV(カンマ区切りテキスト)よりも複雑なデータ形式として残したい場合(例えば辞書データをそのまま保存したい場合)，jsonが便利である．
json形式はプログラミング言語との親和性が高く，更に人間が見てもわかりやすいというメリットがある．
Pythonにも標準モジュールとしてjsonが実装されている．<br/>
以下に日本語を含む辞書データをPytonを使ってファイルに保存する例を挙げる．

```python
import json

en2ja = {"red": u"赤", "blue": u"青", "yellow": u"黄", "green": {"light_green": u"黄緑", "dark_green": u"深緑"}}
print json.dumps(en2ja, ensure_ascii=False).encode("utf-8")
```

このPythonコードをjson_test.pyという名前で保存し実行すると，以下のようなデータが得られる．

```sh
$ python json_test.py > test.json
$ cat test.json
{"red": "赤", "blue": "青", "yellow": "黄", "green": {"light_green": "黄緑", "dark_green": "深緑"}}
```

jsonファイルを整形したデータが欲しい場合は，jqコマンドにデータをパイプで渡せば良い．<br/>
[jq is a lightweight and flexible command-line JSON processor](http://stedolan.github.io/jq/)<br/>
※補足ですが，jqコマンドには他にも便利な機能がついているので，良かったら自分で調べてみて下さい．

```sh
$ cat test.json | jq .
{
  "red": "赤",
  "yellow": "黄",
  "green": {
    "light_green": "黄緑",
    "dark_green": "深緑"
  },
  "blue": "青"
}
```

更に，このデータをPythonで読み込む場合には以下のように書く．

```python
import codecs
import json
fin = codecs.open("test.json", "r", "utf-8")

en2ja = dict(json.load(fin))
print en2ja
```

このPythonコードをjson_test_2.pyという名前で保存し実行すると，以下のようになり，データをdict型として読み込めていることが確認できた．

```sh
$ python json_test_2.py
{u'blue': u'\u9752', u'green': {u'dark_green': u'\u6df1\u7dd1', u'light_green': u'\u9ec4\u7dd1'}, u'yellow': u'\u9ec4', u'red': u'\u8d64'}
```
