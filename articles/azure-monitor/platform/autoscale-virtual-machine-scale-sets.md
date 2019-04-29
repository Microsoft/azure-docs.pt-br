---
title: Autoescala avançada usando máquinas virtuais do Azure
description: Usa o Resource Manager e Conjuntos de Dimensionamento de VMs com várias regras e perfis que enviam email e chamam URLs de webhook com ações de escala.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787746"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Configuração avançada de autoescala usando modelos do Resource Manager para Conjuntos de Dimensionamento de VMs
Você pode escalar e reduzir horizontalmente Conjuntos de Dimensionamento de Máquina Virtual com base nos limites de métrica de desempenho, em uma agenda recorrente ou em determinada data. Você também pode configurar notificações por email e webhook para ações de escala. Este passo a passo mostra um exemplo de configuração de todos esses objetos usando um modelo do Resource Manager em um Conjunto de Dimensionamento de VMs.

> [!NOTE]
> Este passo a passo explica as etapas para conjuntos de dimensionamento de VM, as mesmas informações se aplicam ao dimensionamento automático de [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/), [Serviço de Aplicativo - Aplicativos Web](https://azure.microsoft.com/services/app-service/web/) e [serviços de Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Para uma configuração simples de expandir/reduzir em Conjuntos de Dimensionamento de VMs com base em uma métrica de desempenho simples, como CPU, consulte os documentos sobre o [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) e o [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Passo a passo
Neste passo a passo, usamos [Azure Resource Manager](https://resources.azure.com/) para configurar e atualizar a configuração de dimensionamento automático para um conjunto de dimensionamento. O Gerenciador de Recursos do Azure é uma maneira fácil de gerenciar recursos do Azure por meio de modelos do Resource Manager. Se você não conhece bem a ferramenta do Azure Resource Manager, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implante um novo conjunto de dimensionamento com configuração básica de dimensionamento automático. Este artigo usa um da Galeria de Início Rápido do Azure que tem um conjunto de dimensionamento do Windows com o modelo básico de dimensionamento automático. Os conjuntos de dimensionamento do Linux funcionam da mesma maneira.
2. Depois que o conjunto de dimensionamento é criado, navegue até o recurso do conjunto de dimensionamento do Gerenciador de Recursos do Azure. Você verá o item a seguir no nó do Microsoft.Insights.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A execução do modelo criou uma configuração padrão de dimensionamento automático com o nome **'autoscalewad'**. No lado direito, você pode exibir a definição completa dessa configuração de dimensionamento automático. Nesse caso, a configuração de dimensionamento automático padrão vem com uma regra de escala e redução baseada em % CPU.  

3. Agora você pode adicionar mais perfis e regras com base no agendamento ou em requisitos específicos. Podemos criar uma configuração de dimensionamento automático com três perfis. Para entender as regras de dimensionamento automático e perfis, confira [Práticas recomendadas de dimensionamento automático](autoscale-best-practices.md).  

    | Perfis e regras | DESCRIÇÃO |
    |--- | --- |
    | **Perfil** |**Baseado em desempenho/métrica** |
    | Regra |Contagem de mensagens de fila de Barramento de Serviço > x |
    | Regra |Contagem de mensagens de fila de Barramento de Serviço < y |
    | Regra |CPU% > n |
    | Regra |% CPU < p |
    | **Perfil** |**Horas da manhã de dia de semana, (sem regras)** |
    | **Perfil** |**Dia de lançamento do produto (sem regras)** |

4. Eis um cenário hipotético de dimensionamento que usaremos para este passo a passo.

   * **Baseado em carga** ‑ Desejo escalar ou reduzir horizontalmente com base na carga do aplicativo hospedado no conjunto de dimensionamento.*
   * **Tamanho da Fila de Mensagens** ‑ Uso uma Fila do Barramento de Serviço para as mensagens recebidas pelo meu aplicativo. Uso contagem de mensagens da fila e o percentual de CPU e configuro um perfil padrão para disparar uma ação de escala se a contagem de mensagens ou CPU atingir o limite.\*
   * **Dia e hora da semana** ‑ Desejo ter um perfil baseado na “hora do dia” recorrente semanal chamado “Horas da manhã de dias da semana”. Com base nos dados históricos, sei que é melhor ter determinado número de instâncias de VM para lidar com a carga do meu aplicativo durante esse período.\*
   * **Datas especiais** ‑ Adicionei um perfil de “Dia de lançamento de produto”. Planejo com antecedência em relação a datas específicas para que meu aplicativo esteja pronto para lidar com a carga devido a anúncios de marketing e quando colocamos um novo produto no aplicativo.\*
   * *Os últimos dois perfis também podem ter outras regras com base em métrica de desempenho dentro deles. Nesse caso, decidi por não ter um e, em vez disso, contar com as regras com base em métricas de desempenho padrão. As regras são opcionais para perfis baseados em recorrência e data.*

     A priorização de perfis e regras do mecanismo de dimensionamento automático também é vista no artigo [Práticas recomendadas de dimensionamento automático](autoscale-best-practices.md).
     Para obter uma lista de métricas comuns para dimensionamento automático, confira [Métricas comuns para o dimensionamento automático](autoscale-common-metrics.md)

5. Verifique se você está usando o modo de **leitura/gravação** no Gerenciador de Recursos

    ![Autoscalewad, configuração de dimensionamento automático padrão](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento “perfis” na configuração de dimensionamento automático pela seguinte configuração:

    ![perfis](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Para campos com suporte e seus valores, confira [Documentação da API REST de Dimensionamento Automático](https://msdn.microsoft.com/library/azure/dn931928.aspx). Agora, sua configuração de dimensionamento automático contém os três perfis explicados anteriormente.

7. Finalmente, veja a seção **Notificação** de Dimensionamento Automático. As notificações de dimensionamento automático permitem fazer três coisas quando uma ação de escala ou redução horizontal é disparada com êxito.
   - Notificar o administrador e coadministradores de sua assinatura
   - Enviar email a um conjunto de usuários
   - Disparar uma chamada webhook. Quando disparado, esse webhook enviará metadados sobre a condição de dimensionamento automático e do recurso de conjunto de dimensionamento. Para saber mais sobre a carga de webhook de dimensionamento automático, confira [Configurar webhook e notificações por email para dimensionamento automático](autoscale-webhook-email.md).

   Adicionar o seguinte à configuração de dimensionamento automático substituindo o elemento **notificação** cujo valor é nulo

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Pressione o botão **Colocar** no Gerenciador de Recursos para atualizar a configuração de dimensionamento automático.

Você atualizou uma configuração de dimensionamento automático em um conjunto de escala de VM definido para incluir vários perfis e notificações de escala.

## <a name="next-steps"></a>Próximas etapas
Use estes links para saber mais sobre o dimensionamento automático.

[Solucionar problemas de autoescala com conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Métricas comuns para o dimensionamento automático](autoscale-common-metrics.md)

[Práticas Recomendadas para o Serviço de Aplicativo do Azure](autoscale-best-practices.md)

[Gerenciar o dimensionamento automático usando o PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Gerenciar o dimensionamento automático usando a CLI](cli-samples.md#autoscale)

[Configurar webhooks e notificações por email para dimensionamento automático](autoscale-webhook-email.md)

Referência de modelo [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
