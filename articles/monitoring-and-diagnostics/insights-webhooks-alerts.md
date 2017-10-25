---
title: "Configurar webhooks em alertas de métrica do Azure | Microsoft Docs"
description: "Redirecionar alertas do Azure para outros sistemas que não são do Azure."
author: johnkemnetz
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: johnkem
ms.openlocfilehash: 1a885166e5c71f13da222bfc22b0fc579096c52f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurar um webhook em um alerta do métrica do Azure
Os webhooks permitem rotear uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Você pode usar um webhook em um alerta para roteá-lo aos serviços que enviam SMS, registrar bugs, notificar uma equipe por meio de serviços de bate-papo/mensagens ou qualquer outra ação. Este artigo descreve como definir um webhook em um alerta de métrica do Azure, e a aparência de carga para o HTTP POST para um webhook. Para obter informações sobre a configuração e o esquema de um alerta do Log de Atividades do Azure (alertas sobre eventos), [consulte esta página](insights-auditlog-to-webhook-email.md).

Os alertas HTTP POST do Azure tem conteúdo no formato JSON, esquema definido abaixo, para o URI de um webhook que você fornecer ao criar o alerta. O URI deve ser um ponto de extremidade HTTP ou HTTPS válido. O Azure posta uma entrada por solicitação quando um alerta é ativado.

## <a name="configuring-webhooks-via-the-portal"></a>Configurando webhooks através do portal
Você pode adicionar ou atualizar o URI do webhook na tela Criar/Atualizar Alertas no [portal](https://portal.azure.com/).

![Adicionar uma Regra de alerta](./media/insights-webhooks-alerts/Alertwebhook.png)

Você também pode configurar um alerta para publicar no URI de um webhook usando os [Cmdlets do Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), [CLI entre plataformas](insights-cli-samples.md#work-with-alerts) ou [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticação do webhook
O webhook pode autenticar usando a autorização baseada em token. O URI do webhook é salvo com uma ID de token, por exemplo `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de conteúdo
A operação POST contém o seguinte esquema e conteúdo JSON para todos os alertas baseados em métricas.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Obrigatório | Conjunto fixo de valores | Observações |
|:--- |:--- |:--- |:--- |
| status |S |“Activated”, “Resolved” |Status do alerta como base nas condições que você definiu. |
| context |S | |O contexto do alerta. |
| timestamp |S | |A hora em que o alerta foi disparado. |
| ID |S | |Cada regra de alerta tem uma ID exclusiva. |
| name |S | |O nome do alerta. |
| description |S | |Descrição do alerta. |
| conditionType |S |“Metric”, “Event” |Há suporte para dois tipos de alertas. Um com base em uma condição de métrica, e o outro com base em um evento no Log de Atividades. Use esse valor para verificar se o alerta tem base na métrica ou no evento. |
| condition |S | |Os campos específicos para verificação com base no conditionType. |
| metricName |para alertas de Métrica | |O nome da métrica define o que a regra monitora. |
| metricUnit |para alertas de Métrica |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds" |A unidade permitida na métrica. [Os valores permitidos estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |para alertas de Métrica | |O valor real da métrica que causou o alerta. |
| threshold |para alertas de Métrica | |O valor de limite no qual o alerta é ativado. |
| windowSize |para alertas de Métrica | |O período de tempo que é usado para monitorar a atividade de alerta com base no limite. Deve estar entre 5 minutos e 1 dia. Formato de duração ISO 8601. |
| timeAggregation |para alertas de Métrica |"Average", "Last", "Maximum", "Minimum", "None", "Total" |Como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. [Os valores permitidos estão listados aqui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |para alertas de Métrica | |O operador usado para comparar os dados de métrica atuais para o limite definido. |
| subscriptionId |S | |Id de assinatura do Azure. |
| resourceGroupName |S | |Nome do grupo de recursos do recurso afetado. |
| resourceName |S | |Nome de recurso do recurso afetado. |
| resourceType |S | |Tipo de recurso do recurso afetado. |
| resourceId |S | |ID de recurso do recurso afetado. |
| resourceRegion |S | |Região ou local do recurso afetado. |
| portalLink |S | |Link direto para a página de resumo de recursos do portal. |
| propriedades |N |Opcional |Conjunto de pares `<Key, Value>` (ou seja, `Dictionary<String, String>`) que inclui detalhes sobre o evento. O campo de propriedades é opcional. Em um fluxo de trabalho personalizado baseado em aplicativo lógico ou UI, os usuários podem inserir chaves/valores que podem ser transmitidos por meio do conteúdo. O modo alternativo para transmitir as propriedades personalizadas para o webhook é por meio do próprio URI do webhook (como parâmetros de consulta) |

> [!NOTE]
> O campo de propriedades só pode ser definido usando a [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os alertas e webhooks do Azure no vídeo [Integrar Alertas do Azure com o PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Exemplos de scripts da Automação do Azure (Runbooks) em alertas do Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usar aplicativo lógico para enviar um SMS por meio de Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Usar aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Usar aplicativo lógico para enviar uma mensagem a uma Fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
