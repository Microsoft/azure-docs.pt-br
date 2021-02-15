---
title: Criar alertas do Assistente do Azure para novas recomendações usando o modelo do Resource Manager
description: Saiba como configurar um alerta de novas recomendações do Assistente do Azure usando um modelo do ARM (Azure Resource Manager).
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: f03bf6eaf4f3045e00fc67efe6faa9f53d962089
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91629889"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Início Rápido: Criar alertas do Assistente do Azure sobre novas recomendações usando o modelo do ARM

Este artigo mostra como configurar um alerta de novas recomendações do Assistente do Azure usando um modelo do ARM (Azure Resource Manager).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Sempre que o Assistente do Azure detecta uma nova recomendação para um de seus recursos, um evento é armazenado no [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md). Você pode configurar alertas para esses eventos no Assistente do Azure usando uma experiência de criação de alertas específica para recomendação. Você pode selecionar uma assinatura e, opcionalmente, um grupo de recursos para especificar os recursos nos quais deseja receber alertas.

Você também pode determinar os tipos de recomendações usando essas propriedades:

- Categoria
- Nível de impacto
- Tipo de recomendação

Você também pode configurar a ação que ocorrerá quando um alerta for disparado:

- Selecionando um grupo de ações existente
- Como criar um grupo de ações

Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Atualmente, os alertas do Assistente estão disponíveis apenas para recomendações de Alta Disponibilidade, Desempenho e Custo. Não há suporte para recomendações de segurança.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Para executar os comandos do computador local, instale a CLI do Azure ou os módulos do Azure PowerShell. Para obter mais informações, confira [Instalar o CLI do Azure](/cli/azure/install-azure-cli) e [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Examinar o modelo

O modelo a seguir cria um grupo de ações com um destino de email e habilita todas as notificações de integridade de serviço para a assinatura de destino. Salve esse modelo como *CreateAdvisorAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
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
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

O modelo define dois recursos:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo usando qualquer método padrão para [implantar um modelo do ARM](../azure-resource-manager/templates/deploy-portal.md) como os exemplos a seguir usando a CLI e o PowerShell. Substitua os valores do exemplo de **Grupo de Recursos** e **emailAddress** pelos valores apropriados para o seu ambiente. O nome do workspace deve ser exclusivo entre todas as assinaturas do Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Validar a implantação

Verifique se o workspace foi criado usando um dos comandos a seguir. Substitua os valores do exemplo para **Grupo de Recursos** pelo valor que você usou acima.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
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

- Obtenha uma [visão geral dos alertas do log de atividades](../azure-monitor/platform/alerts-overview.md) e saiba como receber alertas.
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).
