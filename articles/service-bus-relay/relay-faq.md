---
title: Perguntas frequentes de retransmissão do Azure | Microsoft Docs
description: Obtenha respostas a algumas perguntas frequentes sobre Retransmissão do Azure.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 207f73bbf9a92d26be1791fc11ce81fe68252705
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422964"
---
# <a name="azure-relay-faqs"></a>Perguntas frequentes sobre Retransmissão do Azure

Este artigo responde a algumas perguntas frequentes sobre a [Retransmissão do Azure](https://azure.microsoft.com/services/service-bus/). Para obter informações gerais sobre preço e suporte do Azure, confira [Perguntas frequentes sobre suporte do Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-relay"></a>O que é Retransmissão do Azure?
O [serviço de Retransmissão do Azure](relay-what-is-it.md) facilita seus aplicativos híbridos ajudando você a exporta com mais segurança os serviços que residem em uma rede corporativa para a nuvem pública. Você pode expor os serviços sem abrir uma conexão de firewall e sem exigir alterações invasivas a uma infraestrutura de rede corporativa.

### <a name="what-is-a-relay-namespace"></a>O que é um namespace de Retransmissão?
Um [namespace](relay-create-namespace-portal.md) é um contêiner de escopo que você pode usar para tratar recursos de Retransmissão dentro de seu aplicativo. Você deve criar um namespace para usar a Retransmissão. Essa é uma das primeiras etapas para começar.

### <a name="what-happened-to-service-bus-relay-service"></a>O que aconteceu com o serviço de Retransmissão do Barramento de Serviço?
O serviço que antes era chamado de Retransmissão do Barramento de Serviço agora se chama [Retransmissão do WCF](service-bus-relay-tutorial.md). Você pode continuar a usar esse serviço como de costume. O recurso Conexões Híbridas é uma versão atualizada de um serviço que foi transplantado dos Serviços BizTalk do Azure. Ainda há suporte para Retransmissão do WCF e Conexões Híbridas.

## <a name="pricing"></a>Preços
Esta seção responde a algumas perguntas frequentes sobre a estrutura de preços de Retransmissão. Você também pode conferir as [Perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/) para obter informações gerais sobre preços do Azure. Para saber mais sobre o preço da Retransmissão, consulte [Detalhes de preço do Barramento de Serviço][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Como são cobradas as Conexões Híbridas e a Retransmissão do WCF?
Para obter informações completas sobre preços de retransmissão, consulte a tabela [conexões híbridas e retransmissões do WCF][Pricing overview] na página detalhes de preços do barramento de serviço. Além dos preços mencionados, indicados naquela página, você é cobrado por transferências de dados associadas para saída fora do data center em que seu aplicativo é provisionado.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Como sou cobrado por Conexões Híbridas?
Aqui estão três exemplos de cenários de cobrança para Conexões Híbridas:

*   Cenário 1:
    *   Você tem um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas, instalado e em execução contínua durante todo o mês.
    *   Você envia 3 GB de dados pela conexão durante o mês. 
    *   O encargo total é de US$ 5.
*   Cenário 2:
    *   Você tem um único ouvinte, como uma instância do Gerenciador de Conexões Híbridas, instalado e em execução contínua durante todo o mês.
    *   Você envia 10 GB de dados pela conexão durante o mês.
    *   O encargo total é de US$ 7,50. São US$ 5 para a conexão e os primeiros 5 GB + US$ 2,50 para os 5 GB de dados adicionais.
*   Cenário 3:
    *   Você tem duas instâncias, A e B, do Gerenciador de Conexões Híbridas instaladas e em execução contínua durante o mês inteiro.
    *   Você envia 3 GB de dados pela conexão A durante o mês.
    *   Você envia 6 GB de dados pela conexão B durante o mês.
    *   O encargo total é de US$ 10,50. São US$ 5 para conexão A + US$ 5 para a conexão B + US$ 0,50 (para o sexto gigabyte na conexão B).

Observe que os preços usados nos exemplos são aplicáveis somente durante o período de versão prévia das Conexões Híbridas. Os preços estão sujeitos a alterações conforme a disponibilidade geral de Conexões Híbridas.

### <a name="how-are-hours-calculated-for-relay"></a>Como as horas são calculadas para Retransmissão?

A Retransmissão do WCF está disponível apenas nos namespaces da camada Standard. Preços e [cotas de conexão](../service-bus-messaging/service-bus-quotas.md) para retransmissões não mudaram em outros aspectos. Isso significa que as retransmissões continuarão sendo cobradas sobre o número de mensagens (não de operações) e horas de retransmissão. Para obter mais informações, consulte a tabela [“Conexões Híbridas e Retransmissões do WCF”](https://azure.microsoft.com/pricing/details/service-bus/) na página de detalhes de preços.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>E se eu tiver mais de um ouvinte conectado a uma retransmissão específica?
Em alguns casos, uma única retransmissão pode ter vários ouvintes conectados. Uma retransmissão é considerada aberta quando pelo menos um ouvinte de retransmissão está conectado a ela. Adicionar ouvintes a uma retransmissão aberta resulta em horas de retransmissão adicionais. O número de remetentes de retransmissão (clientes que invocam ou enviam mensagens para retransmissões) conectados a uma retransmissão não afeta o cálculo de horas de retransmissão.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Como esse medidor de mensagens é calculado para Retransmissões de WCF?
(**Isso se aplica somente a retransmissões WCF. As mensagens não são um custo para Conexões Híbridas.** )

Em geral, mensagens cobráveis para retransmissões são calculadas usando o mesmo método usado para entidades agenciadas (filas, tópicos e assinaturas) descrito anteriormente. No entanto, há algumas diferenças importantes.

Enviar uma mensagem para uma retransmissão do Barramento de Serviço é tratado como um envio de "passagem completa" ao ouvinte da retransmissão que recebe a mensagem. Não é tratado como uma operação de envio para a retransmissão do Barramento de Serviço, seguido por uma entrega ao ouvinte da retransmissão. Uma invocação de serviço do tipo solicitação-resposta (de até 64 KB) em relação a um ouvinte resulta em duas mensagens cobráveis: uma mensagem cobrável para a solicitação e uma mensagem cobrável para a resposta (supondo que a resposta também tenha 64 KB ou menos). Isso é diferente de usar uma fila para fazer a mediação entre um cliente e um serviço. Se você usar uma fila para mediar entre um cliente e um serviço, o mesmo padrão de solicitação-resposta exigirá que uma solicitação seja enviada à fila, seguida por uma remoção da fila/entrega da fila ao serviço. Isso é seguido pelo envio de uma resposta a outra fila e uma remoção da fila/entrega dessa fila ao cliente. Usando as mesmas suposições de tamanho (até 64 KB), o padrão de fila mediado resulta em 4 mensagens cobráveis. Você seria cobrado por duas vezes o número de mensagens para implementar o mesmo padrão que você obtém usando retransmissão. É claro que há vantagens em usar filas para atingir esse padrão, como durabilidade e nivelamento de carga. Essas vantagens podem justificar a despesa adicional.

As retransmissões abertas usando a associação do WCF **netTCPRelay** tratam as mensagens não como mensagens individuais, mas como um fluxo de dados que flui pelo sistema. Ao usar essa associação, somente o remetente e o ouvinte têm visibilidade do enquadramento das mensagens individuais enviadas e recebidas. Para retransmissões que usam a associação **netTCPRelay**, todos os dados são tratados como um fluxo para o cálculo das mensagens cobráveis. Nesse caso, o Barramento de Serviço calcula a quantidade total de dados enviados ou recebidos por meio de cada retransmissão individual por 5 minutos. Em seguida, ele divide essa quantidade total de dados por 64 KB para determinar o número de mensagens faturáveis para a retransmissão durante o período.

## <a name="quotas"></a>Cotas
| Nome da cota | Escopo |  Observações | Valor |
| --- | --- | --- | --- |
| Ouvintes simultâneos em uma retransmissão |Entidade |Solicitações subsequentes de conexões adicionais são rejeitadas e uma exceção é recebida pelo código de chamada. |25 |
| Conexões de retransmissão simultâneas por todos os pontos de extremidade de retransmissão em um namespace de serviço |Namespace |- |5\.000 |
| Pontos de extremidade de retransmissão por namespace de serviço |Namespace |- |10.000 |
| Tamanho de mensagem para retransmissões [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) e [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Namespace |As mensagens de entrada que excederem essas cotas serão rejeitadas e uma exceção será recebida pelo código de chamada. |64 KB |
| Tamanho de mensagem para retransmissões [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Namespace |Não há limite no tamanho da mensagem. |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>A retransmissão tem alguma cota de uso?
Por padrão, para qualquer serviço de nuvem, a Microsoft define uma cota de uso mensal agregada que é calculada para todas as assinaturas de um cliente. Sabemos que, às vezes, suas necessidades podem exceder esses limites. Você pode contatar o atendimento ao cliente a qualquer momento para que possamos compreender suas necessidades e ajustar esses limites adequadamente. Para o Barramento de Serviço, as cotas totais de uso são:

* 5 bilhões de mensagens
* 2 milhões de horas de retransmissão

Embora reservemos o direito de desabilitar uma conta que tenha excedido suas cotas de uso mensais, fornecemos uma notificação por email e fazemos várias tentativas de contatar o cliente antes de tomar qualquer medida. Os clientes que excedem essas cotas ainda são responsáveis por encargos em excesso.

### <a name="naming-restrictions"></a>Restrições de nomenclatura
Um nome de namespace de Retransmissão deve ter entre 6 e 50 caracteres.

## <a name="subscription-and-namespace-management"></a>Gerenciamento de assinaturas e de namespaces
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Como posso migrar um namespace para outra assinatura do Azure?

Para mover um namespace de uma assinatura do Azure para outra assinatura, você pode usar o [portal do Azure](https://portal.azure.com) ou comandos do PowerShell. Para mover um namespace para outra assinatura, o namespace já deve estar ativo. O usuário que executa os comandos deve ser Administrador nas assinaturas de origem e de destino.

#### <a name="azure-portal"></a>Portal do Azure

Para usar o portal do Azure para migrar namespaces de Retransmissão do Azure de uma assinatura para outra, consulte [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Para usar o PowerShell para mover um namespace de uma assinatura do Azure para outra, use a seguinte sequência de comandos. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um Administrador nas assinaturas de origem e de destino.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Solução de problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Quais são algumas das exceções geradas pelas APIs de Retransmissão do Azure e que ações sugeridas você pode executar?
Para obter uma descrição das exceções comuns e as ações sugeridas que você pode tomar, consulte [exceções][Relay exceptions]de retransmissão.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>O que é uma assinatura de acesso compartilhado e quais idiomas posso usar para gerar uma assinatura?
As SAS (Assinaturas de Acesso Compartilhado) são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. Para obter informações sobre como gerar suas próprias assinaturas em node. js, PHP, Python, Java, C e C#, consulte Autenticação do [barramento de serviço com assinaturas de acesso compartilhado][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>É possível colocar pontos de extremidade de retransmissão em uma lista de permissões?
Sim. O cliente de retransmissão faz conexões com o serviço de Retransmissão do Azure usando nomes de domínio totalmente qualificados. Os clientes podem adicionar uma entrada para `*.servicebus.windows.net` nos firewalls que dão suporte à lista de permissões de DNS.

## <a name="next-steps"></a>Próximas etapas
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
