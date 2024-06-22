# 事前定義モデル接続

プロバイダー統合完了後、次にプロバイダーへのモデルの接続を行います。

まず、接続するモデルのタイプを決定し、対応するプロバイダーのディレクトリ内に対応するモデルタイプの`モジュール`を作成する必要があります。

現在サポートされているモデルタイプは以下の通りです：

* `LLM` テキスト生成モデル
* `text_embedding` テキスト埋め込み
* `rerank` ランク再評価モデル
* `speech2text` 音声からテキスト
* `TTS` テキストから音声
* `moderation` 審査

ここでは`Anthropic`を例に取ります。`Anthropic`はLLMのみをサポートしているため、`model_providers.anthropic`に`llm`という名前の`モジュール`を作成します。

事前定義のモデルの場合、まず`llm`モジュール内にモデル名をファイル名とするYAMLファイルを作成する必要があります。例：`claude-2.1.yaml`

#### モデルYAMLの準備

```yaml
model: claude-2.1  # モデル識別子
# モデル表示名。en_US英語、zh_Hans中国語の二つの言語を設定できます。zh_Hansが設定されていない場合、デフォルトでen_USが使用されます。
# ラベルを設定しない場合、モデル識別子が使用されます。
label:
  en_US: claude-2.1
model_type: llm  # モデルタイプ、claude-2.1はLLM
features:  # サポートする機能、agent-thoughtはエージェント推論、visionは画像理解をサポート
- agent-thought
model_properties:  # モデルプロパティ
  mode: chat  # LLMモード、completeはテキスト補完モデル、chatは対話モデル
  context_size: 200000  # 最大コンテキストサイズ
parameter_rules:  # モデル呼び出しパラメータルール、LLMのみ提供が必要
- name: temperature  # 呼び出しパラメータ変数名
  # デフォルトで5つの変数内容設定テンプレートが用意されています。temperature/top_p/max_tokens/presence_penalty/frequency_penalty
  # use_template内でテンプレート変数名を設定すると、entities.defaults.PARAMETER_RULE_TEMPLATE内のデフォルト設定が使用されます
  # 追加の設定パラメータを設定した場合、デフォルト設定を上書きします
  use_template: temperature
- name: top_p
  use_template: top_p
- name: top_k
  label:  # 呼び出しパラメータ表示名
    zh_Hans: 取样数量
    en_US: Top k
  type: int  # パラメータタイプ、float/int/string/booleanがサポートされています
  help:  # ヘルプ情報、パラメータの作用を説明
    zh_Hans: 仅从每个后续标记的前 K 个选项中采样。
    en_US: Only sample from the top K options for each subsequent token.
  required: false  # 必須かどうか、設定しない場合もあります
- name: max_tokens_to_sample
  use_template: max_tokens
  default: 4096  # パラメータデフォルト値
  min: 1  # パラメータ最小値、float/intのみ使用可能
  max: 4096  # パラメータ最大値、float/intのみ使用可能
pricing:  # 価格情報
  input: '8.00'  # 入力単価、つまりプロンプト単価
  output: '24.00'  # 出力単価、つまり返答内容単価
  unit: '0.000001'  # 価格単位、上記価格は100Kあたりの単価
  currency: USD  # 価格通貨
```

すべてのモデル設定を準備完了後にモデルコードの実装を開始することをお勧めします。

同様に、他のプロバイダーの対応モデルタイプディレクトリ内のYAML設定情報を参照することもできます。完全なYAMLルールについては、Schema[^1]をご覧ください。

#### モデル呼び出しコードの実装

次に、`llm`モジュール内に同名のPythonファイル`llm.py`を作成し、コード実装を行います。

`llm.py`内にAnthropic LLMクラスを作成し、`AnthropicLargeLanguageModel`（任意の名前）と名付け、基底クラス`__base.large_language_model.LargeLanguageModel`を継承し、以下のメソッドを実装します：

*   LLM呼び出し

    LLM呼び出しのコアメソッドを実装し、ストリーミングと同期返答の両方をサポートします。

    ```python
    def _invoke(self, model: str, credentials: dict,
                prompt_messages: list[PromptMessage], model_parameters: dict,
                tools: Optional[list[PromptMessageTool]] = None, stop: Optional[List[str]] = None,
                stream: bool = True, user: Optional[str] = None) \
            -> Union[LLMResult, Generator]:
        """
        LLMを呼び出す

        :param model: モデル名
        :param credentials: モデル認証情報
        :param prompt_messages: プロンプトメッセージ
        :param model_parameters: モデルパラメータ
        :param tools: ツール呼び出し用ツール
        :param stop: 停止ワード
        :param stream: ストリーム応答かどうか
        :param user: ユーザーID
        :return: 完全な応答またはストリーミング応答チャンク生成結果
        """
    ```

    実装時には、同期返答とストリーミング返答を処理するために2つの関数を使用する必要があります。Pythonは`yield`キーワードを含む関数をジェネレータ関数として認識し、返されるデータタイプが固定されるため、同期返答とストリーミング返答を別々に実装する必要があります。以下のように（以下の例では簡略化されたパラメータを使用していますが、実際の実装では上記のパラメータリストに従う必要があります）：

    ```python
    def _invoke(self, stream: bool, **kwargs) \
            -> Union[LLMResult, Generator]:
        if stream:
              return self._handle_stream_response(**kwargs)
        return self._handle_sync_response(**kwargs)

    def _handle_stream_response(self, **kwargs) -> Generator:
        for chunk in response:
              yield chunk
    def _handle_sync_response(self, **kwargs) -> LLMResult:
        return LLMResult(**response)
    ```
*   事前計算入力トークン

    モデルが事前計算トークンインターフェースを提供していない場合は、0を返しても構いません。

    ```python
    def get_num_tokens(self, model: str, credentials: dict, prompt_messages: list[PromptMessage],
                       tools: Optional[list[PromptMessageTool]] = None) -> int:
        """
        指定されたプロンプトメッセージのトークン数を取得

        :param model: モデル名
        :param credentials: モデル認証情報
        :param prompt_messages: プロンプトメッセージ
        :param tools: ツール
        :return:
        """
    ```
*   モデル認証情報検証

    プロバイダーの認証情報検証と同様に、ここでは個別のモデルに対して検証を行います。

    ```python
    def validate_credentials(self, model: str, credentials: dict) -> None:
        """
        モデル認証情報を検証

        :param model: モデル名
        :param credentials: モデル認証情報
        :return:
        """
    ```
*   呼び出し異常エラーのマッピングテーブル

    モデル呼び出し異常時に、ランタイム指定の`InvokeError`タイプにマッピングする必要があります。これにより、Difyは異なるエラーに対して異なる後続処理を行うことができます。

    ランタイムエラー：

    * `InvokeConnectionError` 呼び出し接続エラー
    * `InvokeServerUnavailableError` 呼び出しサーバー利用不可エラー
    * `InvokeRateLimitError` 呼び出しレート制限エラー
    * `InvokeAuthorizationError` 認証エラー
    * `InvokeBadRequestError` 呼び出し不正リクエストエラー

    ```python
    @property
    def _invoke_error_mapping(self) -> dict[type[InvokeError], list[type[Exception]]]:
        """
        モデル呼び出しエラーを統一エラーにマッピング
        キーは呼び出し元にスローされるエラータイプ
        値はモデルによってスローされるエラータイプで、
        呼び出し元の統一エラータイプに変換する必要があります。

        :return: 呼び出しエラーマッピング
        """
    ```

インターフェースメソッドの説明については：[Interfaces](https://github.com/langgenius/dify/blob/main/api/core/model_runtime/docs/zh_Hans/interfaces.md)をご覧ください。具体的な実装については：[llm.py](https://github.com/langgenius/dify-runtime/blob/main/lib/model_providers/anthropic/llm/llm.py)を参照してください。

[^1]: #### プロバイダー

    * `provider` (string) プロバイダー識別子、例：`openai`
    * `label` (object) プロバイダー表示名、i18n対応、`en_US`英語、`zh_Hans`中国語の二つの言語を設定可能
      * `zh_Hans` (string) \[optional] 中国語ラベル名、`zh_Hans`が設定されていない場合、デフォルトで`en_US`が使用されます。
      * `en_US` (string) 英語ラベル名
    * `description` (object) \[optional] プロバイダー説明、i18n対応
      * `zh_Hans` (string) \[optional] 中国語説明
      * `en_US` (string) 英語説明
    * `icon_small` (string) \[optional] プロバイダー小アイコン、対応するプロバイダー実装ディレクトリ内の`_assets`ディレクトリに保存、中英同様のポリシー
      * `zh_Hans` (string) \[optional] 中国語アイコン
      * `en_US` (string) 英語アイコン
    * `icon_large` (string) \[optional] プロバイダー大アイコン、対応するプロバイダー実装ディレクトリ内の\_assetsディレクトリに保存、中英同様のポリシー
      * `zh_Hans` (string) \[optional] 中国語アイコン
      * `en_US` (string) 英語アイコン
    * `background` (string) \[optional] 背景色の色値、例：#FFFFFF、空白の場合はデフォルトの色が表示されます。
    * `help` (object) \[optional] ヘルプ情報
      * `title` (object) ヘルプタイトル、i18n対応
        * `zh_Hans` (string) \[optional] 中国語タイトル
        * `en_US` (string) 英語タイトル
      * `url` (object) ヘルプリンク、i18n対応
        * `zh_Hans` (string) \[optional] 中国語リンク
        * `en_US` (string) 英語リンク
    * `supported_model_types` (array\[ModelType]) 対応モデルタイプ
    * `configurate_methods` (array\[ConfigurateMethod]) 設定方法
    * `provider_credential_schema` (ProviderCredentialSchema) プロバイダー認証情報スキーマ
    * `model_credential_schema` (ModelCredentialSchema) モデル認証情報スキーマ