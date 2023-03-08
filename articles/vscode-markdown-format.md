---
title: "VSCodeでマークダウンファイルの無駄なスペースを排除する方法"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["markdown", "vscode", "無駄なスペース"]
published: true
---

# VSCodeでマークダウン記事を保存した際の無駄なスペースの排除方法

こちらの記事では、VSCodeでマークダウン記事を作成し保存した際、無駄なスペースができてしまう問題の解決方法をご紹介します。
![](https://storage.googleapis.com/zenn-user-upload/93d1b7747b00-20230308.png =450x)

## 前提条件

今回はVSCodeに以下のプラグインを使用した際の設定方法です。

```bash
Markdown All in One

prettier
```

## prettier-plugin-md-nocjspをinstall

半角スペースが入らないようにするためのプラグインがあるため、`npm`でインストールします。

この時、プロジェクトのルートディレクトリで以下のコマンドを実行します。

```bash
npm install -D prettier-plugin-md-nocjsp
```

## .prettierrcの作成

インストールが完了したら、`.prettierrc`ファイルをルートディレクトリに作成します。

```bash
touch .prettierrc
```

ファイル作成後、以下の内容をファイルに記述、保存します。

```bash:.prettirrc
{
  "overrides": [
    {
      "files": ["*.md", "README"],
      "options": {
        "parser": "markdown-nocjsp"
      }
    },
    {
      "files": "*.mdx",
      "options": {
        "parser": "mdx-nocjsp"
      }
    }
  ]
}
```

以上で設定完了です。
マークダウンファイルを作成し、保存して無駄なスペースができていないか確認してみましょう。

:::message alert
すでにスペースができている場合、もう一度スペースを削除して保存する必要があります。
:::

### 参考記事

[Markdown+VSCodeでブログを執筆する環境を整えてみた](https://note.com/shift_tech/n/n4814c191284a)
