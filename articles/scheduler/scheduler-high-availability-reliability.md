---
title: Alta disponibilidade e confiabilidade do Agendador
description: Alta disponibilidade e confiabilidade do Agendador
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-high-availability-and-reliability"></a>Alta disponibilidade e confiabilidade do Agendador
## <a name="azure-scheduler-high-availability"></a>Alta disponibilidade do Agendador do Azure
Como um núcleo de serviços da plataforma Azure, o Agendador do Azure é altamente disponível e possui implantação do serviço de redundância geográfica e replicação geográfica regional do trabalho.

### <a name="geo-redundant-service-deployment"></a>Implantação de serviço com redundância geográfica
O Agendador do Azure está disponível por meio da interface do usuário em quase todos as regiões geográficas que estão atualmente no Azure. A lista de regiões na qual o Agendador do Azure está disponível está [listada aqui](https://azure.microsoft.com/regions/#services). Se um data center em uma região hospedada é renderizado indisponível, os recursos de failover do Agendador do Azure fazem com que o serviço esteja disponível a partir de outro data center.

### <a name="geo-regional-job-replication"></a>Replicação geográfica regional de trabalho
O Agendador do Azure não é somente disponível na interface inicial para solicitações de gerenciamento, mas seu trabalho é também replicado geograficamente. Quando houver uma interrupção em uma região, o Agendador do Azure faz failover e garante que o trabalho é executado a partir de outro data center na região geográfica emparelhada.

Por exemplo, se você tiver criado um trabalho no Centro Sul dos EUA, o Agendador do Azure replica automaticamente esse trabalho no Centro Norte dos EUA. Quando há uma falha no Centro Sul dos EUA, o Agendador do Azure garante que o trabalho é executado a partir do Centro Norte dos EUA. 

![][1]

Como resultado, o Agendador do Azure garante que seus dados permaneçam na mesma região geográfica maior no caso de uma falha do Azure. Como resultado, não é necessário duplicar o trabalho apenas para adicionar a alta disponibilidade – o Agendador do Azure fornece automaticamente os recursos de alta disponibilidade para os seus trabalhos.

## <a name="azure-scheduler-reliability"></a>Confiabilidade do Agendador do Azure
O Agendador do Azure garante sua própria alta disponibilidade e adota uma abordagem diferente para os trabalhos criados pelo usuário. Por exemplo, seu trabalho pode invocar um ponto de extremidade HTTP que não está disponível. No entanto, o Agendador do Azure tenta executar seu trabalho com êxito, fornecendo opções alternativas para lidar com a falha. O Agendador do Azure faz isso de duas maneiras:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Política de repetição configurável via "retryPolicy"
O Agendador do Azure permite que você configure uma política de repetição. Por padrão, se um trabalho falhar, o Agendador tenta o trabalho novamente quatro vezes mais, em intervalos de 30 segundos. Novamente, você pode configurar essa política de repetição para ser mais agressiva (por exemplo, dez vezes, em intervalos de 30 segundos) ou menos rígida (por exemplo, duas vezes, em intervalos diários).

Como um exemplo de quando isso pode ajudar, você pode criar um trabalho que é executado uma vez por semana e invoca um ponto de extremidade HTTP. Se o ponto de extremidade HTTP estiver inativo por algumas horas, quando o trabalho for executado, não convém aguardar uma semana mais para que o trabalho seja executado novamente, já que até mesmo a política de repetição padrão falhará. Nesses casos, você pode reconfigurar a política de repetição padrão para repetir a cada três horas (por exemplo) em vez de a cada 30 segundos.

Para saber como configurar uma política de repetição, confira [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Capacidade de configuração de ponto de extremidade alternativo via "errorAction"
Se o ponto de extremidade de destino para o trabalho do Agendador do Azure permanecer inacessível, o Agendador do Azure reverterá para o ponto de extremidade de tratamento de erros alternativo após seguir a política de repetição. Se um ponto de extremidade de tratamento de erro alternativo for configurado, o Agendador do Azure o chama. Com um ponto de extremidade alternativo, seus próprios trabalhos são altamente disponíveis em caso de falha.

Por exemplo, no diagrama abaixo, o Agendador do Azure segue a sua política de repetição para acertar um serviço Web de Nova York. Depois que as tentativas falharem, ele verifica se há uma alternativa. Em seguida, ele segue em frente e começa a fazer solicitações para a alternativa com a mesma política de repetição.

![][2]

Observe que a mesma política de repetição se aplica à ação original e à ação de erro alternativa. Também é possível ter um tipo de ação da ação de erro alternativa que seja diferente do tipo de ação da ação principal. Por exemplo, enquanto a ação principal pode estar invocando um ponto de extremidade HTTP, a ação de erro pode, em vez disso, ser uma ação de fila de armazenamento, de fila do barramento de serviço ou de tópico do barramento de serviço que realiza o log dos erros.

Para saber como configurar um ponto de extremidade alternativo, confira [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
