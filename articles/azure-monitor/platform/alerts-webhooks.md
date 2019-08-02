---
title: Fazer um alerta de métrica clássico notificar um sistema diferente do Azure usando um webhook
description: Saiba como redirecionar alertas de métrica do Azure para outros sistemas diferentes do Azure.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345764"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Fazer um alerta de métrica clássico notificar um sistema diferente do Azure usando um webhook
É possível usar webhooks para encaminhar uma notificação de alerta do Azure para outros sistemas para pós-processamento ou notificações personalizadas. Você pode usar um webhook em um alerta para encaminhá-lo para serviços que enviam mensagens de SMS, para registrar bugs, notificar uma equipe por meio de serviços de chat ou envio de mensagens ou qualquer outra ação. 

Este artigo descreve como definir um webhook em um alerta de métrica do Azure. Ele também mostra qual é a aparência do conteúdo do HTTP POST para um webhook. Para obter informações sobre a configuração e o esquema de um alerta de log de atividades do Azure (alerta sobre eventos), consulte [Chamar um webhook em um alerta de log de atividades do Azure](alerts-log-webhook.md).

Os alertas do Azure usam HTTP POST para enviar o conteúdo do alerta no formato JSON para o URI de um webhook que você fornece ao criar o alerta. O esquema é definido posteriormente neste artigo. O URI deve ser um ponto de extremidade HTTP ou HTTPS válido. O Azure posta uma entrada por solicitação quando um alerta é ativado.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurar webhooks por meio do Portal do Azure
Para adicionar ou atualizar o URI do webhook, no [Portal do Azure](https://portal.azure.com/), vá até **Criar/Atualizar Alertas**.

![Painel Adicionar uma regra de alerta](./media/alerts-webhooks/Alertwebhook.png)

Você também pode configurar um alerta para publicar no URI de um webhook usando [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), uma [CLI entre plataformas](../../azure-monitor/platform/cli-samples.md#work-with-alerts) ou [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar usando a autorização baseada em token. O URI do webhook é salvo com uma ID de token. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de conteúdo
A operação POST contém o seguinte esquema e conteúdo JSON para todos os alertas baseados em métricas:

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
| status |S |Activated, Resolved |O status do alerta como base nas condições que você definiu. |
| context |S | |O contexto do alerta. |
| timestamp |S | |A hora em que o alerta foi disparado. |
| id |S | |Cada regra de alerta tem uma ID exclusiva. |
| name |S | |O nome do alerta. |
| description |S | |Uma descrição do alerta. |
| conditionType |S |Metric, Event |Há suporte para dois tipos de alertas: de métrica e evento. Alertas de métricas são baseados na condição de uma métrica. Alertas de eventos são baseados em um evento no log de atividades. Use esse valor para verificar se o alerta é baseado em uma métrica ou em um evento. |
| condition |S | |Os campos específicos para verificação com base no valor de **conditionType**. |
| metricName |Para alertas de métrica | |O nome da métrica define o que a regra monitora. |
| metricUnit |Para alertas de métrica |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |A unidade permitida na métrica. Consulte os [valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Para alertas de métrica | |O valor real da métrica que causou o alerta. |
| threshold |Para alertas de métrica | |O valor de limite no qual o alerta é ativado. |
| windowSize |Para alertas de métrica | |O período que é usado para monitorar a atividade de alertas com base no limite. O valor deve ser entre 5 minutos e 1 dia. O valor deve estar no formato de duração ISO 8601. |
| timeAggregation |Para alertas de métrica |Average, Last, Maximum, Minimum, None, Total |Como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Consulte os [valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Para alertas de métrica | |O operador usado para comparar os dados de métrica atuais com o limite definido. |
| subscriptionId |S | |A ID de assinatura do Azure. |
| resourceGroupName |S | |O nome do grupo de recursos do recurso afetado. |
| resourceName |S | |O nome do recurso afetado. |
| resourceType |S | |O tipo do recurso afetado. |
| resourceId |S | |A ID do recurso afetado. |
| resourceRegion |S | |A região ou local do recurso afetado. |
| portalLink |S | |Um link direto para a página de resumo de recursos do portal. |
| propriedades |N |Opcional |Um conjunto de pares chave/valor que tem detalhes sobre o evento. Por exemplo: `Dictionary<String, String>`. O campo de propriedades é opcional. Em um fluxo de trabalho personalizado baseado em aplicativo lógico ou em interface do usuário, os usuários podem inserir pares chave/valor que podem ser transmitidos por meio do conteúdo. Um modo alternativo de transmitir as propriedades personalizadas para o webhook é por meio do próprio URI do webhook (como parâmetros de consulta). |

> [!NOTE]
> Somente é possível definir o campo de **propriedades** usando [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os alertas e webhooks do Azure no vídeo [Integrar Alertas do Azure com o PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Saiba como [Executar exemplos de scripts da Automação do Azure (runbooks) em alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem de SMS por meio do Twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem do Slack de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem a uma fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

