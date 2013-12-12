# Pythonと文字コード
## ソースの文字コード指定
ソースの文字コードを指定したい場合には，ソースの先頭に以下のように書きます(utf-8指定の例)．

```
# -*- coding: utf-8 -*-
```

## Python 2系と3系の違い
Python 2系と，Python 3系では文字列の扱いが異なるので注意．<br/>
Python 2系で文字列を扱いたい場合には，unicode型に変換する．

### Python 2系
Python 2系では，str(文字列)型とunicode型が存在する．
##### str型
入力された文字の型をそのまま保持する．<br/>
中身はバイトコードになっている(各種動作は以下の通り)．

```python
>>> "あ"
'\xe3\x81\x82'
>>> len("あ")
3
```

##### unicode型
一般的に想定されるいわゆる「文字列」はこちらを指す．<br/>
str型→unicode型にdecodeし，unicode型→str型にencodeして扱う．

```python
>>> u"あ"
u'\u3042'
>>> len(u"あ")
1
>>> "あ".decode("utf-8")
u'\u3042'
>>> u"あ".encode("utf-8")
'\xe3\x81\x82'
```

### Python 3系
##### str型
内部はunicode型になっている．

```python
>>> "あ"
'あ'
>>> len("あ")
1
```

## Python 2系で日本語ファイルI/O
codecsモジュールを利用して，ファイル中の文字列をunicode型に変換する．

```python
>>> import codecs
>>> fin = codecs.open("file_name", "r", "utf-8")
>>> for line in fin:
...  type(line)
...
<type 'unicode'>
```