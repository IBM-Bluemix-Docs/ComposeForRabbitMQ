---

copyright:
  years: 2016,2017
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 開始使用 Compose for RabbitMQ
{: #getting-started-with-compose-for-rabbitmq}

RabbitMQ 會非同步處理應用程式與資料庫之間的訊息，以分隔資料及應用程式層。RabbitMQ 可讓開發人員遞送及追蹤具有可自訂持續性層次、遞送設定及已確認發佈的訊息，並且將其置入佇列。透過使用 {{site.data.keyword.composeForRabbitMQ_full}}，您可以存取具有一組管理特性（例如部署監視、按一下按鈕擴充、使用者設定及日誌檔存取）的簡易管理介面。
{:shortdesc}

**附註：**在 2016 年 9 月 14 日之前佈建且仍然作用中的任何 Compose 服務實例，仍然可以使用，而且可以在 [https://www.compose.com/](https://www.compose.com) 直接存取。在 {{site.data.keyword.cloud}} 帳戶內，可以直接存取及使用從此點往前佈建的任何 Compose 服務實例。

## 建立 Compose for RabbitMQ 服務實例

[建立 {{site.data.keyword.composeForRabbitMQ}} 實例](https://console.ng.bluemix.net/catalog/services/compose-for-rabbitmq/)。

當您建立服務的實例時，請確保要選擇服務名稱及認證名稱。請維持服務不連結；稍後使用佈建服務時所提供的認證，即可將應用程式連接至服務。*可用的認證* 小節中會列出各種認證值。

當您佈建 {{site.data.keyword.composeForRabbitMQ}} 實例時，可以選擇*標準* 或*企業* 方案。使用*企業* 方案，您可以將 {{site.data.keyword.composeForRabbitMQ}} 實例佈建到可用的 {{site.data.keyword.composeEnterprise}} 叢集。{{site.data.keyword.composeEnterprise}} 提供企業相符性所需的安全和隔離，並使用專用網路來確保已部署之資料庫的效能。如需詳細資料，請參閱 [Compose Enterprise 文件](../ComposeEnterprise/index.html)。

## 管理 Compose for RabbitMQ

您可以從服務儀表板來管理服務。在這裡，您可以找到 {{site.data.keyword.cloud_notm}} Compose 資料庫的相關資訊，以及連接方式。您也可以：
- 管理備份 
- 為您的服務配置更多資源 
- 變更服務密碼
- 使用白名單來限制對資料庫的存取權。如需相關資訊，請參閱[設定](./dashboard-settings.html)。

## 連接至 Compose for RabbitMQ

您可以使用與服務一起建立的認證，或使用服務儀表板的*概觀* 標籤中所提供的連線字串及指令行，來連接至服務。

## 將 {{site.data.keyword.cloud_notm}} 應用程式連接至 Compose for RabbitMQ

若要將 {{site.data.keyword.cloud_notm}} 應用程式連接至服務，請使用與服務一起建立的認證。您可以在[連接 {{site.data.keyword.cloud_notm}} 應用程式](./connecting-bluemix-app.html)中，找到如何將 {{site.data.keyword.cloud_notm}} 應用程式連接至 {{site.data.keyword.composeForRabbitMQ}} 服務的資訊。

## 從 {{site.data.keyword.cloud_notm}} 之外連接至 Compose for RabbitMQ

如果您想要從 {{site.data.keyword.cloud_notm}} 之外連接至 {{site.data.keyword.composeForRabbitMQ}}，則可以使用所提供的連線字串或指令行。您可以在[連接外部應用程式](./connecting-external.html)中，找到如何連接的相關資訊。
