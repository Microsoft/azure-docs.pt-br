---
title: Solucionando problemas do dimensionamento automático do Azure
description: Rastreamento de problemas com o dimensionamento automático do Azure usado em Service Fabric, máquinas virtuais, aplicativos Web e serviços de nuvem.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 8c4589acd17e76d1341d5aceada67e565c8f8c37
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251260"
---
# <a name="troubleshooting-azure-autoscale"></a>Solucionando problemas do dimensionamento automático do Azure
 
Azure Monitor dimensionamento automático ajuda você a ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite que você adicione recursos para lidar com aumentos de carga e também economiza dinheiro removendo os recursos que estão ociosos. Você pode dimensionar com base em uma agenda, data/hora fixa ou métrica de recurso que você escolher. Para obter mais informações, consulte [visão geral de dimensionamento automático](autoscale-overview.md).

O serviço de dimensionamento automático fornece métricas e logs para entender quais ações de escala ocorreram e a avaliação das condições que levaram a essas ações. Você pode encontrar respostas para perguntas como:

- Por que o meu serviço se expandiu ou não?
- Por que meu serviço não foi dimensionado?
- Por que uma ação de dimensionamento automático falhou?
- Por que uma ação de dimensionamento automático está levando tempo para ser dimensionada?
  
## <a name="autoscale-metrics"></a>Métricas de dimensionamento automático

O dimensionamento automático fornece [quatro métricas](metrics-supported.md#microsoftinsightsautoscalesettings) para entender sua operação. 

- **Valor de métrica observado** -o valor da métrica na qual você escolheu realizar a ação de escala, como visto ou calculado pelo mecanismo de dimensionamento automático. Como uma única configuração de dimensionamento automático pode ter várias regras e, portanto, várias fontes métricas, você pode filtrar usando a "origem da métrica" como uma dimensão.
- **Limite de métrica** -o limite definido para tomar a ação de escala. Como uma única configuração de dimensionamento automático pode ter várias regras e, portanto, várias fontes métricas, você pode filtrar usando "regra métrica" como uma dimensão.
- **Capacidade observada** -o número ativo de instâncias do recurso de destino, como visto pelo mecanismo de dimensionamento automático.
- **Ações de dimensionamento iniciadas** - O número de ações de expansão e redução horizontal iniciadas pelo mecanismo de dimensionamento automático. Você pode filtrar por escala horizontal versus escala em ações.

Você pode usar o [Metrics Explorer](metrics-getting-started.md) para fazer o gráfico das métricas acima em um único lugar. O gráfico deve mostrar:

  - a métrica real
  - a métrica como visto/computada pelo mecanismo de dimensionamento automático
  - o limite para uma ação de escala
  - a alteração na capacidade 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exemplo 1-analisando uma regra de dimensionamento automático simples 

Temos uma configuração simples de dimensionamento automático para um conjunto de dimensionamento de máquinas virtuais que:

- escala horizontalmente quando a porcentagem média de CPU de um conjunto for maior que 70% por 10 minutos 
- dimensiona quando a porcentagem de CPU do conjunto é inferior a 5% por mais de 10 minutos. 

Vamos examinar as métricas do serviço de dimensionamento automático.
 
![Captura de tela mostra um exemplo de CPU percentual do conjunto de dimensionamento de máquinas virtuais.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Exemplo de CPU percentual do conjunto de dimensionamento de máquinas virtuais](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

**_Figura 1a-percentual de métrica de CPU para o conjunto de dimensionamento de máquinas virtuais e a métrica de valor de métrica observada para configuração de dimensionamento automático_*

![Limite de métrica e capacidade observada](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

_*_Figura 1b-limite de métrica e capacidade observada_*_

Na figura 1B, o *limite de métrica* _ * (linha azul clara) para a regra de expansão é 70.  A **capacidade observada** (linha azul escura) mostra o número de instâncias ativas, que atualmente são 3. 

> [!NOTE]
> Você precisará filtrar o **limite de métrica** pela regra de gatilho de métrica dimensão de escala horizontal (aumentar) para ver o limite de expansão e pela regra de dimensionamento (diminuir). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exemplo 2-dimensionamento automático avançado para um conjunto de dimensionamento de máquinas virtuais

Temos uma configuração de dimensionamento automático que permite que um recurso de conjunto de dimensionamento de máquinas virtuais Escale horizontalmente com base em seus próprios **fluxos de saída** de métrica. Observe que a opção **dividir métrica por contagem de instâncias** para o limite de métrica está marcada. 

A regra de ação de escala é: 

Se o valor do **fluxo de saída por instância** for maior que 10, o serviço de dimensionamento automático deverá escalar horizontalmente em 1 instância. 

Nesse caso, o valor da métrica observada do mecanismo de dimensionamento automático é calculado como o valor de métrica real dividido pelo número de instâncias. Se o valor da métrica observada for menor que o limite, nenhuma ação de expansão será iniciada. 
 
![Captura de tela mostra a página média de fluxos de saída com um exemplo de gráficos de métricas de dimensionamento automático de conjunto de dimensionamento de máquinas virtuais.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Exemplo de gráficos de métricas de dimensionamento automático do conjunto de escala](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

**_Figura 2 – exemplo de escala de dimensionamento automático do conjunto de dimensionamento de máquinas virtuais_* _

Na Figura 2, você pode ver dois gráficos de métricas. 

O gráfico na parte superior mostra o valor real da métrica _ *fluxos de saída**. O valor real é 6. 

O gráfico na parte inferior mostra alguns valores. 
 - O **valor de métrica observado** (azul claro) é 3 porque há duas instâncias ativas e 6 divididas por 2 são 3. 
 - A **capacidade observada** (roxo) mostra a contagem de instâncias vista pelo mecanismo de dimensionamento automático. 
 - O **limite de métrica** (verde claro) é definido como 10. 

Se houver várias regras de ação de escala, você poderá usar a opção de divisão ou **Adicionar filtro** no gráfico do Metrics Explorer para examinar a métrica por uma fonte ou regra específica. Para obter mais informações sobre como dividir um gráfico de métricas, consulte [recursos avançados de gráficos de métrica – divisão](metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>Exemplo 3-noções básicas sobre eventos de dimensionamento automático

Na tela de configuração de dimensionamento automático, vá para a guia **histórico de execução** para ver as ações de escala mais recentes. A guia também mostra a alteração na **capacidade observada** ao longo do tempo. Para obter mais detalhes sobre todas as ações de dimensionamento automático, incluindo operações como atualizar/excluir dimensionamento automático, exibir o log de atividades e filtrar por operações de dimensionamento automático.

![Histórico de execução de configurações de autoescala](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Logs de recursos de dimensionamento automático

O mesmo que qualquer outro recurso do Azure, o serviço de dimensionamento automático fornece [logs de recursos](platform-logs-overview.md). Há duas categorias de logs.

- **Avaliações de dimensionamento automático** -o mecanismo de dimensionamento automático registra entradas de log para cada avaliação de condição única toda vez que faz uma verificação.  A entrada inclui detalhes sobre os valores observados das métricas, as regras avaliadas e se a avaliação resultou em uma ação de escala ou não.

- **Ações de escala de dimensionamento automático** -o mecanismo registra eventos de ação de escala iniciados pelo serviço de dimensionamento automático e os resultados dessas ações de escala (êxito, falha e a quantidade de dimensionamento ocorrida conforme visto pelo serviço de dimensionamento automático).

Assim como ocorre com qualquer serviço com suporte Azure Monitor, você pode usar [as configurações de diagnóstico](diagnostic-settings.md) para rotear esses logs:

- para seu espaço de trabalho Log Analytics para análise detalhada
- para os hubs de eventos e, em seguida, para as ferramentas que não são do Azure
- para sua conta de armazenamento do Azure para arquivamento  

![Configurações de diagnóstico de dimensionamento automático](media/autoscale-troubleshoot/diagnostic-settings.png)

A imagem anterior mostra as configurações de diagnóstico de autoescala portal do Azure. Lá, você pode selecionar a guia diagnóstico/recurso logs e habilitar a coleta e o roteamento de logs. Você também pode executar a mesma ação usando a API REST, a CLI, o PowerShell, os modelos do Resource Manager para configurações de diagnóstico, escolhendo o tipo de recurso como *Microsoft. insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Solução de problemas usando logs de dimensionamento automático 

Para obter a melhor experiência de solução de problemas, é recomendável rotear seus logs para Azure Monitor logs (Log Analytics) por meio de um espaço de trabalho ao criar a configuração de dimensionamento automático. Esse processo é mostrado na imagem na seção anterior. Você pode validar as ações de avaliação e escala melhor usando Log Analytics.

Depois de configurar os logs de dimensionamento automático para serem enviados para o espaço de trabalho do Log Analytics, você pode executar as consultas a seguir para verificar os logs. 

Para começar, tente esta consulta para exibir os logs de avaliação de dimensionamento automático mais recentes:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Ou tente a consulta a seguir para exibir os logs de ação de dimensionamento mais recentes:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Use as seções a seguir para essas perguntas. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Ocorreu uma ação de escala que eu não esperava

Primeiro, execute a consulta para a ação de escala para localizar a ação de dimensionamento em que você está interessado. Se for a ação de escala mais recente, use a seguinte consulta:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Selecione o campo CorrelationId do log de ações de escala. Use CorrelationId para localizar o log de avaliação correto. A execução da consulta abaixo exibirá todas as regras e condições avaliadas, levando à ação de escala.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Qual perfil causou uma ação de escala?

Ocorreu uma ação dimensionada, mas você tem regras e perfis sobrepostos e precisa rastrear o que causou a ação. 

Localize a CorrelationId da ação de escala (conforme explicado no exemplo 1) e execute a consulta nos logs de avaliação para saber mais sobre o perfil.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

A avaliação completa do perfil também pode ser compreendida melhor usando a consulta a seguir

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Uma ação de escala não ocorreu

Eu esperava uma ação de dimensionamento e ela não ocorreu. Pode não haver nenhum evento de ação de escala ou logs.

Examine as métricas de dimensionamento automático se você estiver usando uma regra de escala baseada em métrica. É possível que o **valor da métrica observada** ou a **capacidade observada** não sejam o que você esperava e, portanto, a regra de escala não foi acionada. Você ainda verá avaliações, mas não uma regra de expansão. Também é possível que o tempo de resfriamento mantenha a ocorrência de uma ação de dimensionamento. 

 Examine os logs de avaliação de dimensionamento automático durante o período de tempo que você esperava que a ação de dimensionamento ocorra. Examine todas as avaliações que ele fez e por que ele decidiu não disparar uma ação de escala.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Falha na ação de escala

Pode haver um caso em que o serviço de dimensionamento automático realizou a ação de dimensionamento, mas o sistema decidiu não dimensionar ou falhar para concluir a ação de escala. Use essa consulta para localizar as ações de escala com falha.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Crie regras de alerta para ser notificado de falhas ou ações de dimensionamento automático. Você também pode criar regras de alerta para ser notificado sobre eventos de dimensionamento automático.

## <a name="schema-of-autoscale-resource-logs"></a>Esquema de logs de recursos de dimensionamento automático

Para obter mais informações, consulte [autoescala logs de recursos](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Próximas etapas
Leia informações sobre [práticas recomendadas de dimensionamento automático](autoscale-best-practices.md). 
