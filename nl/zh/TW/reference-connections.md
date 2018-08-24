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

# 連線配置
{: #connection-configuration}

{{site.data.keyword.composeForRabbitMQ_full}} 資料庫連線由 2 個 HAProxy 入口網站管理。每一個入口網站都有 64 MB 的記憶體。

這兩個入口網站讓應用程式在其中一個入口網站無法聯繫時，仍能維持連線功能。用戶端的失效接手是應用程式設計人員的責任。除非使用可完全處理失效接手錯誤的驅動程式，否則您應該執行下列動作：

* 使用可在其連線配置中接受多個端點的驅動程式。
* 確定您應用程式的資料庫讀取或寫入呼叫能適當地回應錯誤。這些選項包括：
  + 記載錯誤並重新連接。
  + 重新連接並重試已導致錯誤的作業。
  + 將失敗的作業放入佇列並排定重新連線。

## 傳輸中加密

所有 {{site.data.keyword.composeForRabbitMQ}} HAProxy 入口網站都已啟用 TLS/SSL，並支援 TLS 1.2 版。服務的憑證為 Let's Encrypt 憑證。

## 連線限制

資料庫連線具有每個入口網站各 1000 個連線的連線限制。超出連線限制時，您的服務就會無回應。您可以透過驅動程式的連線儲存區特性，管理來自應用程式的連線。

## Proxy 逾時

HAProxy 入口網站會管理伺服器與用戶端之間連線的生命週期。此處會詳細說明它們，作為驅動程式撰寫者的參考資料，以及其他對 {{site.data.keyword.composeForEtcd}} 資料庫連線低階活動有興趣者的參考資料。

設定 |值|適用時機
----------|-----------|-----------
client |3h|當用戶端預期要確認或傳送資料時。
connect | 10s |在 Proxy 和伺服器之間建立連線時。
server |3h|當伺服器預期要確認或傳送資料時。
check | 5s |作為次要的逾時檢查，在 Proxy 和伺服器之間建立連線時。
{: caption="表 1. RabbitMQ HAProxy 逾時" caption-side="top"}




