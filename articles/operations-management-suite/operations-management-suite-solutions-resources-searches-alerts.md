---
title: "Salvar pesquisas e alertas em soluções OMS | Microsoft Docs"
description: "As soluções no OMS normalmente inclui pesquisas salvas na Log Analytics para analisar os dados coletados pela solução.  Elas podem também definir alertas para notificar o usuário ou executar automaticamente a ação em resposta a um problema crítico.  Este artigo descreve como definir pesquisas e alertas salvos no Log Analytics em um modelo ARM para que eles possam ser incluídos em soluções de gerenciamento."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21c42a747a08c5386c65d10190baf0054a7adef8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Adicionando alertas e pesquisas salvas do Log Analytics à solução de gerenciamento do OMS (Versão prévia)

> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.   


As [soluções de gerenciamento no OMS](operations-management-suite-solutions.md) geralmente incluirão [pesquisas salvas](../log-analytics/log-analytics-log-searches.md) no Log Analytics para analisar os dados coletados pela solução.  Elas também podem definir [alertas](../log-analytics/log-analytics-alerts.md) para notificar o usuário ou executar automaticamente a ação em resposta a um problema crítico.  Este artigo descreve como definir a Log Analytics pesquisas salvas e alertas em um [modelo do Resource Manager](../resource-manager-template-walkthrough.md) para que eles possam ser incluídos em [soluções de gerenciamento de](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para as soluções de gerenciamento e estão descritos em [Creating management solutions in Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) (Criando soluções de gerenciamento no OMS (Operations Management Suite))  

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com o modo para [criar uma solução de gerenciamento](operations-management-suite-solutions-creating.md) e com a estrutura de um [modelo ARM](../resource-group-authoring-templates.md) e de um arquivo de solução.


## <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
Todos os recursos de Log Analytics estão contidos em um [espaço](../log-analytics/log-analytics-manage-access.md).  Como descrito no [espaço de trabalho OMS e conta de automação](operations-management-suite-solutions.md#oms-workspace-and-automation-account), o espaço de trabalho não está incluído na solução de gerenciamento, mas deve existir antes que a solução seja instalada.  Se ela não estiver disponível, a instalação da solução falhará.

O nome do espaço de trabalho é no nome de cada recurso de Log Analytics.  Isso é feito na solução com o parâmetro **workspace**, conforme descrito no exemplo a seguir de um recurso savedsearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"


## <a name="saved-searches"></a>Pesquisas salvas
Incluir [pesquisas salvas](../log-analytics/log-analytics-log-searches.md) em uma solução para permitir aos usuários consultar dados coletados pela solução.  Pesquisas salvas aparecerão em **Favoritos** no portal do OMS e **pesquisas salvas** no portal do Azure.  Uma pesquisa salva também é necessária para cada alerta.   

Os recursos [da pesquisa salva do Log Analytics](../log-analytics/log-analytics-log-searches.md) têm um tipo `Microsoft.OperationalInsights/workspaces/savedSearches` e a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



Cada uma das propriedades de pesquisas salvas são descritos na tabela a seguir. 

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria para a pesquisa salva.  As pesquisas salvas na mesma solução geralmente compartilham uma única categoria para que eles são agrupados juntos no console. |
| displayname | Nome para exibição para a pesquisa salva no portal. |
| query | Consulta a executar. |

> [!NOTE]
> Você talvez precise usar caracteres de escape na consulta, se ele inclui os caracteres que podem ser interpretados como JSON.  Por exemplo, se sua consulta **OperationName:"Microsoft.Compute/virtualMachines/write tipo: AzureActivity"**, deve ser gravado no arquivo de solução como **OperationName tipo: AzureActivity:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertas
[Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md) são criados por regras de alerta que executar uma pesquisa salva em um intervalo regular.  Se os resultados da consulta correspondência aos critérios especificados, será criado um registro de alerta e uma ou mais ações são executadas.  

Regras de alerta em uma solução de gerenciamento são constituídas por três recursos diferentes.

- **Pesquisa salva.**  Define a pesquisa de log que será executada.  Várias regras de alerta podem compartilhar uma única pesquisa salva.
- **Agenda.**  Define a frequência com a pesquisa de log será executada.  Cada regra de alerta terá apenas um agendamento.
- **Ação de alerta.**  Cada regra de alerta terá um recurso de ação com um tipo de **Alerta** que define os detalhes do alerta, como os critérios para quando um registro de alerta será criado e a gravidade do alerta.  O recurso de ação, opcionalmente, definir uma resposta de email e o runbook.
- **Ação de Webhook (opcional).**  Se a regra de alerta chamará um webhook, ele requer um recurso de ação adicional com um tipo de **Webhook**.    

Salvar pesquisa recursos descritos acima.  Outros recursos são descritos abaixo.


### <a name="schedule-resource"></a>Recursos de agendamento

Uma pesquisa salva pode ter uma ou mais agendas com cada agenda que representa uma regra de alerta separada. A agenda define a frequência com que a pesquisa é executada e o intervalo de tempo em que os dados são recuperados.  Os recursos de agendamento têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` e ter a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



As propriedades de recursos de agendamento são descritas na tabela a seguir.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| Habilitado       | Sim | Especifica se o alerta está habilitado quando ele é criado. |
| intervalo      | Sim | A frequência com a consulta é executada em minutos. |
| queryTimeSpan | Sim | Período de tempo em minutos no qual avaliar resultados. |

O recurso de agendamento deve depender a pesquisa salva para que ele seja criado antes da agenda.


### <a name="actions"></a>Ações
Há dois tipos de recurso de ação especificado pelo **tipo** propriedade.  Uma agenda requer um **alerta** ação que define os detalhes da regra de alerta e quais ações são executadas quando um alerta é criado.  Ele também pode incluir uma **Webhook** ação se um webhook deve ser chamado a partir do alerta.  

Recursos de ação com um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Ações de alerta

Cada agenda terá um **alerta** ação.  Isso define os detalhes do alerta e, opcionalmente, ações de notificação e correção.  Uma notificação envia um email para um ou mais endereços.  Uma correção inicia um runbook na automação do Azure para tentar corrigir o problema detectado.

Ações de alerta tem a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar este trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 



    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                },
            },
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

As propriedades de Recursos de ação de alerta são descritas nas tabelas a seguir.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| Tipo | Sim | Tipo da ação.  Isso será **alerta** para ações de alerta. |
| Nome | Sim | Nome de exibição para o alerta.  Esse é o nome que é exibido no console para a regra de alerta. |
| Descrição | Não | Descrição opcional do alerta. |
| Severidade | Sim | Severidade do alerta registro dos seguintes valores:<br><br> **Crítico**<br>**Aviso**<br>**Informativo** |


##### <a name="threshold"></a>Limite
Esta seção é necessária.  Define as propriedades para o limite de alerta.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| Operador | Sim | O operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| Valor | Sim | O valor para comparar os resultados. |


##### <a name="metricstrigger"></a>MetricsTrigger
Esta seção é opcional.  Inclua-o para um alerta de métrica de medição.

> [!NOTE]
> Alertas de métrica de medição estão atualmente em visualização pública. 

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| TriggerCondition | Sim | Especifica se o limite do número total de violações ou falhas consecutivas dos seguintes valores:<br><br>**Total<br>consecutivas** |
| Operador | Sim | O operador para a comparação dos seguintes valores:<br><br>**gt = maior que<br>lt = menor que** |
| Valor | Sim | Número de vezes que os critérios devem ser atendidos para disparar o alerta. |

##### <a name="throttling"></a>Limitação
Esta seção é opcional.  Inclua esta seção se você desejar Suprimir alertas da mesma regra por algum tempo depois que um alerta é criado.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| DurationInMinutes | Sim, se a limitação elemento incluído | Número de minutos para suprimir alertas depois da mesma regra de alerta será criado. |

##### <a name="emailnotification"></a>EmailNotification
 Esta seção é opcional incluí-lo se desejar que o alerta para enviar mensagens a um ou mais destinatários.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| Destinatários | Sim | Lista delimitada por vírgulas de endereços de email para enviar notificações quando um alerta é criado, como no exemplo a seguir.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Subject | Sim | Linha de assunto do email. |
| Anexo | Não | Anexos não são atualmente suportados.  Se este elemento for incluído, ele deve ser **nenhum**. |


##### <a name="remediation"></a>Correção
Esta seção é opcional incluí-lo se você quiser que um runbook para iniciar em resposta ao alerta. |

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| RunbookName | Sim | Nome do runbook para iniciar. |
| WebhookUri | Sim | URI do webhook para o runbook. |
| Expiry | Não | Data e hora em que a correção expira. |

#### <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook iniciam um processo chamando uma URL e, opcionalmente, fornecendo uma carga a ser enviada. Elas são semelhantes às ações de Correção, exceto que se destinam a webhooks que podem invocar outros processos além de runbooks da Automação do Azure. Eles também oferecem a opção adicional de fornecer uma carga a ser enviada para o processo remoto.

Se o alerta for chamar um webhook, ele precisará de um recurso de ação com um tipo de **Webhook**, além do recurso de ação **Alerta**.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

As propriedades de recursos de ação do Webhook são descritas nas tabelas a seguir.

| Nome do elemento | Obrigatório | Descrição |
|:--|:--|:--|
| type | Sim | Tipo da ação.  Isso será **Webhook** para ações de webhook. |
| name | Sim | Nome de exibição para a ação.  Isso não é exibido no console. |
| wehookUri | Sim | URI para o webhook. |
| customPayload | Não | Carga personalizada a ser enviada para o webhook. O formato dependerá do que o webhook está esperando. |




## <a name="sample"></a>Amostra

A seguir está um exemplo de uma solução que inclua que inclui os seguintes recursos:

- Pesquisa salva
- Agenda
- Ação de alerta
- Ações de webhook

O exemplo usa [parâmetros de solução padrão](operations-management-suite-solutions-solution-file.md#parameters) variáveis que normalmente seriam usados em uma solução em vez de embutir valores nas definições de recurso.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
          }
        },
        "resources": [
          {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
              ]
            },
            "plan": {
              "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
              "Version": "[variables('SolutionVersion')]",
              "product": "[variables('ProductName')]",
              "publisher": "[variables('SolutionPublisher')]",
              "promotionCode": ""
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


O arquivo de parâmetro a seguir fornece valores de amostras para esta solução.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>Próximas etapas
* [Adicionar exibições](operations-management-suite-solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicionar runbooks de automação e outros recursos](operations-management-suite-solutions-resources-automation.md) à sua solução de gerenciamento.

