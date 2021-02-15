---
title: Perguntas frequentes (FAQ) sobre o Barramento de Serviço | Microsoft Docs
description: Este artigo fornece respostas para algumas das perguntas frequentes sobre o barramento de serviço do Azure.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693386"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Barramento de serviço do Azure-perguntas frequentes (FAQ)

Este artigo discute algumas perguntas frequentes sobre o Barramento de Serviço do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](https://azure.microsoft.com/support/faq/) para obter informações gerais sobre preços e suporte do Azure.


## <a name="general-questions-about-azure-service-bus"></a>Perguntas gerais sobre o Barramento de Serviço do Azure
### <a name="what-is-azure-service-bus"></a>O que é o Barramento de Serviço do Azure?
O [barramento de serviço do Azure](service-bus-messaging-overview.md) é uma plataforma de nuvem de mensagens assíncronas que permite que você envie dados entre sistemas separados. A Microsoft oferece esse recurso como um serviço, o que significa que você não precisa hospedar seu próprio hardware para usá-lo.

### <a name="what-is-a-service-bus-namespace"></a>O que é um namespace do Barramento de Serviço?
Um [namespace](service-bus-create-namespace-portal.md) fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. A criação de um namespace é necessária para a utilização do Barramento de Serviço e é uma das primeiras etapas da introdução.

### <a name="what-is-an-azure-service-bus-queue"></a>O que é uma fila do Barramento de Serviço do Azure?
A [Fila do Barramento de Serviço](service-bus-queues-topics-subscriptions.md) é uma entidade na qual as mensagens são armazenadas. As filas são úteis quando você tem vários aplicativos ou várias partes de um aplicativo distribuído que precisam se comunicar umas com as outras. A fila é semelhante a um centro de distribuição em que vários produtos (mensagens) são recebidos e então enviados desse local.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço do Azure?
Um tópico pode ser visualizado como uma fila e ao usar várias assinaturas, ele se torna um modelo mais abrangente de mensagens; essencialmente, uma ferramenta de comunicação de um-para-muitos. Esse modelo de publicação/assinatura (ou *pub/sub*) habilita um aplicativo que envia uma mensagem para um tópico com várias assinaturas para fazer com que essa mensagem seja recebida por vários aplicativos.

### <a name="what-is-a-partitioned-entity"></a>O que é uma entidade particionada?
Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. Com suporte apenas nas camadas de sistema de mensagens básica e Standard, uma [fila particionada ou um tópico](service-bus-partitioning.md) é manipulado por vários agentes de mensagens e armazenados em vários repositórios de mensagens. Esse recurso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não renderiza uma fila ou tópico particionado indisponível.

A ordenação não é garantida ao usar entidades particionadas. Se uma partição não estiver disponível, você poderá enviar e receber mensagens de outras partições.

 As entidades particionadas não são mais compatíveis com a camada [SKU Premium](service-bus-premium-messaging.md). 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Onde o barramento de serviço do Azure armazena dados?
Camada standard do barramento de serviço do Azure utiliza o banco de dados SQL do Azure para sua camada de armazenamento de back-end. Para todas as regiões, exceto sul do Brasil e sudeste asiático, o backup do banco de dados é hospedado em uma região diferente (geralmente a região emparelhada do Azure). Para as regiões do Sul do Brasil e do Sudeste Asiático, os backups de banco de dados são armazenados na mesma região para acomodar os requisitos de residências para essas regiões.

A camada Premium do barramento de serviço do Azure armazena metadados e dados em regiões que você seleciona. Quando a recuperação de desastre geográfico é configurada para um namespace Premium do barramento de serviço do Azure, os metadados são copiados para a região secundária que você selecionar.


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quais portas preciso abrir no firewall? 
Você pode usar os seguintes protocolos com o Barramento de Serviço do Azure para enviar e receber mensagens:

- Advanced Message Queuing Protocol 1,0 (AMQP)
- Protocolo de transferência de hipertexto 1,1 com TLS (HTTPS)

Consulte a tabela a seguir para as portas TCP de saída que você precisa abrir para usar esses protocolos para se comunicar com o barramento de serviço do Azure:

| Protocolo | Porta | Detalhes | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP com TLS. Consulte [Guia do protocolo AMQP](service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Essa porta é usada para a API HTTP/REST e para AMQP-over-WebSockets |

A porta HTTPS geralmente é necessária para a comunicação de saída também quando AMQP é usado pela porta 5671, porque várias operações de gerenciamento executadas pelos SDKs do cliente e a aquisição de tokens de Azure Active Directory (quando usado) são executadas por HTTPS. 

Os SDKs oficiais do Azure geralmente usam o protocolo AMQP para enviar e receber mensagens do barramento de serviço. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

O pacote WindowsAzure. ServiceBus mais antigo para o .NET Framework tem a opção de usar o "protocolo de mensagens do barramento do serviço" (SBMP) herdado, também conhecido como "netmessaging". Esse protocolo usa as portas TCP 9350-9354. O modo padrão para esse pacote é detectar automaticamente se essas portas estão disponíveis para comunicação e mudará para WebSockets com TLS pela porta 443 se esse não for o caso. Você pode substituir essa configuração e forçar esse modo definindo o `Https` [connectivitymode](/dotnet/api/microsoft.servicebus.connectivitymode) na [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) configuração, que se aplica globalmente ao aplicativo.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Quais endereços IP preciso adicionar à lista de permissões?
Para localizar os endereços IP corretos a serem adicionados à lista de permissões para suas conexões, siga estas etapas:

1. Execute o seguinte comando de um prompt de comando: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anote o endereço IP retornado em `Non-authoritative answer`. 

Se você usar a **redundância de zona** para seu namespace, precisará executar algumas etapas adicionais: 

1. Primeiro, execute nslookup no namespace.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anote o nome na seção **resposta não autoritativa**, que está em um dos seguintes formatos: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Execute nslookup para cada um com sufixos s1, s2 e s3 para obter os endereços IP de todas as três instâncias em execução em três zonas de disponibilidade, 

    > [!NOTE]
    > O endereço IP retornado pelo `nslookup` comando não é um endereço IP estático. No entanto, ela permanece constante até que a implantação subjacente seja excluída ou movida para um cluster diferente.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Onde posso encontrar o endereço IP do cliente que envia/recebe mensagens de/para um namespace? 
Não registramos em log os endereços IP de clientes que enviam ou recebem mensagens de/para seu namespace. Regenerar chaves para que todos os clientes existentes não consigam autenticar e examinar as configurações [do controle de acesso baseado em função (RBAC do Azure) do Azure](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)para garantir que somente usuários ou aplicativos permitidos tenham acesso ao namespace. 

Se você estiver usando um namespace **Premium** , use [filtragem de IP](service-bus-ip-filtering.md), pontos de extremidade de serviço de [rede virtual](service-bus-service-endpoints.md)e [pontos de extremidade privados](private-link-service.md) para limitar o acesso ao namespace. 

## <a name="best-practices"></a>Práticas recomendadas
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quais são algumas das práticas recomendadas do Barramento de Serviço do Azure?
Consulte [Práticas recomendadas para melhorias de desempenho usando o Barramento de Serviço][Best practices for performance improvements using Service Bus] – este artigo descreve como otimizar o desempenho ao trocar mensagens.

### <a name="what-should-i-know-before-creating-entities"></a>O que devo saber antes de criar entidades?
As propriedades de uma fila e tópico a seguir são imutáveis. Considere essa limitação ao provisionar suas entidades, pois essas propriedades não podem ser modificadas sem a criação de uma nova entidade de substituição.

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

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Quais usos do barramento de serviço estão sujeitos à transferência de dados? O que não é?
Todas as transferências de dados dentro de uma determinada região do Azure são feitas gratuitamente, bem como qualquer transferência de dados recebida. A transferência de dados fora de uma região está sujeita a encargos de saída, que podem ser encontrados [aqui](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>O Barramento de Serviço cobra pelo armazenamento?
Não. O barramento de serviço não cobra pelo armazenamento. No entanto, há uma cota que limita a quantidade máxima de dados que podem persistir por fila/tópico. Consulte as Perguntas Frequentes a seguir.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Tenho um namespace standard do barramento de serviço. Por que vejo encargos no grupo de recursos ' $system '?
O barramento de serviço do Azure atualizou os componentes de cobrança recentemente. Por causa dessa alteração, se você tiver um namespace padrão do barramento de serviço, poderá ver itens de linha para o recurso '/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system ' no grupo de recursos ' $system '.

Esses encargos representam o encargo base por assinatura do Azure que provisionou um namespace padrão do barramento de serviço. 

É importante observar que esses encargos não são novos, ou seja, eles já existiam no modelo de cobrança anterior. A única alteração é que agora eles estão listados em ' $system '. Isso é feito por causa de restrições no novo sistema de cobrança que agrupa encargos de nível de assinatura, não vinculado a um recurso específico, sob a ID de recurso ' $system '.

## <a name="quotas"></a>Cotas

Para obter uma lista de cotas e limites do Barramento de Serviço, veja [Visão geral sobre cotas do Barramento de Serviço][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Como lidar com mensagens com mais de 1 MB?
Os serviços de mensagens do Barramento de Serviço (filas e tópicos/assinaturas) permitem que o aplicativo envie mensagens com até 256 KB (camada standard) ou 1 MB (camada premium). Se você estiver lidando com mensagens de tamanho maior que 1 MB, use o padrão de verificação de declaração descrito nesta [postagem no blog](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Solução de problemas
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Por que não eu consigo criar um namespace após excluí-lo de outra assinatura? 
Quando você exclui um namespace de uma assinatura, aguarde até 4 horas antes de recriá-lo com o mesmo nome em outra assinatura. Caso contrário, a seguinte mensagem de erro será exibida: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quais são algumas das exceções geradas pelas APIs do Barramento de Serviço do Azure e suas ações sugeridas?
Para obter uma lista de exceções possíveis do Barramento de Serviço, confira [Visão geral das exceções][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>O que é uma Assinatura de Acesso Compartilhado e quais idiomas oferecem suporte para a geração de uma assinatura?
Assinaturas de acesso compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para obter informações sobre como gerar suas próprias assinaturas em Node.js, PHP, Java, Python e C#, consulte o artigo [assinaturas de acesso compartilhado][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?

Mova um namespace de uma assinatura do Azure para outra usando o [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para executar a operação, o namespace já deve estar ativo. O usuário que executa os comandos deve ser administrador nas assinaturas de origem e de destino.

#### <a name="portal"></a>Portal

Para usar o portal do Azure para migrar namespaces do Barramento de Serviço para outra assinatura, siga as instruções descritas [aqui](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

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
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>É possível desabilitar o TLS 1,0 ou 1,1 nos namespaces do barramento de serviço?
Não. Não é possível desabilitar o TLS 1,0 ou 1,1 nos namespaces do barramento de serviço. Nos aplicativos cliente que se conectam ao barramento de serviço, use o TLS 1,2 ou superior. Para obter mais informações, consulte [impondo o TLS 1,2 Use com o barramento de serviço do Azure-Microsoft Tech Community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Barramento de Serviço, consulte os seguintes artigos:

* [Introdução ao Barramento de Serviço Premium do Azure (postagem de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Barramento de Serviço Premium do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do barramento de serviço](service-bus-messaging-overview.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
