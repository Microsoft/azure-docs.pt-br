---
title: "Perguntas frequentes (FAQ) sobre o Barramento de Serviço | Microsoft Docs"
description: "Responde a algumas perguntas frequentes sobre o Barramento de Serviço do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm
ms.openlocfilehash: 1403184d96388cb03b2c767c4da342ec1c6fe236
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-faq"></a>Perguntas frequentes sobre o Barramento de Serviço
Este artigo responde a algumas perguntas frequentes sobre o Barramento de Serviço do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para obter informações gerais sobre preços e suporte do Azure.

## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Barramento de Serviço do Azure
### <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?
[O Barramento de Serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite enviar dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do Barramento de Serviço?
Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um hardware é necessária para a utilização do Barramento de Serviço e será uma das primeiras etapas da introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Barramento de Serviço do Azure?
A [Fila do Barramento de Serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são particularmente úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar umas com as outras. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) são recebidos e então enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço do Azure?
Um tópico pode ser visualizado como uma fila e ao usar várias assinaturas, ele se torna um modelo mais abrangente de mensagens; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) habilita um aplicativo que envia uma mensagem para um tópico com várias assinaturas para fazer com que essa mensagem seja recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?
Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. Uma [fila ou um tópico particionado](service-bus-partitioning.md) é manipulada por vários agentes de mensagens e armazenada em vários repositórios de mensagens. Isso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível.

Observe que a ordenação não é garantida ao usar o particionamento de entidades. Se uma partição não estiver disponível, você poderá enviar e receber mensagens de outras partições.

## <a name="best-practices"></a>Práticas recomendadas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do Barramento de Serviço do Azure?
* [Práticas recomendadas para melhorias de desempenho usando o Barramento de Serviço][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho na troca de mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As propriedades de uma fila e tópico a seguir são imutáveis. Leve isso em conta ao provisionar suas entidades, já que isso não pode ser modificado sem criar uma nova entidade de substituição.

* Tamanho
* Particionamento
* Sessões
* Detecção de duplicidade
* Entidade expressa

## <a name="pricing"></a>Preços
Esta seção responde a perguntas frequentes sobre a estrutura de preços do Barramento de Serviço.

O artigo [Barramento de Serviço, preços e cobrança](service-bus-pricing-billing.md) explica os medidores de cobrança no Barramento de Serviço e, para obter informações sobre opções de preços do Barramento de Serviço, consulte [Detalhes de preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Você também pode visitar as [Perguntas frequentes sobre o suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para obter informações gerais sobre preços do Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Como é cobrado o Barramento de Serviço?
Para saber mais sobre o preço do Barramento de Serviço, veja [Detalhes de preço do Barramento de Serviço][Pricing overview]. Além dos preços mencionados, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo está provisionado.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que é não está?
Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente, bem como qualquer transferência de dados recebida. A transferência de dados fora de uma região está sujeita a encargos de saída que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>O Barramento de Serviço cobra pelo armazenamento?
Não, o Barramento de Serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

## <a name="quotas"></a>Cotas

Para obter uma lista de cotas e limites do Barramento de Serviço, veja [Visão geral sobre cotas do Barramento de Serviço][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>O Barramento de Serviço tem cotas de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregado calculada entre todas as assinaturas de um cliente. Como compreendemos que talvez seja necessário usar mais do que esses limites, contate o atendimento ao cliente a qualquer momento para que possamos entender as suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, a cota de uso agregado é de 5 bilhões de mensagens por mês.

Embora reservemos o direito de desabilitar uma conta de cliente que tenha excedido suas cotas de uso em determinado mês, forneceremos uma notificação por email e faremos várias tentativas de contatar o cliente antes de realizar qualquer ação. Os clientes que excederem essas cotas ainda serão responsáveis pelas cobranças que excederem as cotas.

Como ocorre com outros serviços no Azure, o Barramento de Serviço aplica um conjunto de cotas específicas para garantir que haja um uso inteligente dos recursos. Você pode encontrar mais detalhes sobre essas cotas em [visão geral de cotas do Barramento de Serviço][Quotas overview].

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Barramento de Serviço do Azure e suas ações sugeridas?
Para obter uma lista de exceções possíveis do Barramento de Serviço, confira [Visão geral das exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?
As Assinaturas de Acesso Compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para saber mais sobre como gerar suas próprias assinaturas no Node, PHP, Java e C\#, veja o artigo [Assinaturas de Acesso Compartilhado][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?

Mova um namespace de uma assinatura do Azure para outra usando o [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para executar a operação, o namespace já deve estar ativo. O usuário que executa os comandos deve ser administrador nas assinaturas de origem e de destino.

#### <a name="portal"></a>Portal

Para usar o portal do Azure para migrar namespaces do Barramento de Serviço para outra assinatura, siga as instruções descritas [aqui](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

A sequência de comandos do PowerShell a seguir move um namespace de uma assinatura do Azure para outra. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um administrador em assinaturas de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Barramento de Serviço, veja os tópicos a seguir.

* [Introdução ao Barramento de Serviço Premium do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Barramento de Serviço Premium do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Barramento de Serviço](service-bus-messaging-overview.md)
* [Visão geral da arquitetura de Barramento de Serviço do Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
