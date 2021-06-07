---
title: Planos e cobrança
description: Saiba mais sobre planos e cobrança do Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898466"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Planos e Cobrança do Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar com os trabalhos que você já configurou no Agendador, [migre para os Aplicativos Lógicos do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rápido possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

## <a name="job-collection-plans"></a>Planos de coleção de trabalhos

No Agendador do Azure, uma coleção de trabalhos contém um número específico de trabalhos. A coleção de trabalhos é a entidade faturável e fornecida nos planos Standard, P10 Premium e P20 Premium, que são descritos aqui: 

| Plano de coleção de trabalhos | Máximo de trabalhos por coleção | Recorrência máxima | Máximo de coleções de trabalhos por assinatura | Limites | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 trabalhos por coleção | Um por minuto. Não pode executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 100 contas de coleções de trabalhos Standard. | Acesso ao conjunto de recursos completo do Agendador | 
| **P10 Premium** | 50 trabalhos por coleção | Um por minuto. Não pode executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 10.000 contas de coleções de trabalhos P10 Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">Contate-nos</a>. | Acesso ao conjunto de recursos completo do Agendador |
| **P20 Premium** | 1000 trabalhos por coleção | Um por minuto. Não pode executar trabalhos com mais frequência do que um por minuto. | Cada assinatura do Azure pode ter até 5.000 contas de coleções de trabalhos P20 Premium. Para obter mais coleções, <a href="mailto:wapteams@microsoft.com">Contate-nos</a>. | Acesso ao conjunto de recursos completo do Agendador |
|||||| 

## <a name="pricing"></a>Preços

Para obter detalhes sobre preços, confira [Preços do Agendador](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Fazer upgrade ou downgrade de planos

A qualquer momento, é possível fazer upgrade ou downgrade de um plano de coleção de trabalhos entre os planos Standard, P10 Premium e P20 Premium.

## <a name="active-status-and-billing"></a>Status ativo e cobrança

As coleções de trabalhos ficam sempre ativas, a menos que a assinatura do Azure inteira entre em algum estado desabilitado temporário devido a problemas de cobrança. E, embora seja possível desabilitar todos os trabalhos em uma coleção de trabalhos por uma única operação, essa ação não altera o status de cobrança da coleção de trabalhos, então, essa coleção *ainda* será cobrada. As coleções de trabalhos vazias são consideradas ativas e são cobradas.

Para certificar-se de que uma coleção de trabalhos não seja cobrada, é necessário excluir a coleção de trabalhos.

## <a name="standard-billable-units"></a>Unidades faturáveis standard

Uma unidade faturável standard pode incluir até 10 coleções de trabalhos Standard. Como uma coleção de trabalhos Standard pode ter até 50 trabalhos por coleção, uma unidade de cobrança standard permite que a assinatura do Azure tenha até 500 trabalhos, ou até cerca de 22 *milhões* de execuções de trabalhos por mês. Esta lista explica como você é cobrado com base em vários números de coleções de trabalhos Standard:

* Se você tiver entre 1 e 10 coleções de trabalhos Standard, será cobrado por uma unidade de cobrança standard. 

* Se você tiver entre 11 e 20 coleções de trabalhos Standard, será cobrado por duas unidades de cobrança standard. 

* Se você tiver entre 21 e 30 coleções de trabalhos Standard, será cobrado por três unidades de cobrança standard e assim por diante.

## <a name="p10-premium-billable-units"></a>Unidades faturáveis P10 premium

Uma unidade faturável P10 premium pode incluir até 10.000 coleções de trabalhos P10 Premium. Como uma coleção de trabalhos P10 Premium pode ter até 50 trabalhos por coleção, uma unidade de cobrança P10 premium permite que a assinatura do Azure tenha até 500.000 trabalhos, ou até cerca de 22 *bilhões* de execuções de trabalhos por mês. 

Coleções de trabalhos P10 Premium fornecem os mesmos recursos que as coleções de trabalhos Standard, mas oferecem uma redução de preço para os aplicativos que exigem várias coleções de trabalho e fornecem mais escalabilidade. Esta lista explica como você é cobrado com base em vários números de coleções de trabalhos P10 Premium:

* Se você tiver entre 1 e 10.000 coleções de trabalhos P10 Premium, será cobrado por uma unidade de cobrança P10 premium. 

* Se você tiver entre 10.001 e 20.000 coleções de trabalhos P10 Premium, será cobrado por 2 unidades de cobrança P10 premium e assim por diante.

## <a name="p20-premium-billable-units"></a>Unidades faturáveis P20 premium

Uma unidade faturável P20 premium pode incluir até 5.000 coleções de trabalhos P20 Premium. Como uma coleção de trabalhos P20 Premium pode ter até 1.000 trabalhos por coleção, uma unidade de cobrança P20 premium permite que a assinatura do Azure tenha até 5.000.000 trabalhos, ou até cerca de 220 *bilhões* de execuções de trabalhos por mês.

As coleções de trabalhos P20 Premium fornecem os mesmos recursos das coleções de trabalhos P10 Premium, mas também dão suporte a um número maior de trabalhos por coleção e a um número total maior geral de trabalhos do que o P10 Premium, fornecendo mais escalabilidade.

## <a name="plan-comparison"></a>Comparação de planos

* Se você tiver mais de 100 coleções de trabalhos Standard (10 unidades de cobrança standard), então pode obter um melhor negócio tendo todas as coleções de trabalhos em um plano Premium.

* Se você tiver uma coleção de trabalhos Standard e uma coleção de trabalhos Premium, será cobrada uma unidade de cobrança standard *e* uma unidade de cobrança premium.

  O serviço do Agendador faz a cobrança com base no número de coleções de trabalhos ativas que são definidas como standard ou premium.

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)