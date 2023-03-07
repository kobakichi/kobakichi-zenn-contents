---
title: "MacOSでTerraformの環境構築 CommandLineToolsエラー"
emoji: "🍣"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "CLI", "mac"]
published: true
---

## はじめに

Terraform を MacOS へ導入する際に、

```bash
Your Command Line Tools are too outdated.
Update them from Software Update in System Preferences.
```

というエラーに遭遇したので記事にしました。

## 環境

```bash
System Version: macOS 13.2 (22D49)
Kernel Version: Darwin 22.3.0
Homebrew 4.0.4
```

## Terraform の install

[公式ドキュメント](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)を参考に導入開始。以下のコマンドをターミナルで実行しました。

```bash
brew tap hashicorp/tap
```

コマンド実行後、以下のエラーに遭遇。

```bash
Error: Your Command Line Tools are too outdated.
Update them from Software Update in System Preferences.

If that doesn't show you any updates, run:
sudo rm -rf /Library/Developer/CommandLineTools

sudo xcode-select --install

Alternatively, manually download them from:
https://developer.apple.com/download/all/.
You should download the Command Line Tools for Xcode 14.1.
```

コマンドラインツールが古すぎるから、ソフトウェアをアップデートしてくださいと言われているので、上記に書いてあるコードを実行します。

```bash
sudo rm -rf /Library/Developer/CommandLineTools
```

このコマンドを実行することにより、コマンドラインツールをアンインストールしています。

次に、以下のコマンドを実行しコマンドラインツールをインストールします。

```bash
sudo xcode-select --install
```

再度、以下のコマンドで Terraform を install します。

```bash
brew tap hashicorp/tap
```

続けて次のコマンドを実行。

```bash
brew install hashicorp/tap/terraform
```

最後にインストールできているか、バージョンを確認します。

```bash
terraform -v
  Terraform v1.3.9
  on darwin_amd64
```

バージョンが確認できれば Terraform のインストール完了です。
