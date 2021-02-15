---
title: Esquemas para os alertas da central de segurança do Azure
description: Este artigo descreve os esquemas diferentes usados pela central de segurança do Azure para alertas de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: f9b3be69ab57c0abf7523169303def899f325229
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789209"
---
# <a name="security-alerts-schemas"></a>Esquemas de alertas de segurança

Se sua assinatura tiver o Azure defender habilitado, você receberá alertas de segurança quando a central de segurança detectar ameaças aos seus recursos.

Você pode exibir esses alertas de segurança nas páginas de **proteção contra ameaças** da central de segurança do Azure ou por meio de ferramentas externas, como:

- [Azure Sentinel](../sentinel/index.yml) – Siem nativo de nuvem da Microsoft. O conector do Sentinel Obtém alertas da central de segurança do Azure e os envia para o [espaço de trabalho log Analytics](../azure-monitor/learn/quick-create-workspace.md) para o Azure Sentinel.
- SIEMs de terceiros – enviam dados para os [hubs de eventos do Azure](../event-hubs/index.yml). Em seguida, integre os dados do hub de eventos a um SIEM de terceiros. Saiba mais em [transmitir alertas para uma solução Siem, disparar ou de gerenciamento de serviços de ti](export-to-siem.md).
- [A API REST](/rest/api/securitycenter/) -se você estiver usando a API REST para acessar alertas, consulte a [documentação da API de alertas online](/rest/api/securitycenter/alerts).

Se você estiver usando qualquer método programático para consumir os alertas, precisará do esquema correto para localizar os campos que são relevantes para você. Além disso, se você estiver exportando para um hub de eventos ou tentar disparar a automação de fluxo de trabalho com conectores HTTP genéricos, use os esquemas para analisar corretamente os objetos JSON.

>[!IMPORTANT]
> O esquema é ligeiramente diferente para cada um desses cenários, portanto, certifique-se de selecionar a guia relevante abaixo.


## <a name="the-schemas"></a>Os esquemas 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automação de fluxo de trabalho e exportação contínua para o Hub de eventos](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Exemplo de JSON para alertas enviados a aplicativos lógicos, Hub de eventos e SIEMs de terceiros

Abaixo, você encontrará o esquema dos eventos de alerta passados para:

- Instâncias do aplicativo lógico do Azure que foram configuradas na automação de fluxo de trabalho da central de segurança
- Hub de eventos do Azure usando o recurso de exportação contínua da central de segurança

Para obter mais informações sobre o recurso de automação de fluxo de trabalho, consulte [automatizar respostas para gatilhos da central de segurança](workflow-automation.md).

Para obter mais informações sobre a exportação contínua, consulte [Exportar continuamente os dados da central de segurança](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Espaços de trabalho do Azure Sentinel e Log Analytics](#tab/schema-sentinel)

O conector do Sentinel Obtém alertas da central de segurança do Azure e os envia para o espaço de trabalho Log Analytics para o Azure Sentinel. 

Para criar um caso ou um incidente Sentinel usando alertas da central de segurança, você precisará do esquema para esses alertas mostrados abaixo. 

Para obter mais informações sobre o Azure Sentinel, consulte [a documentação](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Log de atividades do Azure](#tab/schema-activitylog)

A central de segurança do Azure audita os alertas de segurança gerados como eventos no log de atividades do Azure.

Você pode exibir os eventos de alertas de segurança no log de atividades pesquisando o evento ativar alerta, conforme mostrado:

[![Pesquisando o log de atividades para o evento ativar alerta](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Exemplo de JSON para alertas enviados ao log de atividades do Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>O modelo de dados do esquema

|Campo|Descrição|
|----|----|
|**meios**|Constante, "operação"|
|**correlationId**|A ID do alerta da central de segurança do Azure|
|**descrição**|Descrição do alerta|
|**eventDataId**|Consulte CorrelationId|
|**eventName**|O valor e os subcampos localizadas contêm o nome de exibição do alerta|
|**category**|O valor e os subcampos localizadas são constantes-"segurança"|
|**eventTimestamp**|Carimbo de data/hora UTC para quando o alerta foi gerado|
|**id**|A ID do alerta totalmente qualificado|
|**level**|Constante, "informativo"|
|**operationId**|Consulte CorrelationId|
|**operationName**|O campo valor é constante-"Microsoft. Security/Locations/Alerts/Activate/Action", e o valor localizado será "ativar alerta" (possivelmente pode ser localizado par a localidade do usuário)|
|**resourceGroupName**|Incluirá o nome do grupo de recursos|
|**resourceProviderName**|Os subcampos valor e localizevalue são constantes-"Microsoft. Security"|
|**resourceType**|Os subcampos valor e localizadavalue são constantes-"Microsoft. Security/Locations/Alerts"|
|**resourceId**|A ID de recurso do Azure totalmente qualificada|
|**status**|Os subcampos valor e localizevalue são constantes-"ativas"|
|**subStatus**|Os subcampos valor e localizador estão vazios|
|**submissionTimestamp**|O carimbo de data/hora UTC do envio do evento para o log de atividades|
|**subscriptionId**|A ID da assinatura do recurso comprometido|
|**properties**|Um conjunto JSON de propriedades adicionais pertencentes ao alerta. Eles podem mudar de um alerta para o outro, no entanto, os campos a seguir aparecerão em todos os alertas:<br>-severidade: a gravidade do ataque<br>-compromisedEntity: o nome do recurso comprometido<br>-remediationSteps: matriz de etapas de correção a serem executadas<br>-intenção: a intenção de Kill chain do alerta. As tentativas possíveis são documentadas na [tabela intenções](alerts-reference.md#intentions)|
|**relatedEvents**|Matriz de constante vazia|
|||





### <a name="ms-graph-api"></a>[MS API do Graph](#tab/schema-graphapi)

Microsoft Graph é o gateway para dados e inteligência em Microsoft 365. Ele fornece um modelo de programação unificado que você pode usar para acessar a enorme quantidade de dados em Microsoft 365, Windows 10 e Enterprise Mobility + Security. Use a riqueza de dados em Microsoft Graph para criar aplicativos para organizações e consumidores que interagem com milhões de usuários.

O esquema e uma representação JSON para alertas de segurança enviados ao MS Graph estão disponíveis na [documentação do Microsoft Graph](/graph/api/resources/alert?preserve-view=true&view=graph-rest-1.0).

---


## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu os esquemas que as ferramentas de proteção contra ameaças do Azure Security Center usam ao enviar informações de alerta de segurança.

Para obter mais informações sobre as maneiras de acessar alertas de segurança de fora da central de segurança, consulte as seguintes páginas:

- [Azure Sentinel](../sentinel/index.yml) -Siem nativo de nuvem da Microsoft
- [Hubs de eventos do Azure](../event-hubs/index.yml) -serviço de ingestão de dados em tempo real, totalmente gerenciado pela Microsoft
- [Exportar continuamente os dados da central de segurança](continuous-export.md)
- [Espaços de trabalho do log Analytics](../azure-monitor/learn/quick-create-workspace.md) -Azure monitor armazena dados de log em um espaço de trabalho log Analytics, um contêiner que inclui informações de dados e configuração