---
title: Migrar alertas sobre eventos de gerenciamento para Alertas do Log de Atividades | Microsoft Docs
description: "Os alertas em eventos de gerenciamento serão removidos em 1º de outubro. Prepare-se migrando os alertas de migração existentes."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrar alertas do Azure em eventos de gerenciamento para alertas do Log de Atividades


> [!WARNING]
> Os alertas em eventos de gerenciamento serão desativados a partir do dia 1º de outubro. Use as instruções abaixo para entender se você tem esses alertas e migrá-los em caso positivo.
>
> 

## <a name="what-is-changing"></a>O que está mudando

O Azure Monitor (antigo Azure Insights) oferecia a capacidade de criar um alerta que disparava eventos de gerenciamento e gerava notificações para a URL de um webhook ou endereços de email. Você pode ter criado um desses alertas usando uma das seguintes maneiras:
* No Portal do Azure de certos tipos de recursos, em Monitoramento -> Alertas -> Adicionar Alerta, em que "Alertar sobre" está definido como "Eventos"
* Executando o cmdlet do PowerShell, Add-AzureRmLogAlertRule
* Usando diretamente a [API REST do alerta](http://docs.microsoft.com/rest/api/monitor/alertrules) com odata.type = “ManagementEventRuleCondition” e dataSource.odata.type = “RuleManagementEventDataSource”
 
O script do PowerShell a seguir retorna uma lista de todos os alertas sobre eventos de gerenciamento em sua assinatura, bem como as condições definidas em cada alerta.

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Se você não tiver alertas em eventos de gerenciamento, o cmdlet do PowerShell acima produzirá uma série de mensagens de aviso como esta:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Essas mensagens de aviso podem ser ignoradas. Se você tiver alertas sobre eventos de gerenciamento, a saída desse cmdlet do PowerShell será esta:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Cada alerta é separado por uma linha tracejada, e os detalhes incluem a ID de recurso do alerta e a regra específica que está sendo monitorada.

Essa funcionalidade foi transferida para [Alertas do Log de Atividades do Azure Monitor](monitoring-activity-log-alerts.md). Esses novos alertas permitem que você defina uma condição em eventos do Log de Atividades e receba uma notificação quando um novo evento corresponder à condição. Eles também oferecem vários aprimoramentos de alertas sobre eventos de gerenciamento:
* Você pode reutilizar seu grupo de destinatários de notificação ("ações") em vários alertas usando [Grupos de Ação](monitoring-action-groups.md), reduzindo a complexidade que é alterar quem deve receber um alerta.
* Você poderá receber uma notificação diretamente em seu telefone usando SMS com Grupos de Ação.
* Você pode [criar alertas do Log de Atividades com modelos do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Você pode criar condições com maior flexibilidade e complexidade a fim de atender às suas necessidades específicas.
* As notificações são entregues mais rapidamente.
 
## <a name="how-to-migrate"></a>Como migrar
 
Para criar um novo Alerta do Log de Atividades, você pode:
* Seguir [nosso guia sobre como criar um alerta no Portal do Azure](monitoring-activity-log-alerts.md)
* Saiba como [Criar um alerta usando um modelo do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Os alertas em eventos de gerenciamento que você criou anteriormente não serão migrados automaticamente para os Alertas do Log de Atividades. Você precisa usar o script do PowerShell anterior para listar os alertas sobre eventos de gerenciamento configurados atualmente, e recriá-los manualmente como Alertas do Log de Atividades. Isso deve ser feito antes de 1º de outubro, pois após essa data os alertas em eventos de gerenciamento não serão mais visíveis em sua assinatura do Azure. Outros tipos de alertas do Azure, incluindo alertas de métrica do Azure Monitor, alertas do Application Insights e alertas do Log Analytics não são afetados por essa alteração. Se você tiver dúvidas, poste nos comentários abaixo.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Log de Atividades](monitoring-overview-activity-logs.md)
* Configurar [alertas do Log de Atividades por meio do Portal do Azure](monitoring-activity-log-alerts.md)
* Configurar [alertas do Log de Atividades por meio do Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Examine o [esquema do webhook de alertas do Log de Atividade](monitoring-activity-log-alerts-webhook.md)
* Saiba mais sobre as [Notificações de Serviço](monitoring-service-notifications.md)
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)
