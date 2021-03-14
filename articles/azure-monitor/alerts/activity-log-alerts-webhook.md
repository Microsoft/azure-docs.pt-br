---
title: Noções básicas sobre o esquema de webhook usado em alertas do log de atividades
description: Saiba mais sobre o esquema JSON que é enviado para uma URL de webhook quando um alerta do log de atividades é ativado.
ms.topic: conceptual
ms.date: 03/31/2017
ms.openlocfilehash: 31b9f4b41d741475a031efd4392c7df2fd2260c4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034329"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhook para alertas de log de atividades do Azure
Como parte da definição de um grupo de ações, você pode configurar pontos de extremidade de webhook para receber notificações de alerta do log de atividades. Os webhooks permitem rotear uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Este artigo mostra a aparência do conteúdo para o HTTP POST para um webhook.

Para saber mais sobre alertas do log de atividades, veja como [Criar alertas do log de atividades do Azure](./activity-log-alerts.md).

Para saber mais sobre grupos de ações, veja como [criar grupos de ações](./action-groups.md).

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](./alerts-common-schema.md), que fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure monitor, para suas integrações de webhook. [Saiba mais sobre as definições de esquema de alerta comuns.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>Autenticar o webhook
Opcionalmente, o webhook pode usar a autorização baseada em token para autenticação. O URI do webhook é salvo com uma ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Esquema de conteúdo
O conteúdo JSON contida na operação POST difere com base no campo de data.context.activityLog.eventSource do conteúdo.

> [!NOTE]
> Atualmente, a descrição que faz parte do evento do log de atividades é copiada para a propriedade **"Descrição do alerta"** disparada.
>
> Para alinhar a carga do log de atividades com outros tipos de alertas, a partir de 1º de abril de 2021, a propriedade de alerta disparada **"Descrição"** conterá a descrição da regra de alerta.
>
> Em preparação para essa alteração, criamos uma nova propriedade **"Descrição do evento do log de atividades"** para o alerta do log de atividades acionado. Essa nova propriedade será preenchida com a propriedade **"Description"** que já está disponível para uso. Isso significa que o novo campo **"Descrição do evento do log de atividades"** conterá a descrição que faz parte do evento do log de atividades.
>
> Examine suas regras de alerta, regras de ação, WebHooks, aplicativo lógico ou quaisquer outras configurações em que você possa estar usando a propriedade **"Descrição"** do alerta acionado e substitua-a pela propriedade **"Descrição do evento do log de atividades"** .
>
> Se sua condição (em suas regras de ação, WebHooks, aplicativo lógico ou qualquer outra configuração) for baseada na propriedade **"Descrição"** para alertas do log de atividades, talvez seja necessário modificá-la para que ela se baseie na propriedade **"Descrição do evento do log de atividades"** .
>
> Para preencher a nova propriedade **"Descrição"** , você pode adicionar uma descrição na definição de regra de alerta.
> ![Alertas do log de atividades acionados](media/activity-log-alerts-webhook/activity-log-alert-fired.png)

### <a name="common"></a>Comum

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administrativa

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Segurança

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Recomendação

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Para obter detalhes de esquema específico sobre alertas de log de atividades de notificação do serviço integridade, veja [Notificações de integridade do serviço](../../service-health/service-notifications.md). Além disso, saiba como [configurar notificações de webhook de integridade do serviço com suas soluções de gerenciamento de problemas existentes](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Nome do elemento | Descrição |
| --- | --- |
| status |Usado para alertas de métrica. Sempre definido como "ativado" para alertas do log de atividades. |
| contexto |Contexto do evento. |
| resourceProviderName |O provedor de recursos do recurso afetado. |
| conditionType |Sempre "Evento". |
| name |Nome da regra de alerta. |
| id |ID do recurso do alerta. |
| descrição |Descrição do alerta definida quando o alerta é criado. |
| subscriptionId |Id de assinatura do Azure. |
|  timestamp |Hora quando o evento foi gerado pelo serviço do Azure que processou a solicitação. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos do recurso afetado. |
| properties |Conjunto de pares `<Key, Value>` (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| event |Elemento que contém metadados sobre o evento. |
| autorização |As propriedades do controle de acesso baseado em função do Azure do evento. Essas propriedades geralmente incluem a ação, função e escopo. |
| category |Categoria do evento. Os valores com suporte são: Administrativo, Alerta, Segurança, ServiceHealth e Recomendação. |
| chamador |Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas do sistema. |
| correlationId |Geralmente um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem à mesma ação maior e geralmente compartilham uma correlationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo do evento. |
| eventSource |Nome do serviço ou infraestrutura do Azure que gerou o evento. |
| httpRequest |A solicitação geralmente inclui o clientRequestId, clientIpAddress e método HTTP (por exemplo, PUT). |
| nível |Um dos seguintes valores: Crítico, Erro, Aviso e Informativo. |
| operationId |Geralmente um GUID compartilhado entre os eventos correspondentes a uma única operação. |
| operationName |Nome da operação. |
| properties |Propriedades do evento. |
| status |Cadeia de caracteres. Status da operação. Os valores comuns incluem: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido. |
| subStatus |Geralmente inclui o código de status HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevam um substatus. Os valores de substatus comuns incluem: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504). |

Para obter detalhes de esquema específico em todos os outros alertas do log de atividades, veja [Visão geral do log de atividades do Azure](../essentials/platform-logs-overview.md).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o log de atividades](../essentials/platform-logs-overview.md).
* [Execute os scripts de automação do Azure (Runbooks) nos alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Usar aplicativo lógico para enviar um SMS por meio do Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* [Usar aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.
* [Usar aplicativo lógico para enviar uma mensagem a uma fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do log de atividades.