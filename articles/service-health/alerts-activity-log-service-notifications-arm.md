---
title: Receber alertas do log de atividades nas notificações de serviço do Azure usando um modelo do Resource Manager
description: Seja notificado por SMS, email ou webhook quando um serviço do Azure for executado.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 730c023de61275d95fe594642149770af42b34b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535752"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Início Rápido: Criar alertas do log de atividades nas notificações de serviço usando um modelo do ARM

Este artigo mostra como configurar alertas do log de atividades para notificações de integridade do serviço usando um modelo do ARM (modelo do Azure Resource Manager).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

As notificações de integridade do serviço são armazenadas no [log de atividades do Azure](../azure-monitor/essentials/platform-logs-overview.md). Considerando o volume possivelmente grande de informações armazenadas no log de atividades, há uma interface do usuário separada para facilitar a exibição e a configuração de alertas nas notificações de integridade do serviço.

Você pode receber um alerta quando o Azure envia notificações de integridade do serviço para sua assinatura do Azure. Você pode configurar o alerta de acordo com:

- A classe de notificação do serviço de integridade (Problemas de serviço, Manutenção planejada, Avisos de integridade).
- A assinatura afetada.
- Os serviços afetados.
- As regiões afetadas.

> [!NOTE]
> As notificações de integridade do serviço não enviam um alerta sobre o recurso de eventos de integridade.

Também é possível configurar para quem o alerta deve ser enviado:

- Selecione um grupo de ações existente.
- Crie um novo grupo de ações (que pode ser usado posteriormente para futuros alertas).

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos do computador local, instale a CLI do Azure ou os módulos do Azure PowerShell. Para obter mais informações, confira [Instalar o CLI do Azure](/cli/azure/install-azure-cli) e [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Examinar o modelo

O modelo a seguir cria um grupo de ações com um destino de email e habilita todas as notificações de integridade de serviço para a assinatura de destino. Salve esse modelo como *CreateServiceHealthAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      }
    }
  ]
}
```

O modelo define dois recursos:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo usando qualquer método padrão para [implantar um modelo do ARM](../azure-resource-manager/templates/deploy-portal.md) como os exemplos a seguir usando a CLI e o PowerShell. Substitua os valores de exemplo de **Grupo de Recursos** e **emailAddress** pelos valores apropriados para o seu ambiente.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Validar a implantação

Verifique se o workspace foi criado usando um dos comandos a seguir. Substitua os valores do exemplo para **Grupo de Recursos** pelo valor que você usou acima.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se planejar continuar trabalhando com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão. Quando não for mais necessário, exclua o grupo de recursos, o que excluirá a regra de alerta e os recursos relacionados. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [melhores práticas para configurar os alertas da Integridade do Serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Saiba como [configurar notificações por push móvel para a Integridade do Serviço do Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Saiba mais sobre as [notificações de integridade do serviço](service-notifications.md).
- Saiba mais sobre [limitação de taxa de notificação](../azure-monitor/alerts/alerts-rate-limiting.md).
- Examine o [esquema do webhook de alertas de log de atividades](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/alerts/alerts-overview.md) e saiba como receber alertas.
- Saiba mais sobre [grupos de ação](../azure-monitor/alerts/action-groups.md).
