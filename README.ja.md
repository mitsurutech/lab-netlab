# 1台の VM で学ぶネットワークラボ：FRRouting によるスタティックルーティング、OSPF、BGP

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-netlab/actions/workflows/check.yml)

授業でそのまま使える **コンピューターネットワークのラボ**が3つあります。[MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)が提供するもので、学校では生徒ごとに専用のマシンとターミナルをブラウザーで使えます。生徒は一人ひとり、中に小さなネットワークが組まれた Ubuntu VM を受け取ります。**FRRouting が動くルーター**と ping を打つホストがあり、実機でも使うのと同じ `vtysh` コンソールで、**スタティックルーティング、OSPF、BGP** を学びます。生徒のコンピューターには何もインストールせず、Cisco のライセンスも要りません。

ルーターにはそれぞれ専用のマシンが要りますが、生徒の VM は1台です。そこで、すべてのルーターとホストを**ネットワーク名前空間**（network namespace）、すべてのリンクを仮想ケーブルのペアにし、各ルーターが自分の FRRouting のデーモンを動かします。本物のコマンドが使えることが大事な点です。`ip route`、`traceroute -n`、`tcpdump`、`vtysh` です。

## 3つのラボ

| ラボ | ファイル | 生徒がすること | ヘルプページ |
| --- | --- | --- | --- |
| スタティックルーティング | [`static-routing.lab.yaml`](static-routing.lab.yaml) | パケットがどこで止まるかを調べ、必要なルーターにスタティックルートを追加し、管理用ネットワークには届かないままにする | [スタティックルーティングのラボ](https://mitsurutech.co.uk/ja/help/lab-netlab-static) |
| OSPF | [`ospf.lab.yaml`](ospf.lab.yaml) | 3台のルーターでエリア 0 の OSPF を立ち上げ、hello を観察し、すべての隣接関係が Full になるまで待ち、あるネットワークを広告から外す | [OSPF のラボ](https://mitsurutech.co.uk/ja/help/lab-netlab-ospf) |
| BGP | [`bgp.lab.yaml`](bgp.lab.yaml) | 2つの自律システムをネイバーにし、それぞれのネットワークを広告し、一方の背後にあるプライベートネットワークは広告しない | [BGP のラボ](https://mitsurutech.co.uk/ja/help/lab-netlab-bgp) |

各ラボは、何をするかを書き、答えは書かない README を、生徒のホームフォルダーに置きます。それぞれに**チェック**があります。これは、教員が読まなくても提出された VM を採点するスクリプトです。新たに起動した、保存済みマシンのコピーの上で実行されるので、生徒が `write memory` で保存したものだけが見え、実際の状態が成り立っているときだけ合格になります（ルートがルーティングテーブルにある、OSPF のネイバーが Full である、または BGP セッションが Established である、`h1` がルーターを通る経路で `h2` に届く、プライベートネットワークには届かないままである、など）。

## ファイルの中身

各 `.lab.yaml` は、プラットフォームがそのまま実行する **VM ラボの定義**の全体です。

| キー | 内容 |
| --- | --- |
| `cpus`、`memory`、`disk` | 生徒ごとの VM のサイズ。2 CPU、2048 MB、10 GB |
| `open` | ブラウザーで開くポート。なし。ラボはターミナルです |
| `setup` | 各生徒の VM が初めて起動したときに root で1回実行されるスクリプト。Ubuntu のアーカイブから FRRouting と `tcpdump` を約10秒でインストールし、`netlab` コマンド、トポロジー、ラボの README を書き出します |
| `check` | 採点用のスクリプト。新たに起動した、保存済みディスクのコピーの上で root として実行されます。終了ステータスが結果で、`0` なら合格です |

インストールされる `netlab` コマンド：`netlab status` はマシンの一覧、`netlab shell r1` はルーターのコンソール、`netlab shell h1` はホストのコンソールを開き、`netlab reset` はラボを最初からやり直します。

## プラットフォームで使う

**クラスの課題として出すには：**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. 出発点にしたいサンプルを押すか、ここにあるファイルのどれかの内容を貼り付けます。サンプルを使うとテキストが置き換わり、元に戻すで自分のテキストに戻せます。
3. 公開して、まず自分のコピーを試します。生徒ごとに専用の VM があり、セッションをまたいで保たれます。
4. ほかの課題と同じように配布します。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存済みのその生徒のマシンのコピーが起動し（保存し直されません）、合格したかどうかと出力内容が表示されます。

マニュアルの [Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)と[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)に、そのほかの説明があります。独自のトポロジーを作るには、ファイルをコピーして、その `setup` の中のトポロジーを変更します。ルーター、ホスト、リンクごとに1行で、すべてのルーターでそのプロトコルを起動するには `daemons ospfd` または `daemons bgpd` を書きます。

学校のアカウントがまだありませんか。[最初の1年は無料](https://mitsurutech.co.uk/ja/pricing)で、生徒50人まで使えます。[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルのチェック

push のたびに、`.github/workflows/check.yml` が、各ファイルが上記の5つのキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの上限の 16 KB 以内であることを確認します。VM は起動しません。それはプラットフォーム自身のマシンで行います。

## ラボで動かないこと

VM が届くのは、Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には届きません。そのため、インターネット上のホストをラボに向けることはできません。VM は1台なので、攻撃元にも攻撃先にもなる2台目のマシンはなく、複数のルーターからなるトポロジーはその中に作られます。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。対象は、ラボの定義、`netlab` ツール、このドキュメントです。[FRRouting](https://frrouting.org) のライセンスは GPL-2.0 で、VM の起動時に Ubuntu のアーカイブからインストールされます。ここでは何も配布していません。

## このリポジトリについて

[Mitsuru Technologies](https://mitsurutech.co.uk) が[MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナーラボ、VM ラボ、GitHub から Web アプリをデプロイする機能、自動採点のクイズがあります。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。日本語での研修とサポートもご用意しています。

同じ提供元のほかのラボ：[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)。
