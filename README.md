1つのサーバに複数のサイトを入れている場合、`/etc/nginx/sites-available`に、各サイト用の設定ファイルを個別につくって、`/etc/nginx/sites-enabled`に反映させたいファイルのリンクをはって、Nginxを再起動させるというのが一般的な管理方法だと思ってますが、sites-availableにファイルを作って、sites-enabledにリンクはって、設定間違ってたからまたavailableのファイルを開いて修正して、リンク張ろうと思ったらファイル名忘れて、availableのファイル一覧確認してとかやってるとつかれるので、ツールを作ってみました。

# 環境
- zshを使っています。（bashでも多分動くと思います）
- Ubuntu16.10で動作確認しました。

# 出来ること
- sites-availableのファイル一覧をすぐに確認できる
- sites-enabledのファイル一覧もすぐに確認できる
- 設定ファイルをどこからでもvimで開ける
- enabledへの反映もどこからでもリンクできる
- enabledのリンク削除もすぐできる
- availableへの追加も簡単にできる
- nginxの設定ファイルチェックも短いコマンドでできる
- nginxの再起動も短いコマンドでできる
- 設定ファイルの削除（リンク・ファイル削除）もすぐできる
- enabledのリンク全削除もすぐできる
- certbotを使ったssl取得も簡単にできる（準備作業は必要）

# インストール

gitから落として、PATH通ってるフォルダ（/usr/local/binを想定）に置きます。

```
$ git clone https://github.com/edo1z/ngcon.git
$ sudo mv ngcon/ngcon /usr/local/bin
$ sudo rm -rf ngcon
```

# 使い方

基本的にroot権限が必要です。

## 簡単なヘルプ

```
$ ngcon
commands [lsa, lse, add, new, a, e, test, re, vim, rm, rmall, delete, get_ssl]
```

## site-availableのファイル一覧を確認

```
$ ngcon lsa
```

## site-enabledのファイル一覧を確認

```
$ ngcon lse
```

## site-enabledにファイルを反映（availableのリンクをはる）

```
$ ngcon add [conf file name]
```

## site-availableにファイルを追加

既存ファイル(base file)をコピーして新規ファイルを(new file)という名前で作成します。作成後、new fileをvimで開きます。

```
$ ngcon new [base file name] [new file name]
```

## site-availableのパスを表示します

```
$ ngcon a
```

## site-enabledのパスを表示します

```
$ ngcon e
```

## nginxの設定ファイルチェックをします

```
$ ngcon test
```

## nginxを再起動します

```
$ ngcon re
```

## vimで設定ファイルを開きます

```
$ ngcon vim [conf file name]
```

## site-enabledのリンクを削除します

```
$ ngcon rm [conf file name]
```

## site-enabledのリンクを全部削除します

```
$ ngcon rmall
```

## 設定ファイルを削除します。

enabledからもavailableからも削除します。

```
$ ngcon delete [conf file name]
```

## Let's Encryptの証明書を取得します。（おまけ）

### certbotのインストール

certbotが必要ですので、このコマンドを使う場合、事前にインストールが必要です。

```
$ sudo apt-get install certbot
```

### Nginx設定ファイルの作成

下記のような簡単な設定ファイルを、`t_forcert`という名前(ファイル名は変えられます)で、site-abailableに入れておきます。(server_nameは、SSL証明書を取得したいドメインにします)。また、/usr/share/nginx/html配下にindex.htmlが存在する必要があります。（Nginxのデフォルト状態ならあります。）

```
server {
  listen 80;
  server_name example.com;
  index index.html;
  root /usr/share/nginx/html;
}
```
### コマンド実行

```
$ ngcon get_ssl example.com
```

これで取得完了できます。やっているのは、上記設定ファイルを反映してNginxを再起動させて、`certbot certonly`コマンドを実行しています。証明書取得後に、上記設定ファイルを削除して、再度Nginxを再起動しています。

### 補足

--standaloneを使ってインストールできるなら、逆にめんどくさいだけだと思います。すでにサイトが動いていて、Nginxをなるべく止めたくないとかの場合に、ある程度有用かもしれません。

### LICENSE

This software is released under the MIT License, see LICENSE.txt.


