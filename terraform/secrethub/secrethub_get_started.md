## SecretHub

https://secrethub.io/

## Features

SecretHub で出来ること。

- クレデンシャル情報のストア
- チームでの共有とアクセスコントロール
- ロギング
- 各OS向けパッケージング済みのクライアントCLI
- **Terraform Provider** (その他、インテグレーションは多数)

セキュリティに関しては[このあたり](https://github.com/secrethub/ansible-secrethub)に詳細が記載されてる。

## Get Started with SecretHub

### Official instruction

https://secrethub.io/docs/start/getting-started/

### Installation

各OS向けパッケージ版も用意されているけど、今回は Docker でお試し。

```
docker run -it -v $HOME/.secrethub:/root/.secrethub secrethub/cli
```

### Get Started

```
/ # secrethub signup
Let's get you setup. Before we continue, I need to know a few things about you. Please answer the questions below, followed by an [ENTER]

The username you'd like to use: nakt
Your full name: Tetsuo Nakamura
Your (work) email address: someone@gmail.com

An account credential will be generated and stored at /root/.secrethub/credential. Losing this credential means you lose the ability to decrypt your secrets. So keep it safe.
Please enter a passphrase to protect your local credential (leave empty for no passphrase):
Enter the same passphrase again:
Setting up your account..............................................
Created your account.

Do you want to create a shared workspace for your team? [Y/n]: n

You can create a shared workspace later using `secrethub org init`.

Setup complete. To read your first secret, run:
    secrethub read nakt/start/hello
```

読み書きを確認してみる

```
/ # secrethub read nakt/start/hello
Welcome Tetsuo Nakamura! This is your first secret. To write a new version of this secret, run:

    secrethub write nakt/start/hello

/ # echo "Hello World" | secrethub write nakt/start/hello
Writing secret value...
Write complete! The given value has been written to nakt/start/hello:2
/ # secrethub read nakt/start/hello
Hello World
```

監査ログを確認してみる

```
/ # secrethub audit nakt/start/hello
AUTHOR    EVENT                    IP ADDRESS      DATE
nakt      read.secret              x.x.x.84    24 minutes ago
nakt      read.secret_version      x.x.x.84    24 minutes ago
nakt      create.secret_version    x.x.x.84    24 minutes ago
nakt      read.secret              x.x.x.84    26 minutes ago
nakt      read.secret_version      x.x.x.84    26 minutes ago
nakt      create.secret_version    x.x.x.84    26 minutes ago
nakt      create.secret            x.x.x.84    26 minutes ago
nakt      create.repo              x.x.x.84    26 minutes ago
```

## Terraformから使ってみる

### レポジトリ作成

Serethub にテスト用のレポジトリを作成する

```
/ # secrethub repo init nakt/demoapp
Creating repository...
Create complete! The repository nakt/demoapp is now ready to use.
```

### Provider のインストール

手元の環境は Mac なので、公式手順のダウンロード先を macOS 用バイナリのパスに変更。

```
mkdir -p .terraform.d/plugins
curl -sSfL https://github.com/secrethub/terraform-provider-secrethub/releases/latest/download/terraform-provider-secrethub-macOS-64-bit.tar.gz | tar zxf - -C .terraform.d/plugins
```
### Terraform コード

テスト用の Terraform コードを用意。

```
locals {
  secrethub_dir = "nakt/demoapp"
}

provider "secrethub" {
  credential = file("~/.secrethub/credential")
}

resource "secrethub_secret" "db_password" {
  path = "${local.secrethub_dir}/password"

  generate {
    length      = 22
    use_symbols = true
  }
}

output "secret" {
  value = secrethub_secret.db_password.value
}
```

## Terraform 実行

実行してみる

```
(default) ~/w/t/secrethub ❯❯❯ terraform init --plugin-dir .terraform.d/plugins

Initializing the backend...

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.secrethub: version = "~> 0.3"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

(default) ~/w/t/secrethub ❯❯❯ terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # secrethub_secret.db_password will be created
  + resource "secrethub_secret" "db_password" {
      + id      = (known after apply)
      + path    = "nakt/demoapp/password"
      + value   = (sensitive value)
      + version = (known after apply)

      + generate {
          + length      = 22
          + use_symbols = true
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

secrethub_secret.db_password: Creating...
secrethub_secret.db_password: Creation complete after 3s [id=nakt/demoapp/password]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

secret = !!?R9Kf=pLOi3zSqLdHGBa
```

stateに生で記録されていることを確認 (笑)

```
(default) ~/w/t/secrethub ❯❯❯ cat terraform.tfstate | jq -r ".resources[].instances[].attributes.value"
!!?R9Kf=pLOi3zSqLdHGBa
```

SecretHub 内レポジトリを削除する
```
/ # secrethub repo rm nakt/demoapp
[DANGER ZONE] This action cannot be undone. This will permanently remove the nakt/demoapp repository, all its secrets and all associated service accounts. Please type in the full path of the repository to confirm: nakt/demoapp
Removing repository...
Removal complete! The repository nakt/demoapp has been permanently removed.
```

## Refs.

- [SecretHub](https://secrethub.io/)
- [Security Design](https://github.com/secrethub/ansible-secrethub)
- [Ansible Module](https://github.com/secrethub/ansible-secrethub)
