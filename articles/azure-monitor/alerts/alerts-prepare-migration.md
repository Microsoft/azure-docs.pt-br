---
title: Atualizar aplicativos lógicos & runbooks para migração de alertas
description: Saiba como modificar seus WebHooks, aplicativos lógicos e runbooks para se preparar para a migração voluntária.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: ce61c3539a4ea29cbeb48c379ed143363500701e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038009"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparar seus aplicativos lógicos e runbooks para a migração de regras de alerta clássicos

> [!NOTE]
> Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Azure governamental e o Azure China 21Vianet serão desativados em **29 de fevereiro de 2024**.
>

Se você optar por migrar voluntariamente suas regras de alerta clássicas para novas regras de alerta, haverá algumas diferenças entre os dois sistemas. Este artigo explica essas diferenças e como você pode se preparar para a alteração.

## <a name="api-changes"></a>Alterações de API

As APIs que criam e gerenciam as regras de alerta clássicas ( `microsoft.insights/alertrules` ) são diferentes das APIs que criam e gerenciam novos alertas de métrica ( `microsoft.insights/metricalerts` ). Se você criar e gerenciar programaticamente regras de alerta clássicas hoje, atualize seus scripts de implantação para trabalhar com as novas APIs.

A tabela a seguir é uma referência às interfaces programáticas para alertas clássicos e novos:

| Tipo de script de implantação | Alertas clássicos | Novos alertas de métrica |
| ---------------------- | -------------- | ----------------- |
|API REST     | [Microsoft. insights/alertrules](/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](/rest/api/monitor/metricalerts)       |
|CLI do Azure     | [az monitor alert](/cli/azure/monitor/alert)        | [alerta AZ monitor de métricas](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [Referência](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referência](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modelo do Azure Resource Manager | [Para alertas clássicos](./alerts-enable-template.md)|[Para novos alertas de métrica](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Alterações de carga de notificação

O formato da carga de notificação é ligeiramente diferente entre [as regras de alerta clássicas](alerts-webhooks.md) e os [novos alertas de métrica](alerts-metric-near-real-time.md#payload-schema). Se você tiver regras de alerta clássicas com webhook, aplicativo lógico ou ações de runbook, deverá atualizar os destinos para aceitar o novo formato de carga.

Use a tabela a seguir para mapear os campos de carga do webhook do formato clássico para o novo formato:

| Tipo de ponto de extremidade de notificação | Alertas clássicos | Novos alertas de métrica |
| -------------------------- | -------------- | ----------------- |
|O alerta foi ativado ou resolvido?    | **status**       | **Data. status** |
|Informações contextuais sobre o alerta     | **contexto**        | **Data. Context**        |
|Carimbo de data/hora em que o alerta foi ativado ou resolvido     | **contexto. Timestamp**       | **Data. Context. Timestamp**        |
| ID da regra de alerta | **context.id** | **data.context.id** |
| Nome da regra de alerta | **context.name** | **data.context.name** |
| Descrição da regra de alerta | **contexto. Descrição** | **Data. Context. Description** |
| Condição de regra de alerta | **contexto. condição** | **Data. Context. Condition** |
| Nome da métrica | **Context. condição. metricname** | **Data. Context. Condition. allOf [0]. metricname** |
| Agregação de tempo (como a métrica é agregada na janela de avaliação)| **contexto. condição. timeaggregation** | **contexto. condição. timeaggregation** |
| Período de avaliação | **Context. condição. windowSize** | **Data. Context. Condition. windowSize** |
| Operador (como o valor da métrica agregada é comparado com o limite) | **Context. Condition. Operator** | **Data. Context. Condition. Operator** |
| Limite | **contexto. condição. limite** | **Data. Context. Condition. allOf [0]. Threshold** |
| Valor da métrica | **contexto. condição. metricvalue** | **Data. Context. Condition. allOf [0]. metricvalue** |
| ID da assinatura | **contexto. SubscriptionId** | **Data. Context. SubscriptionId** |
| Grupo de recursos do recurso afetado | **Context. resourcegroup** | **Data. Context. resourcegroup** |
| Nome do recurso afetado | **Context. resourceName** | **Data. Context. resourceName** |
| Tipo do recurso afetado | **Context. resourceType** | **Data. Context. resourceType** |
| ID de recurso do recurso afetado | **Context. ResourceId** | **Data. Context. ResourceId** |
| Link direto para a página de Resumo de recursos do portal | **Context. portalLink** | **Data. Context. portalLink** |
| Campos de carga personalizada a serem passados para o webhook ou o aplicativo lógico | **properties** | **Data. Properties** |

As cargas são semelhantes, como você pode ver. A seção a seguir oferece:

- Detalhes sobre como modificar aplicativos lógicos para trabalhar com o novo formato.
- Um exemplo de runbook que analisa a carga de notificação para novos alertas.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificar um aplicativo lógico para receber uma notificação de alerta de métrica

Se você estiver usando aplicativos lógicos com alertas clássicos, deverá modificar o código do aplicativo lógico para analisar a nova carga de alertas de métrica. Siga estas etapas:

1. Crie um novo aplicativo lógico.

1. Use o modelo "manipulador de alerta de métricas de Azure Monitor". Este modelo tem um gatilho de **solicitação HTTP** com o esquema apropriado definido.

    ![Captura de tela mostra dois botões, aplicativo lógico em branco e Azure Monitor – manipulador de alerta de métricas.](media/alerts-prepare-migration/logic-app-template.png "Modelo de alerta de métrica")

1. Adicione uma ação para hospedar sua lógica de processamento.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usar um runbook de automação que recebe uma notificação de alerta de métrica

O exemplo a seguir fornece o código do PowerShell para usar em seu runbook. Esse código pode analisar as cargas para as regras de alerta de métrica clássica e novas regras de alerta de métrica.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Para obter um exemplo completo de um runbook que para uma máquina virtual quando um alerta é disparado, consulte a [documentação da automação do Azure](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Integração de parceiros por meio de WebHooks

A maioria de [nossos parceiros que se integram com alertas clássicos](../partners.md) já dão suporte a alertas de métrica mais recentes por meio de suas integrações. As integrações conhecidas que já funcionam com novos alertas de métrica são:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se você estiver usando uma integração de parceiro que não está listada aqui, confirme com o provedor que ele trabalha com novos alertas de métrica.

## <a name="next-steps"></a>Próximas etapas

- [Como usar a ferramenta de migração](alerts-using-migration-tool.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)