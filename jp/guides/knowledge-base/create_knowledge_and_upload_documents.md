# ナレッジベースの作成とドキュメントのアップロード

### 1 ナレッジベースの作成

Difyのメインナビゲーションバーからナレッジベースをクリックすると、既存のナレッジベースが表示されます。**ナレッジベースの作成**をクリックして作成ウィザードに進みます：

<figure><img src="../../.gitbook/assets/image (173).png" alt=""><figcaption><p>ナレッジベースの作成</p></figcaption></figure>

* ファイルが準備できている場合は、ファイルのアップロードから始めてください。
* まだ文書が準備できていない場合は、空のデータセットを作成してください。

<figure><img src="../../.gitbook/assets/image (191).png" alt=""><figcaption><p>ナレッジベースの作成</p></figcaption></figure>

{% hint style="info" %}
データセット作成時に外部データソースを選択した場合、そのナレッジベースのタイプは変更できません。これは、単一のナレッジベースで複数のデータソースを使用することによる管理の困難を防ぐためです。複数のデータソースを使用する場合、複数のナレッジベースを作成することをお勧めします。
{% endhint %}

***

### **2 ドキュメントのアップロード**

**ナレッジベース内でのドキュメントのアップロード手順：**

1. ローカルからアップロードするドキュメントを選択します。
2. セグメンテーションとクリーニングを行い、効果をプレビューします。
3. インデックスと検索戦略を選択および設定します。
4. セグメンテーションの埋め込みを待ちます。
5. アップロードが完了し、アプリケーションで使用できるようになります🎉

**ドキュメントのアップロード制限：**

* 単一のドキュメントのアップロードサイズは15MBに制限されています。
* 一度にアップロードできるファイルの最大数は20個です。
* SaaS版の異なる[サブスクリプションプラン](https://dify.ai/pricing)は、**バッチアップロードの数、ドキュメントの総アップロード数、ベクトルストレージ**に制限があります。

### 3 セグメンテーションとクリーニング

**セグメンテーション**：大規模言語モデルには限られたコンテキストウィンドウがあるため、通常は全体のテキストをセグメントに分割し、ユーザーの質問に最も関連性の高いセグメントを召喚します。これを「セグメンテーションTopK召喚モード」と呼びます。また、ユーザーの質問とテキストセグメントをセマンティックマッチングする際、適切なセグメンテーションサイズは関連性の最も高いテキスト内容をマッチングするのに役立ち、情報ノイズを減らします。

**クリーニング**：テキスト召喚の効果を確保するために、データをモデルに入力する前にクリーニングが必要です。たとえば、出力に不要な文字や空行が含まれている場合、回答の品質に影響を与える可能性があります。この問題を解決するために、Difyはさまざまなクリーニング方法を提供し、出力を下流のアプリケーションに送信する前にクリーニングすることができます。

セグメンテーションとクリーニングは2つの設定方法をサポートしています。

* 自動モード（近日廃止予定）
* カスタムモード

<figure><img src="../../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

カスタムモードでは、ユーザーは異なるドキュメント形式やシナリオ要件に応じてテキストのセグメンテーションとクリーニング戦略を設定できます。

**セグメンテーションルール：**

* セグメンテーション識別子：識別子（例："\\n"）を設定すると、テキストにその識別子が現れるたびにセグメンテーションが行われます。
* セグメントの最大長：テキストの文字数の最大上限に基づいてセグメンテーションを行い、その長さを超えると強制的にセグメンテーションが行われます。
* セグメントの重複長：セグメント間の重複文字数を設定します。セグメントの長さの10〜25％に設定することが推奨され、セグメント間のセマンティック関連性を保ち、多セグメント召喚時の効果を向上させます。

**前処理ルール：**

* 連続する空白、改行、タブ文字の置換。
* すべてのURLとメールアドレスの削除。

***

### 4 ETL オプション設定

RAGのプロダクションレベルのアプリケーションでは、データ召喚の効果を向上させるために、複数のデータソースを前処理およびクリーニングする必要があります。これをETL（抽出、変換、ロード）と呼びます。非構造化/半構造化データの前処理能力を強化するために、Difyは以下のオプションのETLソリューションをサポートしています：**Dify ETL** と[**Unstructured ETL**](https://unstructured.io/)。

> Unstructuredは、データを抽出してクリーンなデータに変換し、後続のステップに使用できるようにします。

Difyの各バージョンでのETLソリューションの選択：

* SaaS版では選択不可、デフォルトでUnstructured ETLを使用。
* コミュニティ版では選択可能、デフォルトでDify ETLを使用、[環境変数](../../getting-started/install-self-hosted/environments.md#zhi-shi-ku-pei-zhi)を介してUnstructured ETLを有効にできます。

ファイル解析のサポート形式の違い：

| DIFY ETL                                    | Unstructured ETL                                                         |
| ------------------------------------------- | ------------------------------------------------------------------------ |
| txt、markdown、md、pdf、html、htm、xlsx、xls、docx、csv | txt、markdown、md、pdf、html、htm、xlsx、xls、docx、csv、eml、msg、pptx、ppt、xml、epub |

{% hint style="info" %}
異なるETLソリューションではファイル抽出の効果にも違いがあります。Unstructured ETLのデータ処理方法について詳細を知りたい場合は、[公式ドキュメント](https://docs.unstructured.io/open-source/core-functionality/partitioning)を参照してください。
{% endhint %}

***

### 5 インデックス方式

テキストの**インデックス方式**を選択してデータのマッチング方法を指定する必要があります。インデックス戦略は検索戦略と密接に関連しているため、シナリオ要件に基づいて適切なインデックス方式を選択する必要があります。

**高品質モード：**OpenAIの埋め込みインターフェースを使用して処理を行い、ユーザーのクエリに対してより高い精度を提供します。

**エコノミーモード**：キーワードインデックス方式を使用し、精度を下げる代わりにトークンを消費しません。

**Q\&A モード（コミュニティ版のみサポート）：**Q\&Aセグメンテーションモード機能は、上記の通常の「Q to P」（質問からテキスト段落へのマッチング）とは異なり、「Q to Q」（質問から質問へのマッチング）を採用します。文書がセグメント化された後、各セグメントごとにQ\&Aマッチングペアを生成し、ユーザーが質問すると、最も類似した質問を見つけ出し、対応するセグメントを回答として返します。この方法はより正確で、ユーザーの質問に直接マッチングし、真に必要な情報をより正確に取得できます。

ナレッジベースにドキュメントをアップロードする際、システムはテキストをセグメント化し、ユーザーの質問（入力）が関連するテキストセグメント（Q to P）にマッチするようにします。最終的に結果を出力します。

> 質問テキストは完全な文法構造を持つ自然言語であり、文書検索タスクのいくつかのキーワードとは異なります。そのため、Q to Q（質問から質問へのマッチング）のモードは、意味とマッチングをより明確にし、頻度の高い類似した質問シナリオにも対応します。

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption><p>Q&#x26;Aセグメンテーションモードで複数のQ&#x26;Aペアにまとめられたテキスト</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption><p>Q to PとQ to Qのインデックス方式の違い</p></figcaption></figure>

***

### 6 検索設定

高品質インデックスモードでは、Difyは3つの検索オプションを提供します：

* **ベクトル検索**：クエリ埋め込みを生成し、そのベクトル表現に最も類似したテキストセグメントを検索します。
* **全文検索**：ドキュメント内のすべての語句をインデックスし、ユーザーが任意の語句をクエリし、それらの語句を含むテキストセグメントを返すことを可能にします。
* **ハイブリッド検索**：全文検索とベクトル検索を同時に実行し、リランクステップを追加して、2つの検索結果からユーザーの質問に最も適した結果を選択します。RerankモデルAPIの設定が必要です。

3つの検索方式の具体的な設定は以下の通りです：

#### **ベクトル検索**

定義：クエリ埋め込みを生成し、そのベクトル表現に最も類似したテキストセグメントを検索します。

<figure><img src="../../.gitbook/assets/image (116).png" alt="" width="563"><figcaption><p>ベクトル検索設定</p></figcaption></figure>

TopK：ユーザーの質問に最も類似したテキストセグメントをフィルタリングするために使用します。システムは選択されたモデルのコンテキストウィンドウの大きさに基づいてセグメント数を動的に調整します。システムのデフォルト値は3です。

Score閾値：テキストセグメントフィルタリングの類似度の閾値を設定します。設定されたスコアを超えるテキストセグメントのみを召喚します。システムのデフォルトではこの設定はオフになっており、召喚されたテキストセグメントの類似度フィルタリングは行いません。オンにした場合のデフォルト値は0.5です。

Rerankモデル：モデルプロバイダーのページでRerankモデルのAPIキーを設定した後、検索設定で「Rerankモデル」をオンにすると、システムはセマンティック検索後に召喚された文書結果を再度セマンティックリランクし、並び替え結果を最適化します。Rerankモデルを設定した場合、TopKとScore閾値の設定はリランクステップでのみ有効です。

#### **全文検索**

定義：ドキュメント内のすべての語句をインデックスし、ユーザーが任意の語句をクエリし、それらを含むテキストセグメントを返すことを可能にします。

<figure><img src="../../.gitbook/assets/image (122).png" alt="" width="563"><figcaption><p>全文検索設定</p></figcaption></figure>

TopK：ユーザーの質問に最も類似したテキストセグメントをフィルタリングするために使用します。システムは選択されたモデルのコンテキストウィンドウの大きさに基づいてセグメント数を動的に調整します。システムのデフォルト値は3です。

Rerankモデル：モデルプロバイダーのページでRerankモデルのAPIキーを設定した後、検索設定で「Rerankモデル」をオンにすると、システムは全文検索後に召喚された文書結果を再度セマンティックリランクし、並び替え結果を最適化します。Rerankモデルを設定した場合、TopKとScore閾値の設定はリランクステップでのみ有効です。

#### **ハイブリッド検索**

全文検索とベクトル検索を同時に実行し、リランクステップを適用して、2つの検索結果からユーザーの質問に最も適した結果を選択します。RerankモデルAPIの設定が必要です。

<figure><img src="../../.gitbook/assets/image (118).png" alt="" width="563"><figcaption><p>ハイブリッド検索設定</p></figcaption></figure>

TopK：ユーザーの質問に最も類似したテキストセグメントをフィルタリングするために使用します。システムは選択されたモデルのコンテキストウィンドウの大きさに基づいてセグメント数を動的に調整します。システムのデフォルト値は3です。

Rerankモデル：モデルプロバイダーのページでRerankモデルのAPIキーを設定した後、検索設定で「Rerankモデル」をオンにすると、システムはハイブリッド検索後に召喚された文書結果を再度セマンティックリランクし、並び替え結果を最適化します。Rerankモデルを設定した場合、TopKとScore閾値の設定はリランクステップでのみ有効です。