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

# Configuração da Conexão
{: #connection-configuration}

As conexões com o banco de dados do {{site.data.keyword.composeForRabbitMQ_full}} são gerenciadas por 2 portais HAProxy. Cada portal tem 64 MB de memória.

Os dois portais que permitem que os aplicativos mantenham a conectividade no caso de um deles se torne inacessível. Failover no lado do cliente é responsabilidade do designer de aplicativo. A menos que você esteja trabalhando com um driver que manipule completamente os erros de failover, é necessário:

* Trabalhar com um driver que aceite múltiplos terminais em sua configuração de conexão.
* Assegurar que as suas chamadas de aplicativos leiam ou gravem adequadamente na reação do banco de dados a erros. As opções incluem:
  + Criar log do erro e reconectar.
  + Reconectar e tentar novamente a operação que causou o erro.
  + Enfileirar a operação com falha e planejar a reconexão.

## Criptografia em Trânsito

Todos os portais HAProxy do {{site.data.keyword.composeForRabbitMQ}} têm o TLS/SSL ativado e suportam o TLS versão 1.2. Os certificados para o serviço são certificados Let's Encrypt.

## Limites de Conexão

As conexões com o banco de dados têm um limite de 1.000 conexões por portal. Exceder o limite de conexão tornará seu serviço não responsivo. É possível gerenciar conexões de seu aplicativo por meio do recurso de definição do conjunto de conexões do driver.

## Tempos limite de proxy

Os portais HAProxy gerenciam o ciclo de vida de conexões entre o servidor e o cliente. Nós as detalhamos aqui como uma referência para gravadores de driver e outros que têm interesse na atividade de baixo nível de conexões com o banco de dados do {{site.data.keyword.composeForEtcd}}.

Configurando | Value | Aplica
----------|-----------|-----------
cliente | 3h | Quando se espera que o cliente reconheça ou envie dados.
conectar | 10s | Quando uma conexão está sendo feita entre o proxy e o servidor.
server | 3h | Quando se espera que o servidor reconheça ou envie dados.
check | 5s | Como uma verificação de tempo limite secundário quando uma conexão está sendo feita entre o proxy e o servidor.
{: caption="Tabela 1. Tempos limite do RabbitMQ HAProxy" caption-side="top"}




