---
title: "TerraformでRDSインスタンスを削除する方法"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "aws", "RDS"]
published: false
---

## TerraformでRDSを作成し、destroyを実行する際にハマりました。

Terraformを使用して、RDSインスタンスを作成。不要になったため作成したインスタンスを全て削除しようとしましたが、なぜかRDSインスタンスを削除することができず。

以下のエラーに何度も遭遇しました。

```bash
Error: final_snapshot_identifier is required when skip_final_snapshot is false
```

## 結論:RDSの設定項目を修正し、applyしてからdestroyを実行する

RDSをはじめに作成する際、設定する項目によって`destroy`できるかどうかの運命が決まるということでした。私はこの理解が足らず、コードを変更して`plan`で設定が更新されているはずなのに、`destroy`できないというループにハマりました。

結論として、初めに削除保護の有効化、インスタンスを削除する際のスナップショット作成を有効にしてインスタンスを作成した場合、コードを修正後に一旦`apply`を実行。その後、`destroy`を実行することでインスタンスを削除する必要がありました。

設定に必要な項目は以下のコードになります。

```bash
backup_window              = "04:00-05:00"
backup_retention_period    = 0#←この値を0に設定すること
maintenance_window         = "Mon:05:00-Mon:08:00"
auto_minor_version_upgrade = false

# 削除まわりの設定
deletion_protection = false#←この設定をfalseにすること
skip_final_snapshot = true#←この設定をtrueにすること

apply_immediately = true
```

以上の項目を設定し、一旦applyを実行。設定を反映させてからdestroyを実行することで無事作成したインスタンスを削除することができました。

## 設定項目の意味を振り返る

ここで修正が必要な設定項目を振り返ります。

### backup_retention_period

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance#backup_retention_period

バックアップを何日分保存するかの設定値。7に設定すれば7日間、5に設定すれば5日間といった具合に変更することが可能です。0に設定することで、自動バックアップを無効化することが可能です。設定値は0〜35の間でなければならない。

### deletion_protection

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance#deletion_protection

DBインスタンスの削除保護機能。この値を`true`にすることで、誤ってDBインスタンスを削除してしまうことを防ぐことができる。デフォルト値は`false`。

### skip_final_snapshot

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_instance#skip_final_snapshot

DBインスタンスが削除される前に最終DBスナップショットを作成するかどうかを決定する項目。`true`を設定した場合、DBスナップショットは保存されない。DBスナップショットとは、DBインスタンス全体のバックアップのことを意味する。

## まとめ

RDSを削除する場合、設定項目を修正し、一旦`apply`を実行。その後に`destroy`を実行することでRDSインスタンスを削除することができるようになる。私のように無駄に時間を溶かしてしまう人が少しでも減りますように。
