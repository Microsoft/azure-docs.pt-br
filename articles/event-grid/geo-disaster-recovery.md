---
title: Recuperação de desastre geográfica na grade de eventos do Azure | Microsoft Docs
description: Descreve como a grade de eventos do Azure dá suporte à GeoDR (recuperação de desastre geográfica) automaticamente.
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94980840"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperação de desastre geográfica no lado do servidor na grade de eventos do Azure
A grade de eventos agora tem uma GeoDR (recuperação de desastre geográfica) automática de metadados não apenas para novos, mas todos os domínios, tópicos e assinaturas de evento existentes. Se uma região inteira do Azure falhar, a grade de eventos já terá todos os seus metadados de infraestrutura relacionados a eventos sincronizados com uma região emparelhada. Seus novos eventos começarão a fluir novamente sem nenhuma intervenção por você. 

A recuperação de desastres é medida com duas métricas:

- Objetivo de ponto de recuperação (RPO): os minutos ou horas de dados que podem ser perdidos.
- RTO (objetivo de tempo de recuperação): os minutos ou horas em que o serviço pode estar inoperante.

O failover automático da grade de eventos tem RPOs e RTOs diferentes para seus metadados (assinaturas de evento etc.) e dados (eventos). Se você precisar de uma especificação diferente das seguintes, ainda poderá implementar seu próprio [failover do lado do cliente usando as APIs de integridade do tópico](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>RPO (Objetivo de Ponto de Recuperação)
- **RPO de metadados**: zero minutos. Sempre que um recurso é criado na grade de eventos, ele é imediatamente replicado entre regiões. Quando ocorre um failover, nenhum metadado é perdido.
- **RPO de dados**: se o sistema estiver íntegro e atualizado no tráfego existente no momento do failover regional, o RPO de eventos será de cerca de 5 minutos.

## <a name="recovery-time-objective-rto"></a>RTO (Objetivo de Tempo de Recuperação)
- **RTO de metadados**: embora geralmente aconteça muito mais rapidamente, dentro de 60 minutos, a grade de eventos começará a aceitar chamadas de criação/atualização/exclusão para tópicos e assinaturas.
- **RTO de dados**: como metadados, geralmente acontece muito mais rapidamente, no entanto, dentro de 60 minutos, a grade de eventos começará a aceitar novo tráfego após um failover regional.

> [!NOTE]
> O custo para GeoDR de metadados na grade de eventos é: $0.


## <a name="next-steps"></a>Próximas etapas
Se você quiser implementar sua própria lógica de failover do lado do cliente, consulte [# criar sua própria recuperação de desastre para tópicos personalizados na grade de eventos](custom-disaster-recovery.md)
