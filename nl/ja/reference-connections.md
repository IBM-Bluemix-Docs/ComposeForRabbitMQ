---
copyright:
  years: 2016,2018
lastupdated: "2018-06-13"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 接続構成
{: #connection-configuration}

{{site.data.keyword.composeForRabbitMQ_full}} データベース接続は、2 つの HAProxy ポータルによって管理されます。 各ポータルのメモリーは 64MB です。

ポータルが 2 つあるので、そのうち 1 つが到達不能になっても、アプリケーションは接続性を維持できます。 クライアント・サイドのフェイルオーバーは、アプリケーション設計者の責任です。 フェイルオーバー・エラーを完全に処理するドライバーを使用する場合を除き、以下を行う必要があります。

* 接続構成で複数のエンドポイントを受け入れるドライバーを使用する。
* データベースで読み書きを行うアプリケーション呼び出しのエラー処理が適切であることを確認する。 次のような選択肢があります。
  + エラーをログに記録して再接続する。
  + 再接続し、エラーを出した操作を再試行する。
  + 失敗した操作をキューイングして再接続をスケジューリングする。

## 転送中の暗号化

すべての {{site.data.keyword.composeForRabbitMQ}} HAProxy ポータルは、TLS/SSL 対応になっており、TLS バージョン 1.2 をサポートします。 サービスに関する証明書は、Let's Encrypt 証明書です。

## 接続制限

データベース接続には、ポータルあたり 1000 接続という接続制限があります。 接続制限を超えると、サービスが応答しなくなります。 ドライバーの接続プーリング機能を使用して、アプリケーションからの接続を管理できます。

## プロキシーのタイムアウト

HAProxy ポータルは、サーバーとクライアントとの間の接続のライフサイクルを管理します。 ドライバーの作成者や、{{site.data.keyword.composeForEtcd}} データベース接続の低水準のアクティビティーに関心がある方のための参照として、詳細を以下の部分で説明します。

設定 | 値 | 適用対象
----------|-----------|-----------
client | 3h | クライアントによるデータの確認応答または送信が予期される時間。
connect | 10s | プロキシーとサーバーの間の接続が行われる時間。
server | 3h | サーバーによるデータの確認応答または送信が予期される時間。
check | 5s | プロキシーとサーバーの間の接続が行われる時間の 2 次タイムアウト・チェックとして。
{: caption="表 1. RabbitMQ HAProxy のタイムアウト" caption-side="top"}



