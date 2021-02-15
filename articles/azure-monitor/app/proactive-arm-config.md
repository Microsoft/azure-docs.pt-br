---
title: Configurações de regra de detecção inteligente-insights Aplicativo Azure
description: Automatizar o gerenciamento e a configuração das regras de detecção inteligente do Application Insights do Azure com Modelos do Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 169ad40e32f688ae20a9d02f61db161844b1254a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890506"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerenciar regras de detecção inteligente do Application Insights usando modelos do Azure Resource Manager

As regras de detecção inteligente no Application Insights podem ser gerenciadas e configuradas usando [modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Esse método pode ser usado na implantação de novos recursos do Application Insights com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração da regra de detecção inteligente

É possível definir as seguintes configurações para uma regra de detecção inteligente:
- Se a regra estiver habilitada (o padrão é **true** ).
- Se os emails devem ser enviados aos usuários associados ao [leitor de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-reader) da assinatura e às funções de [colaborador de monitoramento](../../role-based-access-control/built-in-roles.md#monitoring-contributor) quando uma detecção é encontrada (o padrão é **true** ).
- Quaisquer destinatários de email adicionais que devem receber uma notificação quando uma detecção for encontrada.
    -  A configuração de email não está disponível para as regras de detecção inteligente marcadas como _Visualização_ .

Para permitir a definição das configurações da regra por meio do Azure Resource Manager, a configuração da regra de detecção inteligente agora está disponível como um recurso interno dentro do recurso do Application Insights nomeado **ProactiveDetectionConfigs** .
Para máxima flexibilidade, cada regra de detecção inteligente pode ser definida com configurações de notificação exclusivas.

## <a name="examples"></a>Exemplos

Abaixo estão alguns exemplos que mostram como definir as configurações das regras de detecção inteligente usando modelos do Azure Resource Manager.
Todos os exemplos se referem a um recurso do Application Insights nomeado _"myApplication"_ e à "regra de detecção inteligente de duração da dependência longa" que é nomeada internamente _"longdependencyduration"_ .
Certifique-se de substituir o nome de recurso do Application Insights e especificar o nome interno da regra de detecção inteligente relevante. Verifique a tabela abaixo para obter uma lista dos nomes internos correspondentes do Resource Manager do Azure para cada regra de detecção inteligente.

### <a name="disable-a-smart-detection-rule"></a>Desabilitar uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Desabilitar o envio de notificações de email para uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adicionar destinatários de email adicionais para uma regra de detecção inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Nomes das regras de detecção inteligente

Abaixo está uma tabela de nomes de regra de detecção inteligente assim como aparecem no portal, juntamente com seus nomes internos, os quais devem ser usados no modelo do Azure Resource Manager.

> [!NOTE]
> As regras de detecção inteligente marcadas como _Visualização_ não dão suporte a notificações por email. Portanto, você só pode definir a propriedade _Enabled_ para essas regras. 

| Nome da regra do portal do Azure | Nome interno
|:---|:---|
| Tempo de carregamento de página lento | slowpageloadtime |
| Tempo de resposta do servidor lento | slowserverresponsetime |
| Duração da dependência longa | longdependencyduration |
| Degradação no tempo de resposta do servidor | degradationinserverresponsetime |
| Degradação na duração da dependência | Degradação na duração da dependência |
| Degradação na taxa de gravidade de rastreamento (visualização) | extension_traceseveritydetector |
| Aumento anormal no volume de exceção (visualização) | extension_exceptionchangeextension |
| Potencial perda de memória detectada (visualização) | extension_memoryleakextension |
| Potencial problema de segurança detectado (visualização) | extension_securityextensionspackage |
| Aumento anormal no volume de dados diário (versão prévia) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Regra de alerta de anomalias de falha

Este modelo de Azure Resource Manager demonstra a configuração de uma regra de alerta de anomalias com uma severidade de 2. Essa nova versão da regra de alerta de anomalias de falha faz parte da nova plataforma de alerta do Azure e substitui a versão clássica que está sendo desativada como parte do [processo de aposentadoria de alertas clássicos](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

> [!NOTE]
> As anomalias de falha são um serviço global, portanto, o local da regra é criado no local global.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Esse Azure Resource Manager modelo é exclusivo para a regra de alerta de anomalias de falha e é diferente das outras regras de detecção inteligente clássicas descritas neste artigo. Se você quiser gerenciar anomalias de falha manualmente, isso é feito em alertas de Azure Monitor, enquanto todas as outras regras de detecção inteligente são gerenciadas no painel detecção inteligente da interface do usuário.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar automaticamente:

- [Anomalias de falha](./proactive-failure-diagnostics.md)
- [Vazamentos de memória](./proactive-potential-memory-leak.md)
- [Anomalias de desempenho](./proactive-performance-diagnostics.md)

