# SearXNG
SearXNGは無料のインターネットメタ検索エンジンで、さまざまな検索サービスの結果を統合します。ユーザーは追跡されず、解析されることもありません。DifyはすでにSearXNGへのインターフェースを実装しているため、Dify内で直接利用することができます。以下はSearXNGをDifyに統合する手順です：

## 1. Dockerを使用してSearXNGコンテナをインストールする
```
docker run --rm \
             -d -p 8080:8080 \
             -v "${PWD}/searxng:/etc/searxng" \
             -e "BASE_URL=http://0.0.0.0:8080/" \
             -e "INSTANCE_NAME=searxng" \
             searxng/searxng
```
他の方法でSearXNGをインストールしたい場合は、[こちら](https://docs.searxng.org/admin/installation.html)を参照してください。

## 2. settings.ymlを修正する
SearXNGをインストールすると、デフォルトの出力形式はHTML形式になっています。JSON形式を有効にする必要があります。以下の行をsettings.ymlファイルに追加してください。前述の例のように、settings.ymlファイルは${PWD}/searxng/settings.ymlにあります。
```
  # アクセスを拒否する形式を削除し、小文字を使用します。
  # formats: [html, csv, json, rss]
  formats:
    - html
    - json    # <-- この行を追加
```

## 3. DifyにSearXNGを統合する
`ツール > SearXNG > 認証へ行く` でアクセスアドレスを入力します。例えば：http://x.x.x.x:8080

## 4. 完了
使用を開始しましょう！