---
title: Modelo para criar alertas de Resource Health
description: Crie alertas por meio de programação que notificam você quando seus recursos do Azure se tornam não disponíveis.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 4f1cbe1e2d2c185906feb4ccba380cb31df864f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588212"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Configurar alertas de integridade de recursos do Azure usando modelos do Resource Manager

Este artigo mostrará como criar, programaticamente, Alertas de Log de Atividades do Resource Health usando modelos do Azure Resource Manager e o Azure PowerShell.

O Azure Resource Health mantém você informado sobre o status de integridade atual e histórico de seus recursos do Azure. Os alertas do Azure Resource Health podem notificar você quase em tempo real quando esses recursos tiverem uma alteração no seu status de integridade. Criar alertas do Resource Health por meio de programação permite que usuários criem e personalizem alertas em massa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as instruções nesta página, você precisará configurar algumas coisas antecipadamente:

1. Você precisa instalar o [módulo Azure PowerShell](/powershell/azure/install-az-ps)
2. Você precisará [criar ou reutilizar um Grupo de Ações](../azure-monitor/alerts/action-groups.md) configurado para notificar você

## <a name="instructions"></a>Instruções
1. Usando o PowerShell, faça logon no Azure usando sua conta e selecione a assinatura com a qual você deseja interagir

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > Você pode usar `Get-AzSubscription` para listar as assinaturas as quais você tem acesso.

2. Localize e salve a ID completa do Azure Resource Manager para seu Grupo de Ações

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Crie e salve um modelo do Resource Manager para alertas do Resource Health conforme `resourcehealthalert.json` ([veja os detalhes abaixo](#resource-manager-template-options-for-resource-health-alerts))

4. Crie uma nova implantação do Azure Resource Manager usando este modelo

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. Você será solicitado a digitar o nome do alerta e ID de recurso do Grupo de Ações que você copiou anteriormente:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Se tudo funcionar com êxito, você receberá uma confirmação no PowerShell

    ```output
    DeploymentName          : ExampleDeployment
    ResourceGroupName       : <resourceGroup>
    ProvisioningState       : Succeeded
    Timestamp               : 11/8/2017 2:32:00 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                            Name                     Type       Value
                            ===============          =========  ==========
                            activityLogAlertName     String     <Alert Name>
                            activityLogAlertEnabled  Bool       True
                            actionGroupResourceId    String     /...

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Observe que, se estiver planejando automatizar totalmente esse processo, você simplesmente precisará editar o modelo do Resource Manager para não solicitar os valores na etapa 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Opções de modelo do Resource Manager para alertas de Resource Health

Você pode usar esse modelo de base como ponto de partida para a criação de alertas do Resource Health. Esse modelo funcionará como escrito e se você se inscreverá para receber alertas para todos os eventos de integridade de recursos recentemente ativados em todos os recursos em uma assinatura.

> Na parte inferior deste artigo também incluímos um modelo de alerta mais complexo que deve aumentar o sinal para a taxa de ruído para alertas do Resource Health em comparação com este modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

No entanto, um alerta amplo como este geralmente não é recomendável. Saiba como é possível definir o escopo para baixo desse alerta para se concentrar nos eventos que nos interessa abaixo.

### <a name="adjusting-the-alert-scope"></a>Ajustando o escopo de alerta

Os alertas do Resource Health podem ser configurados para monitorar eventos em três escopos diferentes:

 * Nível de Assinatura
 * Nível do Grupo de Recursos
 * Nível de Recurso

O modelo de alerta é configurado no nível da assinatura, mas se você quiser configurar o alerta para notificá-lo apenas sobre determinados recursos ou recursos dentro de um determinado grupo de recursos, você simplesmente precisará modificar a seção `scopes` no modelo acima.

Para um escopo de nível de grupo de recursos, a seção de escopos deve se parecer com:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

E, para um escopo de nível de recursos, a seção de escopos deve se parecer com:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Por exemplo: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Você pode acessar o portal do Azure e examinar a URL ao exibir o recurso do Azure para obter essa cadeia de caracteres.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Ajustar os tipos de recursos que alertam você

Os alertas no nível de grupo de recursos ou assinatura podem ter diferentes tipos de recursos. Se quiser limitar os alertas para serem provenientes apenas de um certo subconjunto de tipos de recursos, você poderá definir isso na seção `condition` do modelo da seguinte forma:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Aqui, usamos o wrapper `anyOf` para permitir que o alerta de integridade de recurso corresponda a qualquer uma das condições que especificamos, permitindo os alertas que se destinam a tipos específicos de recursos.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Ajustar os eventos do Resource Health que alertam você
Quando recursos passam por um evento de integridade, eles podem passar por uma série de estágios que representa o estado do evento de integridade: `Active`, `In Progress`, `Updated` e `Resolved`.

Você pode deseja ser notificado apenas quando um recurso se tornar não íntegro, caso em que você deseja configurar o alerta para notificar somente quando `status` for `Active`. No entanto se quiser também ser notificado sobre os outros estágios, você poderá adicionar esses detalhes da seguinte forma:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Se quiser ser notificado para todos os quatro estágios de eventos de integridade, você poderá remover essa condição conjuntamente e o alerta notificará você independentemente da propriedade `status`.

> [!NOTE]
> Cada seção "anyOf" deve conter apenas um valor de tipo de campo.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Ajustar os alertas do Resource Health para evitar eventos "desconhecidos"

O Azure Resource Health pode relatar a você a integridade mais recente de seus recursos monitorando-os constantemente usando executores de teste. Os status de integridade reportados relevantes são: "Disponível", "Não disponível" e "Degradado". No entanto, em situações em que o executor e o recurso do Azure não conseguem se comunicar, um status de integridade "Desconhecido" é relatado para o recurso e é considerado um evento de integridade "Ativo".

No entanto, quando um recurso relata "Desconhecido", é provável que o status de integridade não tenha sido alterado desde o último relatório preciso. Se quiser eliminar alertas em eventos "Desconhecidos", você poderá especificar essa lógica no modelo:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

Neste exemplo, estamos apenas notificando os eventos em que o status de integridade atual e anterior não tem "Desconhecido". Essa alteração poderá ser uma adição útil se os alertas forem enviados diretamente ao seu celular ou email. 

Observe que é possível que as propriedades currentHealthStatus e previousHealthStatus sejam nulas em alguns eventos. Por exemplo, quando um evento atualizado ocorre, é provável que o status de integridade do recurso não tenha sido alterado desde o último relatório, apenas que informações adicionais do evento estejam disponíveis (por exemplo, causa). Portanto, usar a cláusula acima pode resultar em alguns alertas não sendo disparados, pois os valores de Properties. currentHealthStatus e Properties. previousHealthStatus serão definidos como NULL.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Ajustar o alerta para evitar eventos iniciados pelo usuário

Resource Health eventos podem ser disparados por eventos iniciados pela plataforma e pelo usuário. Pode fazer sentido enviar apenas uma notificação quando o evento de integridade for causado pela plataforma do Azure.

É fácil configurar o alerta para filtrar apenas esses tipos de eventos:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Observe que é possível que o campo de causa seja nulo em alguns eventos. Ou seja, ocorre uma transição de integridade (por exemplo, disponível para indisponível) e o evento é registrado imediatamente para evitar atrasos de notificação. Portanto, o uso da cláusula acima pode resultar em um alerta não ser disparado, pois o valor da Propriedade Properties. cláusula será definido como NULL.

## <a name="complete-resource-health-alert-template"></a>Concluir Resource Health modelo de alerta

Usando os diferentes ajustes descritos na seção anterior, aqui está um modelo de exemplo que é configurado para maximizar o sinal para a taxa de ruído. Tenha em mente as advertências indicadas acima em que os valores de propriedade currentHealthStatus, previousHealthStatus e causa podem ser nulos em alguns eventos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

No entanto, você saberá melhor quais configurações são eficazes para você, então use as ferramentas mostradas a você nesta documentação para fazer sua própria personalização.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Resource Health:
-  [Visão geral de Azure Resource Health](Resource-health-overview.md)
-  [Tipos de recursos e verificações de integridade disponíveis por meio de Azure Resource Health](resource-health-checks-resource-types.md)


Criar alertas de Integridade do Serviço:
-  [Configurar alertas para a Integridade do Serviço](./alerts-activity-log-service-notifications-portal.md) 
-  [Esquema de eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log-schema.md)
