---
title: Limites e cotas e limitações no Agendador do Azure
description: Saiba mais sobre limites, cotas, valores padrão e limitações do Agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78898519"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites e cotas e limitações no Agendador do Azure

> [!IMPORTANT]
> Os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) estão substituindo o Agendador do Azure, que está [sendo desativado](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Para continuar trabalhando com os trabalhos que você configurou no Agendador, [migre para o aplicativo lógico do Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) assim que possível. 
>
> O Agendador não está mais disponível no portal do Azure, mas a [API REST](/rest/api/scheduler) e os [cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md) permanecem disponíveis no momento para que você possa gerenciar seus trabalhos e suas coleções de trabalhos.

## <a name="limits-quotas-and-thresholds"></a>Limites, cotas e limitações

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Cabeçalho x-ms-request-id

Cada solicitação feita no serviço de Agendador retorna um cabeçalho de resposta chamado **x-MS-Request-ID**. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação. Portanto, se você confirmar que uma solicitação está formatada corretamente e ela falhar de forma consistente, relate o erro à Microsoft fornecendo o valor do cabeçalho de resposta **x-ms-request-id** e incluindo estes detalhes: 

* O valor de **x-ms-request-id**
* O tempo aproximado quando a solicitação foi feita 
* Os identificadores da assinatura do Azure, da coleção de trabalhos e do trabalho 
* O tipo de operação que a solicitação tentou

## <a name="next-steps"></a>Próximas etapas

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Planos e Cobrança do Agendador do Azure](scheduler-plans-billing.md)
* [Referência da API REST do Agendador do Azure](/rest/api/scheduler)
* [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)