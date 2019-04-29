---
title: Noções básicas sobre configurações de autoescala no Azure Monitor
description: Uma análise detalhada das configurações de dimensionamento automático e como elas funcionam. Aplica-se a Máquinas Virtuais, Serviços de Nuvem e Aplicativos Web
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787488"
---
# <a name="understand-autoscale-settings"></a>Compreender configurações de Autoescala
As configurações de Autoescala ajudam a garantir que você tenha a quantidade certa de recursos em execução para lidar com a carga flutuante do seu aplicativo. Você pode definir as configurações de Autoescala para serem disparadas com base em métricas que indicam carga ou desempenho ou para serem disparadas em uma data e hora agendadas. Este artigo analisa detalhadamente a anatomia de uma configuração de Autoescala. O artigo começa com o esquema e as propriedades de uma configuração e, em seguida, percorre os diferentes tipos de perfil que podem ser configurados. Por fim, o artigo aborda como o recurso de Autoescala no Azure decide qual o perfil a ser executado em um determinado momento.

## <a name="autoscale-setting-schema"></a>Esquema de configuração de dimensionamento automático
Para ilustrar o esquema de configuração de Autoescala, a seguinte configuração de Autoescala é usada. É importante observar que essa configuração de Autoescala tem:
- Um perfil. 
- Duas regras de métrica neste perfil: uma para escalar horizontalmente e outra para reduzir horizontalmente.
  - A regra para escalar horizontalmente é disparada quando o percentual médio da métrica da CPU do conjunto de dimensionamento de máquinas virtuais é superior a 85% nos últimos 10 minutos.
  - A regra para reduzir horizontalmente é disparada quando a média do conjunto de dimensionamento de máquinas virtuais é menor que 60% no último minuto.

> [!NOTE]
> Uma configuração pode ter vários perfis. Para saber mais, consulte a seção [perfis](#autoscale-profiles). Um perfil também pode ter várias regras de escalonamento e redução horizontal definidas. Para ver como elas são avaliadas, consulte a seção [avaliação](#autoscale-evaluation).

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
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
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
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
      }
    ]
  }
}
```

| Seção | Nome do elemento | DESCRIÇÃO |
| --- | --- | --- |
| Configuração | ID | A ID do recurso da configuração de dimensionamento automático. As configurações de dimensionamento automático são um recurso do Azure Resource Manager. |
| Configuração | Nome | O nome da configuração de dimensionamento automático. |
| Configuração | location | O local da configuração de dimensionamento automático. Esse local pode ser diferente do local em que o recurso está sendo dimensionado. |
| propriedades | targetResourceUri | A ID do recurso que está sendo dimensionado. Você só pode ter uma configuração de dimensionamento automático por recurso. |
| propriedades | perfis | Uma configuração de dimensionamento automático é composta de um ou mais perfis. Cada vez que o mecanismo de dimensionamento automático é executado, ele executa um perfil. |
| Perfil | Nome | O nome do perfil. Escolha qualquer nome que o ajude a identificar o perfil. |
| Perfil | Capacity.maximum | A capacidade máxima permitida. Garante que a Autoescala, ao executar este perfil, não dimensione os recursos acima desse limite. |
| Perfil | Capacity.minimum | A capacidade mínima permitida. Garante que a Autoescala, ao executar este perfil, não dimensione os recursos abaixo desse limite. |
| Perfil | Capacity.default | Se houver algum problema ao ler a métrica do recurso (nesse caso, a CPU de "vmss1") e a capacidade atual estiver abaixo do padrão, a Autoescala escalará horizontalmente de acordo com o padrão. Isso serve para garantir a disponibilidade do recurso. Se a capacidade atual já for maior do que a capacidade padrão, a Autoescala não fará a redução horizontal. |
| Perfil | regras | A Autoescala dimensiona automaticamente entre as capacidades máximas e mínimas usando as regras do perfil. Pode haver várias regras em um perfil. Normalmente há duas regras: uma para determinar quando escalar horizontalmente e outra para determinar quando reduzir horizontalmente. |
| Regra | metricTrigger | Define a condição de métrica da regra. |
| metricTrigger | metricName | O nome da métrica. |
| metricTrigger |  metricResourceUri | A ID do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o do recurso que está sendo dimensionado. Em alguns casos, ela pode ser diferente. Por exemplo, você pode escalonar um conjunto de dimensionamento de máquinas virtuais com base no número de mensagens em uma fila de armazenamento. |
| metricTrigger | timeGrain | A duração de métrica de amostragem. Por exemplo, **TimeGrain = "PT1M"** significa que a métrica deve ser agregada a cada 1 minuto usando o método de agregação especificado no elemento de estatística. |
| metricTrigger | statistic | O método de agregação dentro do período de timeGrain. Por exemplo, **statistic = “Average”** e **timeGrain = “PT1M”** significa que as métricas devem ser agregadas a cada 1 minuto, considerando a média. Essa propriedade determina como a métrica é amostrada. |
| metricTrigger | timeWindow | O período de tempo no qual as métricas devem ser consultadas. Por exemplo, **timeWindow = “PT10M”** significa que sempre que a Autoescala for executada, ela consultará as métricas dos últimos 10 minutos. O período de tempo permite que as métricas sejam normalizadas e evita uma reação a picos transitórios. |
| metricTrigger | timeAggregation | O método de agregação usado para agregar as métricas amostradas. Por exemplo, **TimeAggregation = “Average”** deve agregar as métricas amostradas obtendo a média. No caso anterior, é obtida a média das dez amostras de um minuto. |
| Regra | scaleAction | A ação a ser executada quando o metricTrigger da regra for acionado. |
| scaleAction | direction | "Increase" para escalar horizontalmente ou "Decrease" para reduzir horizontalmente.|
| scaleAction | value | Quanto aumentar ou diminuir a capacidade do recurso. |
| scaleAction | cooldown | O período de tempo a esperar após uma operação de dimensionamento antes de escalonar novamente. Por exemplo, se **cooldown = “PT10M”**, a Autoescala não tentará escalonar novamente nos próximos 10 minutos. O resfriamento deve permitir que as métricas se estabilizem após a adição ou a remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de dimensionamento automático

Há três tipos de perfis de dimensionamento automático:

- **Perfil regular:** O perfil mais comum. Se você não precisa dimensionar seus recursos com base no dia da semana ou em um dia específico, você pode usar um perfil regular. Este perfil poderá ser configurado com regras de métrica que determinam quando escalar e quando reduzir horizontalmente. Deve haver somente um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil regular. Observe que também é possível definir um perfil para escalonar uma contagem de instâncias estáticas do recurso.

- **Perfil de Data Fixa:** Este perfil é para casos especiais. Por exemplo, digamos que você tenha um evento importante se aproximando em 26 de dezembro de 2017 (PST). Você deseja que as capacidades mínima e máxima do recurso sejam diferentes naquele dia, mas que ainda sejam dimensionadas de acordo com as mesmas métricas. Nesse caso, você deve adicionar um perfil de data fixa à lista de perfis da configuração. O perfil será configurado para ser executado somente no dia do evento. Para qualquer outro dia, a Autoescala usa o perfil regular.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Perfil de Recorrência:** Esse tipo de perfil permite garantir que o perfil sempre seja usado em um determinado dia da semana. Os perfis de recorrência têm apenas uma hora de início. Eles são executados até que o próximo perfil de recorrência ou perfil de data fixa esteja definido para iniciar. Uma configuração de Autoescala com apenas um perfil de recorrência executa esse perfil, mesmo que haja um perfil regular definido na mesma configuração. Os dois exemplos a seguir ilustram como esse perfil é usado:

    **Exemplo 1: Dias da semana vs. finais de semana**
    
    Digamos que, nos finais de semana, você deseja que sua capacidade máxima seja 4. Em dias da semana, como espera mais carga, você deseja que a capacidade máxima seja 10. Nesse caso, a configuração conterá dois perfis de recorrência, um para ser executado nos finais de semana e o outro nos dias da semana.
    A configuração tem esta aparência:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    A configuração anterior mostra que cada perfil de recorrência tem uma agenda. Essa agenda determina quando o perfil inicia a execução. O perfil pára quando é hora de executar outro perfil.

    Por exemplo, na configuração anterior, "weekdayProfile" é definido para iniciar na segunda-feira, às 12:00h. Isso significa que esse perfil começará a ser executado na segunda-feira, às 12:00h. Ela continua até sábado às 12:00h, quando o "weekendProfile" está agendado para iniciar a execução.

    **Exemplo 2: Horário comercial**
    
    Digamos que você deseja ter um limite de métrica durante o horário comercial (9:00h às 17: 00h) e outro para todos os outros horários. A configuração deve ser como esta:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    A configuração anterior mostra que "businessHoursProfile" inicia a execução na segunda-feira às 9:00h e continua até 17:00h. É o momento em que “nonBusinessHoursProfile” inicia a execução. O "nonBusinessHoursProfile" é executado até as 9:00h da terça-feira e, então, "businessHoursProfile" assume novamente. Isso é repetido até sexta-feira às 17:00h. Nesse momento, "nonBusinessHoursProfile" é executado até segunda-feira às 9:00h.
    
> [!Note]
> A interface do usuário da Autoescala no Portal do Azure impõe horários de término para perfis de recorrência e começa a executar o perfil padrão da configuração de Autoescala entre os perfis de recorrência.
    
## <a name="autoscale-evaluation"></a>Avaliação da Autoescala
Considerando que as configurações de Autoescala podem ter vários perfis e que cada perfil pode ter várias regras de métrica, é importante entender como uma configuração de Autoescala é avaliada. Cada vez que o trabalho de Autoescala é executado, ele começa escolhendo o perfil que é aplicável. Depois, a Autoescala avalia os valores mínimo e máximo e as regras de métrica do perfil, decidindo se uma ação de dimensionamento é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Qual perfil o dimensionamento automático escolherá?

A Autoescala usa a seguinte sequência para escolher o perfil:
1. Primeiro ela procura algum perfil de data fixa configurado para ser executado no momento. Se houver, a Autoescala o executará. Se houver vários perfis de data fixa para serem executados, a Autoescala selecionará o primeiro deles.
2. Se não houver nenhum perfil de data fixa, a Autoescala examinará os perfis de recorrência. Se um perfil de recorrência for encontrado, ela o executará.
3. Se não houver nenhum perfil de data fixa nem de recorrência, a Autoescala executará o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como o dimensionamento automático avalia várias regras?

Depois que a Autoescala determina qual perfil executar, ela avalia todas as regras para escalar horizontalmente no perfil (essas são regras com **direction = “Increase”**).

Se uma ou mais regras para escalar horizontalmente forem disparadas, a Autoescala calculará a nova capacidade determinada pela **scaleAction** de cada uma dessas regras. Então, ela escalará horizontalmente até o máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que há um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de escalonamento horizontal: uma que aumenta a capacidade em 10% e outra que aumenta 3 pontos na capacidade. A primeira regra resultará em uma nova capacidade igual a 11 e a segunda regra resultará em uma capacidade igual a 13. Para garantir a disponibilidade do serviço, a Autoescala escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

Se nenhuma regra para escalar horizontalmente for acionada, a Autoescala avaliará todas as regras para reduzir horizontalmente (regras com **direction = “Decrease”**). O dimensionamento automático só executará uma ação de redução se todas as regras para reduzir forem acionadas.

A Autoescala calculará a nova capacidade determinada pela **scaleAction** de cada uma dessas regras. Em seguida, ele escolherá a ação de dimensionamento que resultará no máximo dessas capacidades para garantir a disponibilidade do serviço.

Por exemplo, digamos que há um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual de 10. Há duas regras de redução horizontal: uma que reduz a capacidade em 50% e outra que diminui 3 pontos na capacidade. A primeira regra resultará em uma nova capacidade igual a 5 e a segunda regra resultará em uma capacidade igual a 7. Para garantir a disponibilidade do serviço, a Autoescala escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a Autoescala consultando o seguinte:

* [Visão geral do dimensionamento automático](../../azure-monitor/platform/autoscale-overview.md)
* [Métricas comuns de dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Práticas recomendadas para dimensionamento automático do Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](../../azure-monitor/platform/autoscale-webhook-email.md)
* [API REST do Dimensionamento Automático](https://msdn.microsoft.com/library/dn931953.aspx)
