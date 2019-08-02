---
title: Definir as configurações da regra de detecção inteligente do Application Insights do Azure com os modelos do Azure Resource Manager | Microsoft Docs
description: Automatizar o gerenciamento e a configuração das regras de detecção inteligente do Application Insights do Azure com Modelos do Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477856"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gerenciar regras de detecção inteligente do Application Insights usando modelos do Azure Resource Manager

As regras de detecção inteligente no Application Insights podem ser gerenciadas e configuradas usando [modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Esse método pode ser usado na implantação de novos recursos do Application Insights com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuração da regra de detecção inteligente

É possível definir as seguintes configurações para uma regra de detecção inteligente:
- Se a regra está habilitada (o padrão é **true**.)
- Se os emails devem ser enviados para os usuários associados à assinatura do [leitor de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [Colaborador de monitoramento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) funções quando uma detecção for encontrada (o padrão é **true**.)
- Quaisquer destinatários de email adicionais que devem receber uma notificação quando uma detecção for encontrada.
    -  Configuração de email não está disponível para regras de detecção inteligente marcadas como _visualização_.

Para permitir a definição das configurações da regra por meio do Azure Resource Manager, a configuração da regra de detecção inteligente agora está disponível como um recurso interno dentro do recurso do Application Insights nomeado **ProactiveDetectionConfigs**.
Para máxima flexibilidade, cada regra de detecção inteligente pode ser definida com configurações de notificação exclusivas.

## 

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
        "ApplicationId": "myApplication"
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
        "ApplicationId": "myApplication"
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
        "ApplicationId": "myApplication"
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
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Regra de alerta de falha anomalias v2 (não clássica)

Este modelo do Azure Resource Manager demonstra como configurar uma regra de alerta de v2 de anomalias de falha com uma severidade de 2. Essa nova versão da regra de alerta de anomalias de falha faz parte do Azure nova plataforma de alerta e substitui a versão clássica que está sendo desativada como parte do [processo de desativação de alertas clássicos](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
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
> Este modelo do Azure Resource Manager é exclusivo para a regra de alerta de v2 de anomalias de falha e é diferente do que as outras regras de detecção inteligente clássicas descritos neste artigo.   

## <a name="smart-detection-rule-names"></a>Nomes das regras de detecção inteligente

Abaixo está uma tabela de nomes de regra de detecção inteligente assim como aparecem no portal, juntamente com seus nomes internos, os quais devem ser usados no modelo do Azure Resource Manager.

> [!NOTE]
> Regras de detecção inteligente marcadas como _visualização_ não dão suporte a notificações por email. Portanto, você só pode definir a _habilitado_ propriedade para essas regras. 

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
| Aumento anormal no volume de dados diária (visualização) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar automaticamente:

- [Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdas de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)
