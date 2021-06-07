---
title: Entender como funcionam os alertas de métrica no Azure Monitor.
description: Obtenha uma visão geral do que você pode fazer com alertas de métrica e como eles funcionam no Azure Monitor.
ms.date: 03/11/2021
ms.topic: conceptual
ms.openlocfilehash: 8a243f0a2130e0ec2ebafe726f48e07c148807c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016076"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Entender como funcionam os alertas de métrica no Azure Monitor

Os alertas de métrica no Azure Monitor funcionam com métricas multidimensionais. Essas métricas podem ser [métricas de plataforma](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [métricas personalizadas](../essentials/metrics-custom-overview.md), [logs populares do Azure Monitor convertidos em métricas](./alerts-metric-logs.md) e métricas do Application Insights. Os alertas de métrica verificam, em intervalos regulares, se as condições em uma ou mais séries temporais de métrica são verdadeiras e notificam você quando as condições são atendidas. Os alertas de métrica são baseados em estado, ou seja, eles só enviam notificações quando o estado é alterado.

## <a name="how-do-metric-alerts-work"></a>Como funcionam os alertas de métrica?

Você pode definir uma regra de alerta de métrica especificando um recurso de destino a ser monitorado, o nome, o tipo de condição (estático ou dinâmico) e a condição (um operador e um limite/sensibilidade) da métrica, além de um grupo de ações a disparar quando a regra de alerta é acionada. Os tipos de condição afetam a determinação dos limites. [Saiba mais sobre as opções de sensibilidade e o tipo de condição de Limites Dinâmicos](../alerts/alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Regra de alerta com o tipo de condição estática

Digamos que você tenha criado uma regra de alerta de métrica simples com limite estático da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: porcentagem de CPU
- Tipo de condição: estático
- Tipo de agregação (estatística que é obtida por meio dos valores brutos de métrica. Os [tipos de agregação com suporte](../essentials/metrics-aggregation-explained.md#aggregation-types) são Mínimo, Máximo, Média, Total, Contagem): média
- Período (a janela de retrocesso na qual os valores de métrica são verificados): nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições foram atendidas): 1 minuto
- Operador: maior que
- Limite: 70

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada minuto, examina valores de métrica para os últimos 5 minutos e verifica se a média desses valores excede 70. Se a condição for atendida, ou seja, a média de porcentagem de CPU nos últimos 5 minutos exceder 70, a regra de alerta disparará uma notificação de ativação. Se você configurou um email ou uma ação de web hook no grupo de ações associado à regra de alerta, receberá uma notificação de ativação em ambos.

Quando você está usando várias condições em uma regra, a regra soma as condições. Ou seja, um alerta é acionado quando todas as condições na regra de alerta são avaliadas como verdadeiras e são resolvidas quando uma das condições não é mais verdadeira. Um exemplo para esse tipo de regra de alerta seria monitorar uma máquina virtual do Azure e alertar quando "o percentual de CPU for maior que 90%" e "o comprimento da fila for de mais de 300 itens".

### <a name="alert-rule-with-dynamic-condition-type"></a>Regra de alerta com o tipo de condição dinâmica

Digamos que você tenha criado uma regra de alerta de métrica simples com limites dinâmicos da seguinte maneira:

- Recurso de destino (o recurso do Azure que você deseja monitorar): myVM
- Métrica: porcentagem de CPU
- Tipo de condição: dinâmico
- Tipo de agregação (estatística que é obtida por meio dos valores brutos de métrica. Os [tipos de agregação com suporte](../essentials/metrics-aggregation-explained.md#aggregation-types) são Mínimo, Máximo, Média, Total, Contagem): média
- Período (a janela de retrocesso na qual os valores de métrica são verificados): nos últimos 5 minutos
- Frequência (a frequência com que o alerta de métrica verifica se as condições foram atendidas): 1 minuto
- Operador: maior que
- Sensibilidade: média
- Períodos de retrocesso: 4
- Número de violações: 4

Depois que a regra de alerta for criada, o algoritmo de aprendizado de máquina de Limites Dinâmicos adquire dados históricos que estão disponíveis, calcula o limite que melhor atende ao padrão de comportamento da série de métricas e aprende continuamente com base em novos dados a fim de tornar o limite mais preciso.

Desde o momento em que a regra de alerta é criada, o monitor é executado a cada 1 minuto e examina valores de métrica nos últimos 20 minutos agrupados em períodos de 5 minutos, e verifica se a média dos valores de período em cada um dos 4 períodos excede o limite esperado. Se a condição é atendida, ou seja, a média percentual da CPU nos últimos 20 minutos (quatro períodos de 5 minutos) desviou do comportamento esperado quatro vezes, a regra de alerta aciona uma notificação ativada. Se você configurou um email ou uma ação de web hook no grupo de ações associado à regra de alerta, receberá uma notificação de ativação em ambos.

### <a name="view-and-resolution-of-fired-alerts"></a>Exibição e resolução de alertas acionados

Os exemplos acima de regras de alerta também podem ser exibidos no portal do Azure na folha **Todos os Alertas**.

Se o uso de "myVM" continuar acima do limite em verificações subsequentes, a regra de alerta não será acionada novamente até que as condições sejam resolvidas.

Após algum tempo, o uso de "myVM" volta ao normal (fica abaixo do limite). A regra de alerta monitora a condição mais duas vezes, para enviar uma notificação de resolução. A regra de alerta envia uma mensagem de resolução/desativação quando a condição de alerta não é atendida por três períodos consecutivos para reduzir o excesso em caso de condições intermitentes.

Como a notificação de resolução é enviada por email ou webhooks, o status da instância do alerta (chamado de estado do monitor) no portal do Azure também é definido como resolvido.

> [!NOTE]
>
> Quando uma regra de alerta monitora várias condições, um alerta acionado é resolvido caso pelo menos uma das condições não seja mais atendida por três períodos consecutivos.

### <a name="using-dimensions"></a>Usando dimensões

Os alertas de métrica no Azure Monitor também oferecem suporte ao monitoramento de várias combinações de valores de dimensões com uma regra. Vamos entender por que você pode usar várias combinações de dimensão com a ajuda de um exemplo.

Digamos que você tem um Plano do Serviço de Aplicativo para seu site. Você deseja monitorar o uso da CPU em várias instâncias de execução do site da Web/aplicativo. É possível fazer isso usando uma regra de alerta de métrica como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Tipo de condição: estático
- Dimensões
  - Instance = InstanceName1, InstanceName2
- Tipo de agregação: Média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Como antes, essa regra monitora se a média de uso da CPU nos últimos 5 minutos ultrapassou 70%. No entanto, com a mesma regra você pode monitorar duas instâncias em execução no site. Cada instância será monitorada individualmente e você receberá notificações individualmente.

Digamos que você tenha um aplicativo Web que esteja sob grande demanda e é necessário adicionar mais instâncias. A regra acima ainda monitora apenas duas instâncias. No entanto, você pode criar uma regra como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Tipo de condição: estático
- Dimensões
  - Instância = *
- Tipo de agregação: Média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Limite: 70

Essa regra monitorará automaticamente todos os valores para a instância, ou seja, você pode monitorar suas instâncias conforme elas surgem sem precisar modificar a regra de alerta de métrica novamente.

Ao monitorar várias dimensões, a regra de alertas Limites Dinâmicos pode criar limites ajustados para centenas de séries de métricas por vez. Os Limites Dinâmicos resultam em menos regras de alerta a gerenciar e economia de tempo significativa com o gerenciamento e a criação de regras de alertas.

Digamos que você tenha um aplicativo Web com muitas instâncias e não sabe qual é o limite mais adequado. As regras acima sempre usam o limite de 70%. No entanto, você pode criar uma regra como mostrado abaixo:

- Recurso de destino: myAppServicePlan
- Métrica: porcentagem de CPU
- Tipo de condição: dinâmico
- Dimensões
  - Instância = *
- Tipo de agregação: Média
- Período: nos últimos 5 minutos
- Frequência: 1 min
- Operador: GreaterThan
- Sensibilidade: média
- Períodos de retrocesso: 1
- Número de violações: 1

Essa regra monitora se o uso médio da CPU nos últimos 5 minutos excede o comportamento esperado para cada instância. Com a mesma regra, você pode monitorar instâncias à medida que elas surgem, sem precisar modificar novamente a regra de alerta de métrica. Cada instância obtêm um limite que se ajusta ao padrão de comportamento da série de métricas e que muda continuamente com base nos novos dados para tornar o limite mais preciso. Como antes, cada instância é monitorada individualmente e você obtém notificações individualmente.

Aumentar os períodos de retrocesso e o número de violações também pode permitir filtrar alertas apenas para o alerta na sua definição de um desvio significativo. [Saiba mais sobre as opções avançadas de Limites Dinâmicos](../alerts/alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> É recomendável escolher uma *Granularidade de agregação (período)* maior do que a *Frequência de avaliação*, para reduzir a probabilidade de perder a primeira avaliação da série temporal adicionada nos seguintes casos:
> - Regra de alerta de métrica que monitora várias dimensões – quando uma nova combinação de valor de dimensão é adicionada
> - Regra de alerta de métrica que monitora vários recursos – quando um novo recurso é adicionado ao escopo
> - Regra de alerta de métrica que monitora uma métrica que não é emitida continuamente (métrica esparsa) – quando a métrica é emitida após um período de mais de 24 horas em que ela não foi emitida



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Monitoramento em escala usando alertas de métrica no Azure Monitor

Até agora, você viu como um único alerta de métrica pode ser usado para monitorar uma ou diversas séries de métricas temporais relacionadas a um único recurso do Azure. Em muitas situações, podemos querer a mesma regra de alerta aplicada a vários recursos. O Azure Monitor também dá suporte ao monitoramento de múltiplos recursos (do mesmo tipo) com uma única regra de alerta de métrica para recursos que existem na mesma região do Azure. 

No momento, esse recurso tem suporte para métricas de plataforma (não métricas personalizadas) para os seguintes serviços nas seguintes nuvens do Azure:

| Serviço | Azure público | Governo | China |
|:--------|:--------|:--------|:--------|
| Máquinas virtuais<sup>1</sup>  | **Sim** | **Sim** | **Sim** |
| Bancos de dados do SQL Server | **Sim** | **Sim** | **Sim** |
| Pools elásticos do SQL Server | **Sim** | **Sim** | **Sim** |
| Pools de capacidade de arquivos do NetApp | **Sim** | **Sim** | **Sim** |
| Volumes de arquivos do NetApp | **Sim** | **Sim** | **Sim** |
| Cofres de chaves | **Sim** | **Sim** | **Sim** |
| Cache Redis do Azure | **Sim** | **Sim** | **Sim** |
| Dispositivos do Data Box Edge | **Sim** | **Sim** | **Sim** |

<sup>1</sup> Não há suporte para métricas de rede de máquina virtual (rede no total, total de saída de rede, fluxos de entrada, fluxos de saída, taxa de criação máxima de fluxos de entrada, taxa de criação máxima de fluxos de saída).

Você pode especificar o escopo de monitoramento de uma única regra de alerta de métrica de uma destas três maneiras. Por exemplo, com máquinas virtuais, você pode especificar o escopo como:  

- uma lista de máquinas virtuais (em uma região do Azure) dentro de uma assinatura
- todas as máquinas virtuais (em uma única região do Azure) em um ou mais grupos de recursos em uma assinatura
- todas as máquinas virtuais (em uma região do Azure) em uma assinatura

> [!NOTE]
>
> O escopo de uma regra de alerta de métrica de vários recursos precisa conter pelo menos um recurso do tipo de recurso selecionado.

Criar regras de alerta de métrica que monitorem vários recursos é como [criar qualquer outro alerta de métrica](../alerts/alerts-metric.md) que monitore um único recurso. A única diferença é que você selecionaria todos os recursos que quisesse monitorar. Você também pode criar essas regras por meio de [modelos do Azure Resource Manager](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources). Você receberá notificações individuais para cada recurso monitorado.

> [!NOTE]
>
> Em uma regra de alerta de métrica que monitora vários recursos, apenas uma condição é permitida.

## <a name="typical-latency"></a>Latência típica

Para alertas de métrica, normalmente você será notificado em menos de 5 minutos se definir a frequência de regra de alerta para ser de 1 minuto. Em caso de carga pesada para sistemas de notificação, você poderá ter latência mais longa.

## <a name="supported-resource-types-for-metric-alerts"></a>Tipos de recursos com suporte para alertas de métrica

Você pode encontrar a lista completa dos tipos de recursos com suporte neste [artigo](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).


## <a name="next-steps"></a>Próximas etapas

- [Saiba como criar, exibir e gerenciar alertas de métrica no Azure](../alerts/alerts-metric.md)
- [Saiba como criar alertas no Azure Monitor Metrics Explorer](../essentials/metrics-charts.md#alert-rules)
- [Saiba como criar alertas de métrica usando modelos do Azure Resource Manager](./alerts-metric-create-templates.md)
- [Saiba mais sobre grupos de ação](./action-groups.md)
- [Saiba mais sobre o tipo de condição de Limites Dinâmicos](../alerts/alerts-dynamic-thresholds.md)
- [Saiba mais sobre como solucionar problemas em alertas de métricas](alerts-troubleshoot-metric.md)
