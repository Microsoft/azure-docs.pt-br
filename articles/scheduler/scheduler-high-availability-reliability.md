---
title: Alta disponibilidade e confiabilidade
description: Saiba mais sobre alta disponibilidade e confiabilidade no Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368138"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Alta disponibilidade e confiabilidade para Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

O Agendador do Azure fornece [alta disponibilidade](/azure/architecture/framework/#resiliency) e confiabilidade para seus trabalhos. Para obter mais informações, veja [SLA para Agendador](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Alta disponibilidade

O Agendador do Azure [com alta disponibilidade] e usa implantação de serviço com redundância geográfica e replicação geográfica regional do trabalho.

### <a name="geo-redundant-service-deployment"></a>Implantação de serviço com redundância geográfica

O Agendador do Azure está disponível em quase [todas as regiões geográficas com suporte do Azure hoje](https://azure.microsoft.com/global-infrastructure/regions/#services). Portanto, se um datacenter do Azure em uma região hospedada ficar indisponível, você ainda poderá usar o Agendador do Azure devido às funcionalidades de failover do serviço tornam o Agendador disponível de outro datacenter.

### <a name="geo-regional-job-replication"></a>Replicação geográfica regional de trabalho

Seus próprios trabalhos no Agendador do Azure são replicados entre regiões do Azure. Portanto, se uma região tiver uma interrupção, o Agendador do Azure fará failover e garantirá que seus trabalhos sejam executados em outro datacenter na região geográfica emparelhada.

Por exemplo, se você tiver criado um trabalho no Centro Sul dos EUA, o Agendador do Azure replicará automaticamente esse trabalho no Centro Norte dos EUA. Se ocorrer uma falha no Centro-Sul dos EUA, o Agendador do Azure executará o trabalho no Centro-Norte dos EUA. 

![Replicação geográfica regional de trabalho](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

O Agendador do Azure também garante que seus dados permaneçam dentro da mesma região geográfica, porém mais ampla, no caso de ocorrer uma falha no Azure. Dessa forma, você não precisará duplicar seus trabalhos quando quiser apenas alta disponibilidade. O Agendador do Azure fornece automaticamente a alta disponibilidade para seus trabalhos.

## <a name="reliability"></a>Confiabilidade

O Agendador do Azure garante sua própria alta disponibilidade, mas adota uma abordagem diferente para os trabalhos criados pelo usuário. Por exemplo, suponha que seu trabalho invoque um ponto de extremidade HTTP que não está disponível. O Agendador do Azure ainda tenta executar o trabalho com êxito, oferecendo maneiras alternativas para tratamento de falhas: 

* Configure políticas de repetição.
* Configure pontos de extremidade alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Políticas de repetição

O Agendador do Azure permite configurar políticas de repetição. Se um trabalho falhar, por padrão, o Agendador tentará executar o trabalho novamente mais quatro vezes, a intervalos de 30 segundos. Você pode tornar essa política de repetição mais agressiva, como 10 vezes a intervalos de 30 segundos, ou menos agressiva, por exemplo, duas vezes a intervalos diários.

Por exemplo, suponha que você crie um trabalho semanal que chame um ponto de extremidade HTTP. Se o ponto de extremidade HTTP ficar indisponível por algumas horas, quando o trabalho for executado, não será conveniente aguardar outra semana para o trabalho ser executado novamente, o que acontecerá porque a política de repetição padrão não funcionará neste caso. Portanto, você talvez queira alterar a política de repetição padrão para que as repetições ocorram, por exemplo, a cada três horas, em vez de a cada 30 segundos. 

Para saber como configurar uma política de repetição, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Pontos de extremidade alternativos

Se seu trabalho do Agendador do Azure chamar um ponto de extremidade inacessível, mesmo após você seguir a política de repetição, o Agendador fará fallback para um ponto de extremidade alternativo que pode lidar com esses erros. Dessa forma, se você definir esse ponto de extremidade, o Agendador chamará esse ponto de extremidade, tornando seus próprios trabalhos altamente disponíveis quando ocorrerem falhas.

Por exemplo, este diagrama mostra como o Agendador segue a política de repetição ao chamar um serviço Web em Nova York. Se as tentativas falharem, o Agendador procurará um ponto de extremidade alternativo. Se o ponto de extremidade existir, o Agendador começará a enviar solicitações para o ponto de extremidade alternativo. A mesma política de repetição se aplica à ação original e à ação alternativa.

![Comportamento do Agendador com política de repetição e ponto de extremidade alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

O tipo de ação para a ação alternativa pode ser diferente da ação original. Por exemplo, embora a ação original chame um ponto de extremidade HTTP, a ação alternativa pode registrar erros em log usando uma fila de armazenamento, a fila do barramento de serviço ou a ação de tópico do barramento de serviço.

Para saber como configurar um ponto de extremidade alternativo, consulte [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="next-steps"></a>Próximas etapas

* [Hierarquia de entidades, terminologia e conceitos](scheduler-concepts-terms.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
* [Limites, cotas, valores padrão e códigos de erro](scheduler-limits-defaults-errors.md)