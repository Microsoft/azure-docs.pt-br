---
title: Perguntas frequentes (FAQ) sobre o Barramento de Serviço | Microsoft Docs
description: Responde a algumas perguntas frequentes sobre o barramento de serviço do Azure.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 80809afc9f2a8e8da2f6adecfe916141c4cd3e45
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278332"
---
# <a name="service-bus-faq"></a>Perguntas frequentes sobre o Barramento de Serviço

Este artigo discute algumas perguntas frequentes sobre o Barramento de Serviço do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](https://azure.microsoft.com/support/faq/) para obter informações gerais sobre preços e suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Barramento de Serviço do Azure
### <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?
[O Barramento de Serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite enviar dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do Barramento de Serviço?
Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um namespace é necessária para a utilização do Barramento de Serviço e é uma das primeiras etapas da introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Barramento de Serviço do Azure?
A [Fila do Barramento de Serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar umas com as outras. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) são recebidos e então enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço do Azure?
Um tópico pode ser visualizado como uma fila e ao usar várias assinaturas, ele se torna um modelo mais abrangente de mensagens; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) habilita um aplicativo que envia uma mensagem para um tópico com várias assinaturas para fazer com que essa mensagem seja recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?
Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. Compatível apenas com as camadas de mensagens Básico e Standard, uma [fila ou um tópico particionado](service-bus-partitioning.md) é manipulada por vários agentes de mensagens e armazenada em vários repositórios de mensagens. Esse recurso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível.

A ordenação não é garantida ao usar entidades particionadas. Se uma partição não estiver disponível, você poderá enviar e receber mensagens de outras partições.

 As entidades particionadas não são mais compatíveis com a camada [SKU Premium](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quais portas eu preciso abrir no firewall? 
Você pode usar os seguintes protocolos com o barramento de serviço do Azure para enviar e receber mensagens:

- Advanced Message Queuing Protocol (AMQP)
- Protocolo do sistema de mensagens do Barramento de Serviço (SBMP)
- HTTP

Consulte a tabela a seguir para as portas de saída que você precisa abrir para usar esses protocolos para se comunicar com os hubs de eventos do Azure. 

| Protocol | Portas | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 e 5672 | Consulte o [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 a 9354 | Consulte [modo de conectividade](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quais endereços IP preciso para a lista de permissões?
Para localizar os endereços IP corretos para a lista branca de suas conexões, siga estas etapas:

1. Execute o seguinte comando em um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP retornado em `Non-authoritative answer`. Esse endereço IP é estático. O único ponto no tempo que seria alterado seria se você restaurasse o namespace em um cluster diferente.

Se você usar a redundância de zona para seu namespace, precisará executar algumas etapas adicionais: 

1. Primeiro, execute nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção de **resposta não autoritativa** , que está em um dos seguintes formatos: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Execute nslookup para cada um com sufixos S1, S2 e S3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 


## <a name="best-practices"></a>Práticas recomendadas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do Barramento de Serviço do Azure?
Consulte [práticas recomendadas para melhorias de desempenho usando o barramento de serviço][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho ao trocar mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As propriedades de uma fila e tópico a seguir são imutáveis. Considere essa limitação ao provisionar suas entidades, já que essas propriedades não podem ser modificadas sem criar uma nova entidade de substituição.

* Particionamento
* Sessões
* Detecção de duplicidade
* Entidade expressa

## <a name="pricing"></a>Preços
Esta seção responde a algumas perguntas frequentes sobre a estrutura de preços do Barramento de Serviço.

O artigo [Preços e cobrança do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/) explica os medidores de cobrança no Barramento de Serviço. Para saber mais sobre o preço do Barramento de Serviço, veja [Detalhes de preço do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Você também pode visitar as [Perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/) para obter informações gerais sobre preços do Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Como é cobrado o Barramento de Serviço?
Para saber mais sobre o preço do Barramento de Serviço, consulte [Detalhes de preço do Barramento de Serviço][Pricing overview]. Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que é não está?
Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente, bem como qualquer transferência de dados recebida. A transferência de dados fora de uma região está sujeita a encargos de saída, que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>O Barramento de Serviço cobra pelo armazenamento?
Não, o Barramento de Serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

## <a name="quotas"></a>Cotas

Para obter uma lista de cotas e limites do barramento de serviço, consulte [visão geral][Quotas overview]das cotas do barramento de serviço.

### <a name="does-service-bus-have-any-usage-quotas"></a>O Barramento de Serviço tem cotas de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas de um cliente. Se precisar de mais do que esses limites, você poderá entrar em contato com o atendimento ao cliente em qualquer horário para que possamos entender suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, a cota de uso agregado é de 5 bilhões de mensagens por mês.

Embora a Microsoft se reserve o direito de desabilitar uma conta de cliente que tenha ultrapassado suas cotas de uso em determinado mês, notificações por email são enviadas e várias tentativas de contatar o cliente são feitas antes de realizarmos qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis por cobranças que excedam as cotas.

Como ocorre com outros serviços no Azure, o Barramento de Serviço aplica um conjunto de cotas específicas para garantir que haja um uso inteligente dos recursos. Você pode encontrar mais detalhes sobre essas cotas na [visão geral cotas do barramento de serviço][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens com mais de 1 MB?
Os serviços de mensagens do Barramento de Serviço (filas e tópicos/assinaturas) permitem que o aplicativo envie mensagens com até 256 KB (camada standard) ou 1 MB (camada premium). Se você estiver lidando com mensagens de tamanho maior do que 1 MB, use o padrão de verificação de declaração descrito [nesta postagem de blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Solução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não eu consigo criar um namespace após excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde até 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, a seguinte mensagem de erro será exibida: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Barramento de Serviço do Azure e suas ações sugeridas?
Para obter uma lista de possíveis exceções do barramento de serviço, consulte [visão geral de exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?
Assinaturas de acesso compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para obter informações sobre como gerar suas próprias assinaturas em node. js, PHP, Java, Python e C#, consulte o artigo [assinaturas de acesso compartilhado][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?

Mova um namespace de uma assinatura do Azure para outra usando o [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para executar a operação, o namespace já deve estar ativo. O usuário que executa os comandos deve ser administrador nas assinaturas de origem e de destino.

#### <a name="portal"></a>Portal

Para usar o portal do Azure para migrar namespaces do Barramento de Serviço para outra assinatura, siga as instruções descritas [aqui](../azure-resource-manager/resource-group-move-resources.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

A sequência de comandos do PowerShell a seguir move um namespace de uma assinatura do Azure para outra. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um administrador em assinaturas de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Barramento de Serviço, consulte os seguintes artigos:

* [Introdução ao Barramento de Serviço Premium do Azure (postagem de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Barramento de Serviço Premium do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Barramento de Serviço](service-bus-messaging-overview.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
