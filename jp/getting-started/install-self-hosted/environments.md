# 環境変数説明

### 公共変数

#### CONSOLE_API_URL

コンソールAPIバックエンドのURL。認証コールバックを組み合わせるために使用され、空の場合は同じドメインになります。例：`https://api.console.dify.ai`。

#### CONSOLE_WEB_URL

コンソールのウェブ**フロントエンド**URL。フロントエンドアドレスの一部を組み合わせたり、CORS設定に使用されます。空の場合は同じドメインになります。例：`https://console.dify.ai`

#### SERVICE_API_URL

サービスAPIのURL。**フロントエンド**にサービスAPIのベースURLを表示するために使用されます。空の場合は同じドメインになります。例：`https://api.dify.ai`

#### APP_API_URL

WebアプリAPIのバックエンドURL。**フロントエンド**APIのバックエンドアドレスを宣言するために使用されます。空の場合は同じドメインになります。例：`https://app.dify.ai`

#### APP_WEB_URL

WebアプリのURL。**フロントエンド**にWebアプリAPIのベースURLを表示するために使用されます。空の場合は同じドメインになります。例：`https://api.app.dify.ai`

#### FILES_URL

ファイルプレビューまたはダウンロード用のURLプレフィックス。ファイルプレビューやダウンロードURLをフロントエンドに表示したり、マルチモーダルモデルの入力として使用します。他人による偽造を防ぐため、画像プレビューURLは署名付きで、5分の有効期限があります。

***

### サーバー側

#### MODE

起動モード。dockerによる起動時にのみ有効で、ソースコード起動では無効です。

*   api

    APIサーバーを起動します。
*   worker

    非同期キューのワーカーを起動します。

#### DEBUG

デバッグモード。デフォルトはfalse。ローカル開発時にはこの設定をオンにすることをお勧めします。これにより、モンキーパッチによって発生する問題を防ぐことができます。

#### FLASK_DEBUG

Flaskのデバッグモード。オンにすると、インターフェースでトレース情報が出力され、デバッグが容易になります。

#### SECRET_KEY

セッションクッキーを安全に署名し、データベース上の機密情報を暗号化するためのキー。初回起動時にこの変数を設定する必要があります。`openssl rand -base64 42`を使用して強力なキーを生成できます。

#### DEPLOY_ENV

デプロイ環境。

*   PRODUCTION（デフォルト）

    プロダクション環境。
*   TESTING

    テスト環境。フロントエンドページにはテスト環境を示す明確な色の識別が表示されます。

#### LOG_LEVEL

ログ出力レベル。デフォルトはINFO。プロダクション環境ではERRORに設定することをお勧めします。

#### MIGRATION_ENABLED

trueに設定した場合、コンテナ起動時に自動的にデータベースのマイグレーションが実行されます。dockerによる起動時にのみ有効で、ソースコード起動では無効です。ソースコード起動の場合、apiディレクトリで手動で`flask db upgrade`を実行する必要があります。

#### CHECK_UPDATE_URL

バージョンチェックポリシーを有効にするかどうか。falseに設定した場合、`https://updates.dify.ai`を呼び出してバージョンチェックを行いません。現在、国内から直接CloudFlare Workerのバージョンインターフェースにアクセスできないため、この変数を空に設定すると、このインターフェースの呼び出しをブロックできます。

#### コンテナ起動関連設定

dockerイメージまたはdocker-composeによる起動時にのみ有効です。

*   DIFY_BIND_ADDRESS

    APIサービスのバインドアドレス。デフォルト：0.0.0.0、すべてのアドレスからアクセス可能にします。
*   DIFY_PORT

    APIサービスのバインドポート番号。デフォルト5001。
*   SERVER_WORKER_AMOUNT

    APIサービスのServer worker数。すなわちgevent workerの数。公式：`CPUのコア数 x 2 + 1`。詳細はこちら：https://docs.gunicorn.org/en/stable/design.html#how-many-workers
*   SERVER_WORKER_CLASS

    デフォルトはgevent。Windowsの場合、syncまたはsoloに切り替えることができます。
*   GUNICORN_TIMEOUT

    リクエスト処理のタイムアウト時間。デフォルト200。360に設定することをお勧めします。これにより、長時間のSSE接続をサポートできます。
*   CELERY_WORKER_CLASS

    `SERVER_WORKER_CLASS`と同様に、デフォルトはgevent。Windowsの場合、syncまたはsoloに切り替えることができます。
*   CELERY_WORKER_AMOUNT

    Celery workerの数。デフォルトは1。必要に応じて設定します。
*   HTTP_PROXY

    HTTPプロキシのアドレス。国内からOpenAIやHuggingFaceにアクセスできない問題を解決するために使用されます。注意：プロキシがホストマシンにデプロイされている場合（例：`http://127.0.0.1:7890`）、このプロキシアドレスはローカルモデルに接続する場合と同様に、dockerコンテナ内のホストマシンアドレスを使用する必要があります（例：`http://192.168.1.100:7890`または`http://172.17.0.1:7890`）。
*   HTTPS_PROXY

    HTTPSプロキシのアドレス。国内からOpenAIやHuggingFaceにアクセスできない問題を解決するために使用されます。HTTPプロキシと同様に設定します。

#### データベース設定

データベースにはPostgreSQLを使用します。public schemaを使用してください。

* DB_USERNAME：ユーザー名
* DB_PASSWORD：パスワード
* DB_HOST：データベースホスト
* DB_PORT：データベースポート番号。デフォルト5432
* DB_DATABASE：データベース名
* SQLALCHEMY_POOL_SIZE：データベース接続プールのサイズ。デフォルトは30接続。必要に応じて増やせます。
* SQLALCHEMY_POOL_RECYCLE：データベース接続プールのリサイクル時間。デフォルト3600秒。
* SQLALCHEMY_ECHO：SQLを出力するかどうか。デフォルトはfalse。

#### Redis 設定

このRedis設定はキャッシュおよび対話時のpub/subに使用されます。

* REDIS_HOST：Redisホスト
* REDIS_PORT：Redisポート。デフォルト6379
* REDIS_DB：Redisデータベース。デフォルトは0。セッションRedisおよびCeleryブローカーとは異なるデータベースを使用してください。
* REDIS_USERNAME：Redisユーザー名。デフォルトは空
* REDIS_PASSWORD：Redisパスワード。デフォルトは空。パスワードを設定することを強くお勧めします。
* REDIS_USE_SSL：SSLプロトコルを使用して接続するかどうか。デフォルトはfalse

#### Celery 設定

*   CELERY_BROKER_URL

    フォーマットは以下の通りです。

    <pre><code><strong>redis://&#x3C;redis_username>:&#x3C;redis_password>@&#x3C;redis_host>:&#x3C;redis_port>/&#x3C;redis_database>
    </strong><strong>  
    </strong></code></pre>

    例：`redis://:difyai123456@redis:6379/1`
*   BROKER_USE_SSL

    trueに設定した場合、SSLプロトコルを使用して接続します。デフォルトはfalse。

#### CORS 設定

フロントエンドのクロスオリジンアクセスポリシーを設定するために使用します。

*   CONSOLE_CORS_ALLOW_ORIGINS

    コンソールのCORSクロスオリジンポリシー。デフォルトは`*`、すべてのドメインがアクセス可能です。
*   WEB_API_CORS_ALLOW_ORIGINS

    WebアプリのCORSクロスオリジンポリシー。デフォルトは`*`、すべてのドメインがアクセス可能です。

詳細な設定については、次のガイドを参照してください：[クロスオリジン/認証関連ガイド](https://avytux375gg.feishu.cn/wiki/HyX3wdF1YiejX3k3U2CcTcmQnjg)

#### ファイルストレージ設定

データセットのアップロードファイル、チーム/テナントの暗号化キーなどのファイルを保存するために使用します。

*   STORAGE_TYPE

    ストレージ施設のタイプ

    *   local（デフォルト）

        ローカルファイルストレージ。この場合、以下の`STORAGE_LOCAL_PATH`設定を設定する必要があります。
    *   s3

        S3オブジェクトストレージ。この場合、以下のS3_プレフィックスの設定を設定する必要があります。
    *   azure-blob

        Azure Blobストレージ。この場合、以下のAZURE_BLOB_プレフィックスの設定を設定する必要があります。
*   STORAGE_LOCAL_PATH

    デフォルトはstorage、すなわち現在のディレクトリのstorageディレクトリに保存します。dockerまたはdocker-composeでデプロイする場合、2つのコンテナにある`/app/api/storage`ディレクトリを同じローカルディレクトリにマウントする必要があります。そうしないと、ファイルが見つからないエラーが発生する可能性があります。
* S3_ENDPOINT：S3エンドポイントアドレス
* S3_BUCKET_NAME：S3バケット名
* S3_ACCESS_KEY：S3アクセスキー
* S3_SECRET_KEY：S3シークレットキー
* S3_REGION：S3リージョン情報（例：us-east-1）
* AZURE_BLOB_ACCOUNT_NAME: アカウント名（例：'difyai'）
* AZURE_BLOB_ACCOUNT_KEY: アカウントキー（例：'difyai'）
* AZURE_BLOB_CONTAINER_NAME: コンテナ名（例：'difyai-container'）
* AZURE_BLOB_ACCOUNT_URL: 'https://\\<your_account_name>.blob.core.windows.net'

#### ベクトルデータベース設定

*   VECTOR_STORE

    **使用可能な列挙型は以下を含みます：**

    * `weaviate`
    * `qdrant`
    * `milvus`
    * `zilliz`（`milvus`と同じ）
    * `pinecone`（現在未公開）
    * `tidb_vector`
*   WEAVIATE_ENDPOINT

    Weaviateエンドポイントアドレス（例：`http://weaviate:8080`）。
*   WEAVIATE_API_KEY

    Weaviateに接続するために使用するapi-keyの資格情報。
*   WEAVIATE_BATCH_SIZE

    Weaviateでオブジェクトのバッチ作成数。デフォルトは100。詳細はこちらのドキュメントを参照してください：https://weaviate.io/developers/weaviate/manage-data/import#how-to-set-batch-parameters
*   WEAVIATE_GRPC_ENABLED

    Weaviateとの通信にgRPC方式を使用するかどうか。オンにすると性能が大幅に向上しますが、ローカルでは使用できない可能性があります。デフォルトはtrueです。
*   QDRANT_URL

    Qdrantエンドポイントアドレス（例：`https://your-qdrant-cluster-url.qdrant.tech/`）。
*   QDRANT_API_KEY

    Qdrantに接続するために使用するapi-keyの資格情報。
*   PINECONE_API_KEY

    Pineconeに接続するために使用するapi-keyの資格情報。
*   PINECONE_ENVIRONMENT

    Pineconeの環境（例：`us-east4-gcp`）。
*   MILVUS_HOST

    Milvusホストの設定。
*   MILVUS_PORT

    Milvusポートの設定。
*   MILVUS_USER

    Milvusユーザーの設定。デフォルトは空。
*   MILVUS_PASSWORD

    Milvusパスワードの設定。デフォルトは空。
*   MILVUS_SECURE

    MilvusがSSL接続を使用するかどうか。デフォルトはfalse。

* TIDB_VECTOR_HOST

  TiDB Vectorホスト設定（例：`xxx.eu-central-1.xxx.tidbcloud.com`）
* TIDB_VECTOR_PORT

  TiDB Vectorポート番号設定（例：`4000`）
* TIDB_VECTOR_USER

  TiDB Vectorユーザー設定（例：`xxxxxx.root`）
* TIDB_VECTOR_PASSWORD

  TiDB Vectorパスワード設定
* TIDB_VECTOR_DATABASE

  TiDB Vectorデータベース設定（例：`dify`）

#### ナレッジベース設定

*   UPLOAD_FILE_SIZE_LIMIT

    アップロードファイルのサイズ制限。デフォルトは15M。
*   UPLOAD_FILE_BATCH_LIMIT

    一度にアップロードできるファイル数の上限。デフォルトは5個。
*   ETL_TYPE

    **使用可能な列挙型は以下を含みます：**

    *   dify

        Dify独自のファイル抽出ソリューション
    *   Unstructured

        Unstructured.ioのファイル抽出ソリューション
*   UNSTRUCTURED_API_URL

    ETL_TYPEがUnstructuredの場合、Unstructured APIパスの設定が必要です。

    例：`http://unstructured:8000/general/v0/general`

#### マルチモーダルモデル設定

*   MULTIMODAL_SEND_IMAGE_FORMAT

    マルチモーダルモデルの入力時に画像を送信する形式。デフォルトは`base64`、オプションで`url`。`url`モードでは呼び出しの遅延が`base64`モードよりも少なく、一般的には互換性が高い`base64`モードをお勧めします。`url`に設定する場合、`FILES_URL`を外部からアクセス可能なアドレスに設定する必要があります。これにより、マルチモーダルモデルが画像にアクセスできるようになります。
*   UPLOAD_IMAGE_FILE_SIZE_LIMIT

    アップロード画像ファイルのサイズ制限。デフォルトは10M。

#### Sentry 設定

アプリケーションの監視およびエラーログトラッキングに使用されます。

*   SENTRY_DSN

    Sentry DSNアドレス。デフォルトは空。空の場合、すべての監視情報はSentryに報告されません。
*   SENTRY_TRACES_SAMPLE_RATE

    Sentryイベントの報告割合。例えば、0.01に設定すると1%となります。
*   SENTRY_PROFILES_SAMPLE_RATE

    Sentryプロファイルの報告割合。例えば、0.01に設定すると1%となります。

#### Notion 統合設定

Notion統合設定。変数はNotion integrationを申請することで取得できます：[https://www.notion.so/my-integrations](https://www.notion.so/my-integrations)

* NOTION_CLIENT_ID
* NOTION_CLIENT_SECRET

#### メール関連設定

* MAIL_TYPE
  * resend
    * MAIL_DEFAULT_SEND_FROM\\
      送信者のメール名（例：no-reply [no-reply@dify.ai](mailto:no-reply@dify.ai)）、必須ではありません。
    * RESEND_API_KEY\\
      ResendメールプロバイダーのAPIキー。APIキーから取得できます。
  * smtp
    * SMTP_SERVER\\
      SMTPサーバーアドレス
    * SMTP_PORT\\
      SMTPサーバ ，用于验证接口身份。

* SESSION_タイプ： セッションコンポーネントのタイプ
  *   redis（デフォルト）

      これを選択した場合、下記の SESSION_REDIS_ で始まる環境変数を設定する必要があります。
  *   sqlalchemy

      これを選択した場合、現在のデータベース接続を使用し、sessions テーブルを使用してセッションレコードを読み書きします。
* SESSION_REDIS_HOST：Redis ホスト
* SESSION_REDIS_PORT：Redis ポート、デフォルトは 6379
* SESSION_REDIS_DB：Redis データベース、デフォルトは 0、Redis および Celery ブローカーとは異なるデータベースを使用してください。
* SESSION_REDIS_ユーザー名：Redis ユーザー名、デフォルトは空
* SESSION_REDIS_パスワード：Redis パスワード、デフォルトは空、パスワードの設定を強く推奨します。
* SESSION_REDIS_USE_SSL：SSL プロトコルを使用して接続するかどうか、デフォルトは false

#### クッキー戦略の設定

> ⚠️ この設定はバージョン 0.3.24 から廃止されました。

セッションクッキーのブラウザ戦略を設定するために使用されます。

*   COOKIE_HTTPONLY

    クッキーの HttpOnly 設定、デフォルトは true。
*   COOKIE_SAMESITE

    クッキーの SameSite 設定、デフォルトは Lax。
*   COOKIE_SECURE

    クッキーの Secure 設定、デフォルトは false。


