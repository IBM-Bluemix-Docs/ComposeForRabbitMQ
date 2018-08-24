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

# Arquitetura 
{: #architecture}

## Replicação

Um serviço {{site.data.keyword.composeForRabbitMQ_full}} contém três nós agindo unanimemente como um broker altamente disponível, cada um executando o aplicativo do RabbitMQ e compartilhando usuários, hosts virtuais, filas, trocas, ligações e parâmetros de tempo de execução. Quando você seleciona uma região na qual o serviço é implementado, os três nós são distribuídos pelas zonas de disponibilidade da região e todos os dados e estado necessários para a operação de um broker do RabbitMQ são replicados em todos os nós. 

Um nó funciona como principal e os outros funcionam como espelhos. Os espelhos aplicam as operações que ocorrem no principal exatamente na mesma ordem que o principal e, portanto, mantêm o mesmo estado. Todas as ações diferentes de uma ação de publicação vão apenas para o principal e o principal transmite então o efeito das ações para os espelhos. Se o principal falhar, um dos espelhos será promovido e o broker do RabbitMQ funcionará normalmente.

## Criptografia de Disco

Todos os serviços do  {{site.data.keyword.composeForRabbitMQ}}  têm criptografia em repouso. Seus dados residem em servidores que possuem criptografia em nível de volume ativada. 

Como este {{site.data.keyword.composeForRabbitMQ}} é um serviço gerenciado, é possível que os operadores do {{site.data.keyword.cloud}} Compose vejam dados. Além da criptografia de disco, recomendamos que, se você estiver passando informações pessoais por meio de sua fila de sistema de mensagens, as criptografe antes de enviá-las ou use extensões ou recursos para ativar a criptografia no próprio RabbitMQ. Embora esses métodos possam afetar a usabilidade ou o desempenho, é uma boa prática assegurar-se de que as informações pessoais sejam protegidas com criptografia.


## Auto-scaling

Os recursos são escalados automaticamente com base no uso total de memória do broker RabbitMQ. O armazenamento baseia-se em uma razão de RAM provisionada em 1:1. Esses recursos são empacotados como unidades.

O Auto-scaling foi projetado para responder às tendências de curto a médio prazo de seu banco de dados. A cada minuto, seu serviço será verificado e, se ele estiver sendo executado com pouca RAM, mais unidades serão alocadas para a implementação. 

O Auto-scaling não reduz a capacidade de implementações em que o uso de disco/memória foi reduzido. Os recursos provisionados para seus bancos de dados permanecem para suas necessidades futuras ou até você reduzir a capacidade de sua implementação manualmente.
{: .tip}
