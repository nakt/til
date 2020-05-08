# 

## About Ansible Molecule

[プロジェクトサイト](https://molecule.readthedocs.io/en/latest/)の About Ansible Molecule の機械翻訳。

```
Moleculeプロジェクトは、Ansibleロールの開発とテストを支援するために設計されています。

Molecule は、複数のインスタンス、オペレーティングシステム、ディストリビューション、仮想化プロバイダ、テストフレームワーク、テストシナリオでのテストをサポートします。

Moleculeは、よく書かれていて理解しやすく、メンテナンスが容易な一貫したロールを開発するためのアプローチを推奨しています。

Moleculeは、Ansibleの最新の2つのメジャーバージョン（N/N-1）のみをサポートしており、最新バージョンが2.9.xであれば、2.8.xでもコードをテストすることを意味します。
```

##

molecule のインストール

```
pip install molecule
pip install 'molecule[docker]' 
```

molecule で 新Roleを init すると、推奨ディレクトリ構造と初期テンプレートを scaffold してくれる。

```
(molecule) ~/w/molecule ❯❯❯ molecule init role --driver-name docker testrole
--> Initializing new role testrole...
Initialized role in /Users/tetsuo/work/molecule/testrole successfully.
```










