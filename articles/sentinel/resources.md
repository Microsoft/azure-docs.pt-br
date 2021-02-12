---
title: Recursos úteis ao trabalhar com o Azure Sentinel | Microsoft Docs
description: Este documento fornece uma lista de recursos úteis ao trabalhar com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: 11c5c77623fcc693210d5a42bf94c968e884fec3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920013"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com o Azure Sentinel



Este artigo lista os recursos que podem ajudá-lo a obter mais informações sobre como trabalhar com o Azure Sentinel.

- **Conectores de aplicativos lógicos do Azure**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os logs de auditoria do Azure Sentinel são mantidos nos [logs de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

As operações com suporte a seguir podem ser auditadas.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar pasta de trabalho  |Microsoft. insights/pastas de trabalho|
|Excluir pasta de trabalho    |Microsoft. insights/pastas de trabalho|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Excluir fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Excluir pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Atualizar regras de alerta |Microsoft. SecurityInsights/alertRules|
|Excluir regras de alerta |Microsoft. SecurityInsights/alertRules|
|Atualizar ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Excluir ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Atualizar indicadores   |Microsoft. SecurityInsights/bookmarks|
|Excluir indicadores   |Microsoft. SecurityInsights/bookmarks|
|Atualizar casos   |Microsoft. SecurityInsights/casos|
|Atualizar investigação de caso  |Microsoft. SecurityInsights/casos/investigações|
|Criar comentários de caso   |Microsoft. SecurityInsights/casos/comentários|
|Atualizar conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Excluir conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Atualizar configurações    |Microsoft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Exibir dados de auditoria e relatórios no Azure Sentinel

Você pode exibir esses dados transmitindo-os do log de atividades do Azure para o Azure Sentinel, em que você pode executar pesquisa e análise nele.

1. Conecte a fonte de dados da [atividade do Azure](connect-azure-activity.md) . Depois de fazer isso, os eventos de auditoria são transmitidos em uma nova tabela na tela de **logs** chamada AzureActivity.

1. Em seguida, consulte os dados usando KQL, como faria com qualquer outra tabela.

    Por exemplo, para descobrir quem foi o último usuário para editar uma regra de análise específica, use a seguinte consulta (substituindo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` pela ID da regra que você deseja verificar):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Documentação do fornecedor

| **Fornecedor**  | **Usar o incidente no Azure Sentinel** | **Link**|
|----|----|----|
| GitHub| Usado para acessar a página da Comunidade| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurar CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Curso da Linguagem de Consulta Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs e fóruns

Adoramos ouvir nossos usuários!

- **Poste suas perguntas** no [espaço do TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para o Azure Sentinel. 

- **Envie sugestões para aprimoramentos** por meio de nosso programa de [voz do usuário](https://feedback.azure.com/forums/920458-azure-sentinel) .

- **Exiba e comente** suas postagens no blog do Azure Sentinel:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)

Para obter mais informações sobre a segurança e a conformidade do Azure, consulte o [blog Microsoft Azure segurança e conformidade](https://techcommunity.microsoft.com/t5/microsoft-security-and/bg-p/MicrosoftSecurityandCompliance).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obtenha certificação!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Ler histórias de casos de uso do cliente](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

