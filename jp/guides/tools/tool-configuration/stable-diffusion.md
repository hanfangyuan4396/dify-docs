# ステーブルディフュージョン
ステーブルディフュージョンは、テキストプロンプトを基に画像を生成するツールです。DifyではステーブルディフュージョンWebUI APIへのアクセスを実現しているため、Dify内で直接利用することができます。以下にDifyにステーブルディフュージョンを統合する手順を説明します。

## 1. GPUを搭載したマシンを用意する
ステーブルディフュージョンは、GPUを搭載したマシンを用いることで最適に画像を生成できます。しかし、必須ではなく、CPUのみでも画像を生成することは可能ですが、速度が非常に遅くなるかもしれません。

## 2. ステーブルディフュージョンWebUIを起動する
ローカルマシンまたはサーバー上でステーブルディフュージョンWebUIを起動します。

### 2.1. ステーブルディフュージョンWebUIリポジトリをクローンする
[公式リポジトリ](https://github.com/AUTOMATIC1111/stable-diffusion-webui)からステーブルディフュージョンWebUIリポジトリをクローンします。

```bash
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
```

### 2.2. ローカルでの起動
リポジトリをクローンした後、クローンしたリポジトリのディレクトリに移動し、以下のコマンドを実行してステーブルディフュージョンWebUIを起動します。

#### Windows
```bash
cd stable-diffusion-webui
./webui.bat --api --listen
```

#### Linux
```bash
cd stable-diffusion-webui
./webui.sh --api --listen
```

### 2.3. モデルの準備
ターミナルに表示されたアドレスをブラウザでアクセスすることでステーブルディフュージョンWebUIにアクセスできますが、モデルはまだ利用できません。HuggingFaceまたは他のソースからモデルをダウンロードし、ステーブルディフュージョンWebUIの`models`ディレクトリに配置する必要があります。

例えば、[pastel-mix](https://huggingface.co/JamesFlare/pastel-mix)をモデルとして使用する場合、`git lfs`を使ってモデルをダウンロードし、`stable-diffusion-webui`の`models`ディレクトリに配置します。

```bash
git clone https://huggingface.co/JamesFlare/pastel-mix
```

### 2.4 モデル名の取得
モデルリストに`pastel-mix`が表示されますが、モデル名を取得する必要があります。`http://your_id:port/sdapi/v1/sd-models`にアクセスすると、以下のようなモデル名が表示されます。

```json
[
    {
        "title": "pastel-mix/pastelmix-better-vae-fp32.ckpt [943a810f75]",
        "model_name": "pastel-mix_pastelmix-better-vae-fp32",
        "hash": "943a810f75",
        "sha256": "943a810f7538b32f9d81dc5adea3792c07219964c8a8734565931fcec90d762d",
        "filename": "/home/takatost/stable-diffusion-webui/models/Stable-diffusion/pastel-mix/pastelmix-better-vae-fp32.ckpt",
        "config": null
    },
]
```

`model_name`が必要です。この例では`pastel-mix_pastelmix-better-vae-fp32`です。

## 3. Difyにステーブルディフュージョンを統合する
`ツール > StableDiffusion > 認証へ`で認証とモデル設定を行い、前のステップで取得した情報を使用します。

## 4. 完了

Difyで試してみましょう！