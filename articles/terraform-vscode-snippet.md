---
title: "TerraformをVS codeで書く時のコメントをスニペット化してみた"
emoji: "🎉"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "VScode", "snipett"]
published: true
---

## VS codeでスニペットを登録する方法

こちらの記事では、Terraformのコードを書くときに少しだけ手間なコメントの書き方をスニペット化する方法を解説します。

### 環境

mac OS Ventura 13.2

VS code バージョン: 1.79.2

```
# やりたいのはこれをスニペット化すること
# ------------------------------
# provider
# ------------------------------
```

## terraform.jsonを開く

画像のCodeから、「基本設定」、ユーザースニペットの構成を選択します。

![](https://storage.googleapis.com/zenn-user-upload/4977fb0d2a5e-20230630.png)

検索窓へ`terraform`と入力すると、`terraform.json`が表示されるのでクリックします。`terraform-vars`ではないので注意しましょう。

![](https://storage.googleapis.com/zenn-user-upload/f73140d8d52d-20230630.png)

## スニペットを登録する

`terraform.json`を開いたら、編集します。

![](https://storage.googleapis.com/zenn-user-upload/a0536dceaa78-20230630.png)

書き方についての詳細は、下記の参考にさせていただいた記事を読んでいただきたいため割愛させていただきます。

### terraform.jsonのコード

```json:terraform.json
# varと入力したら${}を入力してくれるスニペット

  "var assign": {
    "prefix": "var",
    "body": ["\"${$1}\""],
    "description": "Variable assign"
  },
# comと入力したら以下のコメントを入力してくれるスニペット

  "create comment": {
    "prefix": "com",
    "body": [
      "# ------------------------------",
      "# ",
      "# ------------------------------"
    ],
    "description": "terraform comment"
  }
```

こちらを記述して保存すれば、良い感じにコメントを挿入できます。

## 参考にさせていただいた記事

[VSCodeでterraformを楽にするための少しのスニペット](https://qiita.com/peace098beat/items/baf991ae0662e229bb54)

これでまたTerraformを書くのが楽しくなりました。
