---
title: Recuperação de desastre geográfico – Hubs de Eventos do Azure | Microsoft Docs
description: Como usar regiões geográficas para fazer failover e executar a recuperação de desastre nos Hubs de Eventos do Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 56077d018c1ae62809d51fc66d7f5aff93fb4c02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821856"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hubs de Eventos do Azure – Recuperação de desastre geográfico 

Quando datacenters ou regiões inteiras do Azure (se nenhuma [zona de disponibilidade](../availability-zones/az-overview.md) for usada) enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Como tal, *a recuperação de desastre em área geográfica* e a *replicação geográfica* são recursos importantes para qualquer empresa. Os Hubs de Eventos do Azure dão suporte à recuperação de desastre de área geográfica e à replicação geográfica no nível do namespace. 

O recurso de recuperação de desastres em área geográfica fica globalmente disponível para o SKU Padrão dos Hubs de Eventos.

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante observar a diferença entre "interrupção" e "desastres". Uma *interrupção* é uma indisponibilidade temporária dos Hubs de Eventos do Azure e pode afetar alguns componentes do serviço, como um repositório de mensagens ou até mesmo o datacenter inteiro. No entanto, depois que o problema for corrigido, os Hubs de Eventos ficarão disponíveis novamente. Normalmente, uma interrupção não causa a perda de mensagens ou de outros dados. Um exemplo de tal interrupção pode ser uma falha de energia no datacenter. Algumas falhas são apenas perdas de conexão curtas devido a problemas de rede ou transitórios. 

Um *desastre* é definido como a perda permanente ou de longo prazo de um cluster dos Hubs de Eventos, uma região do Azure ou um datacenter. A região ou o datacenter pode ou não ficar disponível novamente ou pode ficar inativo por horas ou dias. Outros exemplos desses desastres são incêndios, enchentes ou terremoto. Um desastre que se torne permanente pode causar a perda de algumas mensagens, alguns eventos ou outros dados. No entanto, na maioria dos casos, não deve haver perda de dados e as mensagens poderão ser recuperadas depois que do backup do data center.

O recurso de recuperação de desastre de área geográfica dos Hubs de Eventos do Azure é uma solução de recuperação de desastre. Os conceitos e o fluxo de trabalho descrito neste artigo se aplicam a cenários de desastre e não a falhas transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastre no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Termos e conceitos básicos

O recurso de recuperação de desastre implementa a recuperação de desastre dos metadados e se baseia em namespaces de recuperação de desastre primário e secundário. Observe que o recurso de recuperação de desastre em área geográfica está disponível somente para a [SKU Padrão](https://azure.microsoft.com/pricing/details/event-hubs/). Você não precisa fazer nenhuma alteração de cadeia de conexão, já que a conexão é feita por meio de um alias.

Os seguintes termos são usados neste artigo:

-  *Alias*: o nome para uma configuração de recuperação de desastre que você configurou. O alias fornece uma única cadeia de conexão estável do FQDN (Nome de Domínio Totalmente Qualificado). Aplicativos usam essa cadeia de conexão de alias para conectarem-se a um namespace. 

-  *Namespace primário/secundário*: os namespaces que correspondem ao alias. O namespace primário é "ativo" e recebe mensagens (pode ser um namespace existente ou novo). O namespace secundário "passivo" e não recebe mensagens. Os metadados entre os dois estão sincronizados, para que ambos possam aceitar mensagens continuamente sem quaisquer alterações no código do aplicativo ou na cadeia de conexão. Para garantir que apenas o namespace ativo receba mensagens, você deve usar o alias. 

-  *Metadados*: entidades, como hubs de eventos e os grupos de consumidores; e suas propriedades do serviço que são associadas ao namespace. Observe que somente entidades e suas configurações são replicadas automaticamente. Mensagens e eventos não são replicados. 

-  *Failover*: o processo de ativação do namespace secundário.

## <a name="setup-and-failover-flow"></a>Instalação e fluxo de failover

A seção a seguir é uma visão geral do processo de failover e explica como configurar o failover inicial. 

![1][]

### <a name="setup"></a>Configuração

Primeiro crie ou use um namespace primário existente e um novo namespace secundário, depois emparelhe os dois. Esse emparelhamento fornece um alias que você pode usar para se conectar. Como você usa um alias, não precisa alterar cadeias de conexão. Somente novos namespaces podem ser adicionados ao emparelhamento de failover. Por fim, você deve adicionar um monitoramento para detectar se um failover é necessário. Na maioria dos casos, o serviço é uma parte de um grande ecossistema, assim, failovers automáticos raramente são possíveis, uma vez que failovers devem ser executados em sincronia com o subsistema ou a infraestrutura restante.

### <a name="example"></a>Exemplo

Em um exemplo desse cenário, considere uma solução de ponto de venda (PDV) que emita mensagens ou eventos. Os Hubs de Eventos transmitem esses eventos para uma solução de mapeamento ou de reformatação, que encaminha dados mapeado para outro sistema para processamento adicional. Nesse ponto, todos esses sistemas podem ser hospedados na mesma região do Azure. A decisão sobre quando fazer o failover e em qual parte depende do fluxo de dados em sua infraestrutura. 

Você pode automatizar o failover tanto com sistemas de monitoramento ou com soluções de monitoramento personalizadas. No entanto, essa automação precisa de planejamento e trabalho adicionais, o que está fora do escopo deste artigo.

### <a name="failover-flow"></a>Fluxo do failover

Se você iniciar o failover, as duas etapas são necessárias:

1. Caso ocorra outra interrupção, você deve ser capaz de fazer failover novamente. Portanto, configure outro namespace passivo e atualize o emparelhamento. 

2. Faça pull das mensagens do namespace primário anterior assim que estiver disponível novamente. Depois disso, use esse namespace para mensagens regulares fora de sua configuração de recuperação geográfica ou exclua o namespace primário antigo.

> [!NOTE]
> Há suporte apenas para semânticas encaminhadas com falha. Nesse cenário, você faz o failover e emparelha novamente com um novo namespace. Não há suporte para failback, em um cluster do SQL por exemplo. 

![2][]

## <a name="management"></a>Gerenciamento

Se você cometeu um erro, por exemplo, emparelhou as regiões erradas durante a configuração inicial, você pode interromper o emparelhamento dos dois namespaces a qualquer momento. Se você quiser usar os namespaces emparelhados como namespaces regulares, exclua o alias.

## <a name="samples"></a>Exemplos

O [exemplo no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) mostra como configurar e iniciar um failover. Esse exemplo demonstra os seguintes conceitos:

- Configurações necessárias no Azure Active Directory para usar o Azure Resource Manager com os Hubs de Eventos. 
- Etapas necessárias para executar o exemplo de código. 
- Envie e receba do namespace primário atual. 

## <a name="considerations"></a>Considerações

Observe as seguintes considerações a serem lembradas quanto a esta versão:

1. Em seu planejamento de failover, você também deve considerar o fator tempo. Por exemplo, se você perder a conectividade por mais de 15 a 20 minutos, pode decidir iniciar o failover. 
 
2. O fato de que nenhum dado seja replicado significa que sessões atualmente ativas não são replicadas. Além disso, a detecção duplicada e mensagens programadas podem não funcionar. Novas sessões, mensagens programadas e novas duplicatas funcionarão. 

3. O failover de uma infraestrutura complexa distribuída deve ser [testado](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) pelo menos uma vez. 

4. A sincronização de entidades pode levar algum tempo, cerca de 50 a 100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidades 

O SKU Standard dos Hubs de Eventos oferece suporte às [Zonas de Disponibilidade](../availability-zones/az-overview.md), fornecendo locais isolados de falhas dentro de uma região do Azure. 

> [!NOTE]
> O suporte a Zonas de Disponibilidade para o Standard do Hubs de Eventos do Azure só é oferecido nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) em que existem zonas de disponibilidade.

Você pode habilitar as Zonas de Disponibilidade apenas em novos namespaces usando o portal do Azure. Os Hubs de Eventos não dão suporte à migração dos namespaces existentes. Você não pode desabilitar a redundância de zona depois de habilitá-la em seu namespace.

![3][]

## <a name="next-steps"></a>Próximas etapas

* O [exemplo de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) apresenta um fluxo de trabalho simples que cria um emparelhamento de área geográfica e inicia um failover para um cenário de recuperação de desastre.
* A [referência da API REST](/rest/api/eventhub/disasterrecoveryconfigs) descreve as APIs para executar a configuração de recuperação de desastres de replicação geográfica.

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png