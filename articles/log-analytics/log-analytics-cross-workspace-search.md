---
title: "Executar consultas em espaços de trabalho do Azure Log Analytics | Microsoft Docs"
description: "Este artigo descreve como você pode consultar em vários espaços de trabalho em sua assinatura com os exemplos a seguir."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Executar consultas em diversos espaços de trabalho do Azure Log Analytics

Anteriormente com o Azure Log Analytics, você poderia apenas analisar dados no espaço de trabalho atual e isso limitava sua capacidade de consultar em vários espaços de trabalho definidos em sua assinatura.  

Agora, você pode consultar em vários espaços de trabalho, fornecendo uma exibição de todo o sistema de seus dados.  Só é possível executar esse tipo de consulta no [Portal avançado](log-analytics-log-search-portals.md#advanced-analytics-portal), não no portal do Azure.  

## <a name="querying-across-log-analytics-workspaces"></a>Consultando em espaços de trabalho do Log Analytics
Para fazer referência a outro espaço de trabalho em sua consulta, use o identificador de *espaço de trabalho*.  Por exemplo, a consulta a seguir retorna contagens resumidas de atualizações necessárias à sua classificação da tabela de Atualização de espaço de trabalho atual e outro espaço de trabalho chamado *contosoretail-it*.  


## <a name="identifying-resources"></a>Identificando recursos
A identificação de um espaço de trabalho pode ser executada de várias maneiras:

* Nome de recurso – é um nome legível do espaço de trabalho, também conhecido como *nome do componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A identificação de um espaço de trabalho por seu nome presume que ele seja exclusivo em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do espaço de trabalho, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do espaço de trabalho – uma ID do espaço de trabalho é o identificador exclusivo e imutável atribuído a cada espaço de trabalho representado como um identificador global exclusivo (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do espaço de trabalho. Use isso quando o nome do recurso for ambíguo.  Para espaços de trabalho, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Por exemplo:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Próximas etapas

Examine a [referência de pesquisa de logs do Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) para exibir todas as opções de sintaxe de consulta disponíveis no Log Analytics.    