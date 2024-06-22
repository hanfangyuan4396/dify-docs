# Webアプリテンプレートに基づく

もし開発者がゼロから新しい製品を開発する場合や、製品のプロトタイプ設計段階にある場合、Difyを使用して人工知能サイトを迅速に立ち上げることができます。同時に、Difyは開発者がさまざまな形式のフロントエンドアプリケーションを自由に作成できることを望んでいます。そのため、以下のものを提供しています:

* **SDK** 各種言語のDify APIに迅速にアクセスするためのもの
* **Webアプリテンプレート** さまざまなタイプのアプリケーションのためのWebアプリ開発フレームワーク

マサチューセッツ工科大学のライセンスに基づき、Webアプリテンプレートはオープンソースです。これらを自由に修正・デプロイしてDifyの全機能を実現することができ、またはあなた自身のアプリケーションの参考コードとして利用することもできます。

これらのテンプレートはGitHubで見つけることができます:

* [会話型アプリ](https://github.com/langgenius/webapp-conversation)
* [テキスト生成アプリ](https://github.com/langgenius/webapp-text-generator)

Webアプリテンプレートを使用する最も迅速な方法は、GitHubで "**Use this template**" をクリックすることです。これにより、新しいリポジトリが派生されます。その後、DifyアプリケーションIDとAPIキーを設定する必要があります。例として:

```javascript
export const APP_ID = ''
export const API_KEY = ''
```

`config/index.ts`の中でさらに設定を行います:

```
export const APP_INFO: AppInfo = {
  "title": 'Chat APP',
  "description": '',
  "copyright": '',
  "privacy_policy": '',
  "default_language": 'zh-Hans'
}

export const isShowPrompt = true
export const promptTemplate = ''
```

各Webアプリテンプレートにはデプロイ手順を含むリードミーが提供されています。通常、Webアプリテンプレートには軽量なバックエンドサービスが含まれており、開発者のAPIキーがユーザーに直接露出しないようにします。

これらのWebアプリテンプレートは、AIアプリケーションのプロトタイプを迅速に構築し、Difyのすべての機能を使用するのに役立ちます。もしこれを基に自分自身のアプリケーションや新しいテンプレートを開発した場合、ぜひ私たちと共有してください。