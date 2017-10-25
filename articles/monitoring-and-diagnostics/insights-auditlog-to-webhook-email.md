---
title: Chamar um webhook em alertas do Log de Atividades do Azure | Microsoft Docs
description: "Rotear eventos de log de atividade para outros serviços para ações personalizadas. Por exemplo, envie SMS, registre bugs ou notifique uma equipe por meio do serviço de bate-papo/mensagens."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 341ab32ad0ec691285fbf1537ee298ab30156a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Chamar um webhook em alertas do Log de Atividades do Azure
Os webhooks permitem rotear uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Você pode usar um webhook em um alerta para roteá-lo aos serviços que enviam SMS, registrar bugs, notificar uma equipe por meio de serviços de bate-papo/mensagens ou qualquer outra ação. Este artigo descreve como configurar um webhook para ser chamado quando um alerta do Log de Atividades do Azure é disparado. Também mostra a aparência do conteúdo do HTTP POST para um webhook. Para obter informações sobre a configuração e o esquema de um alerta de métrica do Azure, [consulte esta página](insights-webhooks-alerts.md). Você também pode configurar um alerta do Log de Atividades para enviar um email quando estiver ativado.

> [!NOTE]
> Atualmente, esse recurso está em visualização e será removido em algum momento no futuro.
>
>

Você pode configurar um alerta do Log de Atividades usando os [Cmdlets do Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [CLI entre plataformas](insights-cli-samples.md#work-with-alerts) ou [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx). No momento, você não pode definir um usando o portal do Azure.

## <a name="authenticating-the-webhook"></a>Autenticação do webhook
O webhook pode autenticar usando um destes métodos:

1. **Autorização baseada em token** - O URI do webhook é salvo com uma ID de token, por exemplo, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorização básica** - O URI do webhook é salvo com um nome de usuário e senha, por exemplo, `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Esquema de conteúdo
A operação POST contém o seguinte esquema e carga JSON para todos os alertas baseados em Log de Atividade. Esse esquema é semelhante àquele usado por alertas baseados em métrica.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Nome do elemento | Descrição |
| --- | --- |
| status |Usado para alertas de métrica. Sempre definido como "ativado" para alertas do Log de Atividades. |
| context |Contexto do evento. |
| resourceProviderName |O provedor de recursos do recurso afetado. |
| conditionType |Sempre "Evento". |
| name |Nome da regra de alerta. |
| ID |ID do recurso do alerta. |
| Descrição |Descrição do alerta conforme definido durante a criação do alerta. |
| subscriptionId |ID de assinatura do Azure. |
| timestamp |Hora quando o evento foi gerado pelo serviço do Azure que processou a solicitação. |
| resourceId |ID de recurso do recurso afetado. |
| resourceGroupName |Nome do grupo de recursos do recurso afetado |
| propriedades |Conjunto de pares `<Key, Value>` (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. |
| evento |Elemento que contém metadados sobre o evento. |
| autorização |Propriedades RBAC do evento. Isso geralmente inclui a “ação”, a “função” e o “escopo”. |
| categoria |Categoria do evento. Os valores com suporte são: Administrativo, Alerta, Segurança, ServiceHealth, Recomendação. |
| chamador |Endereço de email do usuário que realizou a operação, declaração UPN ou declaração SPN com base na disponibilidade. Pode ser nulo para determinadas chamadas do sistema. |
| correlationId |Geralmente um GUID no formato de cadeia de caracteres. Eventos com correlationId pertencem à mesma ação maior e geralmente compartilham uma correlationId. |
| eventDescription |Descrição de texto estático do evento. |
| eventDataId |Identificador exclusivo do evento. |
| eventSource |Nome do serviço ou infraestrutura do Azure que gerou o evento. |
| httpRequest |Geralmente inclui a “clientRequestId”, o “clientIpAddress” e o “método” (método HTTP, como PUT, por exemplo). |
| level |Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado”. |
| operationId |Geralmente um GUID compartilhado entre os eventos correspondentes a uma única operação. |
| operationName |Nome da operação. |
| propriedades |Propriedades do evento. |
| status |Cadeia de caracteres. Status da operação. Os valores comuns incluem: "Iniciado", "Em Andamento", "Êxito", "Falha", "Ativo", "Resolvido". |
| subStatus |Geralmente inclui o código de status HTTP da chamada REST correspondente. Também pode incluir outras cadeias de caracteres que descrevam um substatus. Os valores de substatus comuns incluem: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro Interno do Servidor (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504) |

## <a name="next-steps"></a>Próximas etapas
* [Leia mais sobre o Log de Atividades](monitoring-overview-activity-logs.md)
* [Exemplos de scripts da Automação do Azure (Runbooks) em alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usar aplicativo lógico para enviar um SMS por meio de Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
* [Usar aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
* [Usar aplicativo lógico para enviar uma mensagem a uma Fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Este exemplo serve para alertas de métrica, mas pode ser modificado para funcionar com um alerta do Log de Atividades.
