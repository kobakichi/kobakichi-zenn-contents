---
title: "EC2にElasticIPを割り当てる方法"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "ec2"]
published: true
---

## 作成したEC2にElasticIPを割り当てる方法

こちらの記事では、作成したEC2へElasticIPを割り当てる方法を解説します。

### 前提条件

- EC2がすでに作成済みであること。

## ElasticIPとは

ElasticIPアドレスとは、固定されたグローバルIPアドレスのことです。このElasticIPアドレスをEC2へ割り当てることで、割り当てられているパブリックIPアドレスが変動してしまうことを防ぐことができます。

EC2インスタンスを起動させた場合、通常はパブリックIPアドレスが割り当てられます。パブリックIPアドレスもグローバルIPアドレスのためインターネットと通信することができます。

しかし、EC2インスタンスを停止したり再起動した場合には、パブリックIPアドレスが変わってしまいます。この場合、Webサイトなどを公開していた場合には何かの原因でパブリックIPアドレスが変わってしまうとDNSサーバーで名前解決ができなくなり、ユーザーがWebサイトにアクセスできなくなってしまうでしょう。

このパブリックIPアドレスが勝手に変更されないよう、固定されたIPアドレスを割り当てるのがElasticIPになります。

### ElasticIPを割り当てる際の注意点

ElasticIPアドレスは起動しているEC2インスタンスへ割り当てている場合は無料で利用できますが、停止中のEC2インスタンスに割り当てていたり、未使用のElasticIPアドレスを保持していると料金が発生するため注意が必要です。

不要になったElasticIPアドレスは解放すること。これを必ず意識する必要があります。

## ElasticIPを割り当てる手順

以下にElasticIPを割り当てる手順を解説します。

### Elastic IPアドレスの割り当て

初めにマネジメントコンソールからEC2インスタンスを選択。左側のナビゲーションからElasticIPをクリック。右上のElasticIPアドレスを割り当てるをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/524a1e2e8bfb-20230517.png)

ElasticIPアドレスを割り当てる画面に遷移したら、ネットワークボーダーグループが利用しているリージョンであるか確認します。

あわせて、パブリックIPv4アドレスプールにチェックされているかを確認します。アドレスプールというのは、AWSが独自に保有しているIPアドレスを意味します。

![](https://storage.googleapis.com/zenn-user-upload/60af7abb931f-20230520.png)

### Elastic IPアドレスをEC2インスタンスへ関連づける

正常にElastic IPアドレスが割り当てられたら、EC2インスタンスへ関連付けをする必要があります。右上のアクションから、Elastic IPアドレスの関連付けを選択します。

![](https://storage.googleapis.com/zenn-user-upload/9a9997082c77-20230520.png)

リソースタイプ、インスタンス、プライベートIPアドレス、関連付けの許可をそれぞれ確認します。インスタンスの検索窓をクリックすると起動中のインスタンスが表示されます。

プライベートIPアドレスも検索窓をクリックすると表示されるのでクリック。最後に関連付けを許可するにチェックを入れて関連付けるボタンを選択します。

![](https://storage.googleapis.com/zenn-user-upload/cb3b1215d229-20230520.png)

以下のように表示されれば関連付けの完了です。

![](https://storage.googleapis.com/zenn-user-upload/ebf240ab0568-20230520.png)

## Elastic IPでSSH接続してみる

関連付けたElastic IPアドレスで、割り当てたEC2インスタンスへSSH接続できるか確認します。

```bash
$ ssh -i ~/.ssh/sample-ec2.pem ec2-user@3.112.121.14
The authenticity of host '3.112.121.14 (3.112.121.14)' can't be established.
~省略~
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '3.112.121.14' (ED25519) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
4 package(s) needed for security, out of 14 available
Run "sudo yum update" to apply all updates.
```

SSH接続ができることを確認できました。

## EC2インスタンスへの関連付けの解除

Elastic IPアドレスは起動中のインスタンスで使用している場合は無料で使えますが、停止中のインスタンスや未使用のElastic IPアドレスを保持している場合には料金が発生します。

インスタンスを停止する場合やElastic IPアドレスが必要なくなった場合には、忘れずに解放する必要があるので注意が必要です。

まずは関連付けの解除から行います。Elastic IPの画面に遷移し、アクションからElastic IPアドレスの関連付けの解除を選択します。

![](https://storage.googleapis.com/zenn-user-upload/0bf4479ede15-20230520.png)

次にElastic IPアドレスの解放を選択します。

![](https://storage.googleapis.com/zenn-user-upload/7e375289d1e0-20230520.png)

以下の画面が表示されればElastic IPアドレスの解放は完了です。これで無駄な請求を受けることはないでしょう。

![](https://storage.googleapis.com/zenn-user-upload/4b3bc3d8e07e-20230520.png)

## まとめ

Elastic IPアドレスを割り当てて使用することで、固定したIPアドレスをEC2インスタンスへ割り当てることができるようになります。

使用しなくなったElastic IPアドレスは忘れずに解放しておかないと、無駄な料金が掛かってしまうということは必ず覚えておくことが必要です。IPv4は枯渇問題を抱えているため、不要になったIPアドレスは忘れずにAWSへ返却しましょう。
