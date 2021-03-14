---
title: Configurar o monitoramento na integridade de convidado do insights de VM usando regras de coleta de dados (versão prévia)
description: Descreve como modificar o monitoramento padrão na integridade de convidado do insights de VM em escala usando modelos do Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 0db6ed7566c53429f8b9798ac8cdafe76ca7bd5a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052136"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Configurar o monitoramento na integridade de convidado do insights de VM usando regras de coleta de dados (versão prévia)
A [integridade de convidado do insights de VM](vminsights-health-overview.md) permite que você exiba a integridade de uma máquina virtual conforme definido por um conjunto de medidas de desempenho que são amostradas em intervalos regulares. Este artigo descreve como você pode modificar o monitoramento padrão em várias máquinas virtuais usando regras de coleta de dados.


## <a name="monitors"></a>Monitores
O estado de integridade de uma máquina virtual é determinado pelo [acúmulo de integridade](vminsights-health-overview.md#health-rollup-policy) de cada um de seus monitores. Há dois tipos de monitores na integridade de convidado do insights de VM, conforme mostrado na tabela a seguir.

| Monitoramento | Descrição |
|:---|:---|
| Monitor de unidade | Mede alguns aspectos de um recurso ou aplicativo. Talvez esteja verificando um contador de desempenho para determinar o desempenho do recurso ou sua disponibilidade. |
| Monitor agregado | Agrupa vários monitores para fornecer um único estado de integridade agregado. Um monitor agregado pode conter um ou mais monitores de unidade e outros monitores agregados. |

O conjunto de monitores usados pela integridade de convidado do virtual insights e sua configuração não podem ser alterados diretamente. É possível criar [substituições](#overrides) , no entanto, que modificam o comportamento da configuração padrão. As substituições são definidas em regras de coleta de dados. Você pode criar várias regras de coleta de dados, cada uma contendo várias substituições para obter a configuração de monitoramento necessária.

## <a name="monitor-properties"></a>Propriedades do monitor
A tabela a seguir descreve as propriedades que podem ser configuradas em cada monitor.

| Propriedade | Monitores | Descrição |
|:---|:---|:---|
| habilitado | Agregado<br>Unidade | Se for true, o monitor de estado será calculado e contribuirá para a integridade da máquina virtual. Ele pode disparar um alerta de alertas está habilitado. |
| Alertas | Agregado<br>Unidade | Se for true, um alerta será disparado para o monitor quando ele se mover para um estado não íntegro. Se for false, o estado do monitor continuará a contribuir para a integridade da máquina virtual que pode disparar um alerta. |
| Aviso | Unidade | Critérios para o estado de aviso. Se nenhum, o monitor nunca vai inserir um estado de aviso. |
| Crítico | Unidade | Critérios para o estado crítico. Se nenhum, o monitor nunca entrará em um estado crítico. |
| Frequência de avaliação | Unidade | Frequência em que o estado de integridade é avaliado. |
| Lookback | Unidade | Tamanho da janela lookback em segundos. Consulte o [elemento monitorConfiguration](#monitorconfiguration-element) para obter uma descrição detalhada. |
| Tipo de Avaliação | Unidade | Define qual valor usar do conjunto de exemplo. Consulte o [elemento monitorConfiguration](#monitorconfiguration-element) para obter uma descrição detalhada. |
| Exemplo mín. | Unidade | Número mínimo de valores a serem usados para calcular o valor. |
| Exemplo de Max | Unidade | Número máximo de valores a serem usados para calcular o valor. |


## <a name="default-configuration"></a>Configuração padrão
A tabela a seguir lista a configuração padrão para cada monitor. Essa configuração padrão não pode ser alterada diretamente, mas você pode definir [substituições](#overrides) que modificarão a configuração do monitor para determinadas máquinas virtuais.


| Monitoramento | habilitado | Alertas | Aviso | Crítico | Frequência de avaliação | Lookback | Tipo de avaliação | Exemplo mín. | Máximo de amostras |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Utilização da CPU  | True | Falso | Nenhum | \> 90%    | 60 s | 240 s | Min | 2 | 3 |
| Memória disponível | True | Falso | Nenhum | \< 100 MB | 60 s | 240 s | Max | 2 | 3 |
| Sistema de arquivos      | True | Falso | Nenhum | \< 100 MB | 60 s | 120 s | Max | 1 | 1 |


## <a name="overrides"></a>Substituições
Uma *substituição* altera uma ou mais propriedades de um monitor. Por exemplo, uma substituição poderia desabilitar um monitor habilitado por padrão, definir critérios de aviso para o monitor ou modificar o limite crítico do monitor. 

As substituições são definidas em uma [regra de coleta de dados (DCR)](../agents/data-collection-rule-overview.md). Você pode criar vários DCRs com diferentes conjuntos de substituições e aplicá-los a várias máquinas virtuais. Você aplica um DCR a uma máquina virtual criando uma associação conforme descrito em [Configurar coleta de dados para o agente de Azure monitor (versão prévia)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Várias substituições

Um único monitor pode ter várias substituições. Se as substituições definirem propriedades diferentes, a configuração resultante será uma combinação de todas as substituições.

Por exemplo, o `memory|available` Monitor não especifica um limite de aviso ou habilita o alerta por padrão. Considere as seguintes substituições aplicadas a este monitor:

- A substituição 1 define `alertConfiguration.isEnabled` o valor da propriedade como `true`
- A substituição 2 define `monitorConfiguration.warningCondition` com com uma condição de limite de `< 250` .

A configuração resultante seria um monitor que entra em um estado de integridade de aviso quando menos de 250 MB de memória está disponível e cria um alerta de severidade 2 e também entra em estado de integridade crítico quando menos de 100 MB de memória disponível está disponível e cria a severidade de alerta 1 (ou altera o alerta existente da severidade 2 para 1, caso já exista).

Se duas substituições definirem a mesma propriedade no mesmo monitor, um valor terá precedência. As substituições serão aplicadas com base em seu [escopo](#scopes-element), da mais geral para a mais específica. Isso significa que as substituições mais específicas terão a maior chance de serem aplicadas. A ordem específica é a seguinte:

1. Global 
2. Subscription
3. Resource group
4. Uma máquina virtual. 

Se várias substituições no mesmo nível de escopo definirem a mesma propriedade no mesmo monitor, elas serão aplicadas na ordem em que aparecem no DCR. Se as substituições estiverem em DCRs diferentes, elas serão aplicadas em ordem alfabética das IDs de recurso DCR.


## <a name="data-collection-rule-configuration"></a>Configuração da regra de coleta de dados
Os elementos JSON na regra de coleta de dados que definem substituições são descritos nas seções a seguir. Um exemplo completo é fornecido na [regra de coleta de dados de exemplo](#sample-data-collection-rule).

## <a name="extensions-structure"></a>estrutura de extensões
A integridade do convidado é implementada como uma extensão para o agente de Azure Monitor, portanto, as substituições são definidas no `extensions` elemento da regra de coleta de dados. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `name` | Sim | Cadeia de caracteres definida pelo usuário para a extensão. |
| `streams` | Sim | Lista de fluxos aos quais os dados de integridade de convidado serão enviados. Isso deve incluir **Microsoft-HealthStateChange**.  |
| `extensionName` | Sim | Nome da extensão. Isso deve ser **HealthExtension**. |
| `extensionSettings` | Sim | Matriz de `healthRuleOverride` elementos a serem aplicados à configuração padrão. |


## <a name="extensionsettings-element"></a>elemento extensionSettings
Contém configurações para a extensão.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `schemaVersion` | Sim | Cadeia de caracteres definida pela Microsoft para representar o esquema esperado do elemento. Atualmente deve ser definido como 1,0 |
| `contentVersion` | Não | Cadeia de caracteres definida pelo usuário para controlar versões diferentes da configuração de integridade, se necessário. |
| `healthRuleOverrides` | Sim | Matriz de `healthRuleOverride` elementos a serem aplicados à configuração padrão. |

## <a name="healthrulesoverrides-element"></a>elemento healthRulesOverrides
Contém um ou mais `healthRuleOverride` elementos que cada um define uma substituição.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `scopes` | Sim | Lista de um ou mais escopos que especificam as máquinas virtuais às quais essa substituição é aplicável. Embora o DCR esteja associado a uma máquina virtual, a máquina virtual deve estar dentro de um escopo para que a substituição seja aplicada. |
| `monitors` | Sim | Lista de uma ou mais cadeias de caracteres que definem quais monitores receberão essa substituição.  |
| `monitorConfiguration` | Não | Configuração do monitor, incluindo Estados de integridade e como eles são calculados. |
| `alertConfiguration` | Não | Configuração de alerta para o monitor. |
| `isEnabled` | Não | Controla se o monitor está habilitado ou não. Monitor desabilitado alterna para o estado de integridade e os Estados *especiais desabilitados* , a menos que sejam habilitados novamente. Se omitido, o monitor herdará seu status do monitor pai na hierarquia. |


## <a name="scopes-element"></a>elemento escopos
Cada substituição tem um ou mais escopos para definir a quais máquinas virtuais a substituição deve ser aplicada. O escopo pode ser uma assinatura, um grupo de recursos ou uma única máquina virtual. Mesmo que a substituição esteja em um DCR associado a uma máquina virtual específica, ela só será aplicada a essa máquina virtual se a máquina virtual estiver dentro de um dos escopos da substituição. Isso permite que você associe amplamente um número menor de DCRs a um conjunto de VMs, mas fornece controle granular sobre a atribuição de cada substituição dentro do próprio DCR. Talvez você queira criar um pequeno conjunto de DCRs e associação a um conjunto de máquinas virtuais usando a política ao especificar substituições do monitor de integridade para diferentes subconjuntos dessas máquinas virtuais usando o elemento escopos.

A tabela a seguir mostra exemplos de escopos diferentes.

| Escopo | Exemplo |
|:---|:---|
| Máquina virtual única | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Todas as máquinas virtuais em um grupo de recursos | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Todas as máquinas virtuais em uma assinatura | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Todas as máquinas virtuais às quais a regra de coleta de dados está associada | `*` |


## <a name="monitors-element"></a>elemento Monitors
Lista de uma ou mais cadeias de caracteres que definem quais monitores na hierarquia de integridade receberão essa substituição. Cada elemento pode ser um nome de monitor ou um nome de tipo que corresponda a um ou mais monitores que receberão essa substituição. 

```json
"monitors": [
    "<monitor name>"
 ],
```



A tabela a seguir lista os nomes de monitor disponíveis atualmente.

| Nome do tipo | Name | Descrição |
|:---|:---|:---|
| root | root | Monitor de nível superior que representa a integridade da máquina virtual. | |
| utilização da CPU | utilização da CPU | Monitor de utilização da CPU. | |
| discos lógicos | discos lógicos | Monitor agregado para o estado de integridade de todos os discos monitorados na máquina virtual do Windows. | |
| discos lógicos\|* | discos lógicos \| C:<br>discos lógicos \| D: | Monitor agregado de integridade de um determinado disco na máquina virtual do Windows. | 
| \| * \| espaço livre em discos lógicos | discos lógicos \| C: \| espaço livre<br>discos lógicos \| D: \| espaço livre | Monitor de espaço livre em disco na máquina virtual do Windows. |
| filesystems | filesystems | Monitor agregado para integridade de todos os sistemas de sistema na máquina virtual Linux. |
| sistemas\|* | sistemas\|/<br>sistemas de \| /mnt | Agregar integridade de controle de monitor de um sistema de arquivos da máquina virtual Linux. | filesystems|/var/log |
| \| * \| espaço livre do sistema de sistemas | \| / \| espaço livre do sistema de sistemas<br>sistemas \| de File/mnt \| de espaço livre | Monitor de espaço livre em disco no sistema de arquivos de máquina virtual do Linux. | 
| memória | memória | Monitor agregado para integridade da memória da máquina virtual. | |
| memória \| disponível| memória \| disponível | Monitore o monitoramento da memória disponível na máquina virtual. | |


## <a name="alertconfiguration-element"></a>elemento alertConfiguration
Especifica se um alerta deve ser criado a partir do monitor.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Elemento | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Se definido como true, o monitor gerará um alerta ao alternar para um estado crítico ou de aviso e resolver o alerta ao retornar ao estado íntegro. Se for false ou omitido, nenhum alerta será gerado.  |


## <a name="monitorconfiguration-element"></a>elemento monitorConfiguration
Aplica-se somente a monitores de unidade. Define a configuração do monitor, incluindo os Estados de integridade e como eles são calculados.

Os parâmetros definem o algoritmo para calcular o valor da métrica a ser comparado com os limites. Em vez de agir em uma amostra de dados da métrica subjacente, o monitor avalia várias amostras de métricas recebidas na janela do tempo de avaliação e `lookbackSec` atrás. Todos os exemplos recebidos nesse período são considerados e, se a contagem de amostras for maior que `maxSamples` , os exemplos mais antigos acima `maxSamples` serão ignorados. 

Caso haja menos amostras no intervalo de lookback do que o `minSamples` , o monitor será alternado para o estado de integridade *desconhecido* , indicando que não há dados suficientes para tomar uma decisão informada sobre a integridade das métricas subjacentes. Se um número maior de amostras `minSamples` estiver disponível, uma função de agregação especificada pelo parâmetro evaluationtype será executada sobre o conjunto para calcular um único valor.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Elemento | Obrigatório | Descrição | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Não | Define a frequência para a avaliação do estado de integridade. Cada monitor é avaliado no momento em que o agente é iniciado e em um intervalo regular definido por esse parâmetro posteriormente. |
| `lookbackSecs`   | Não | Tamanho da janela lookback em segundos. |
| `evaluationType` | Não | `min` – Pegue o valor mínimo do conjunto de amostras inteiro<br>`max` -obter o valor máximo do conjunto de amostras inteiro<br>`avg` – obter a média dos valores do conjunto de amostras<br>`all` – Compare cada valor único no conjunto com limites. Monitorar o estado das opções se e somente se todas as amostras na condição definirem satisfazer o limite. |
| `minSamples`     | Não | Número mínimo de valores a serem usados para calcular o valor. |
| `maxSamples`     | Não | Número máximo de valores a serem usados para calcular o valor. |
| `warningCondition`  | Não | Limite e a lógica de comparação para a condição de aviso. |
| `criticalCondition` | Não | Limite e a lógica de comparação para a condição crítica. |


## <a name="warningcondition-element"></a>elemento warningCondition
Define o limite e a lógica de comparação para a condição de aviso. Se esse elemento não estiver incluído, o monitor nunca mudará para o estado de integridade de aviso.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Propriedade | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Especifica se a condição está habilitada. Se definido como **false**, a condição será desabilitada mesmo que as propriedades Threshold e Operator possam ser definidas. |
| `threshold` | Não | Define o limite para comparar o valor avaliado. |
| `operator`  | Não | Define o operador de comparação a ser usado na expressão de limite. Valores possíveis: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>elemento criticalCondition
Define a lógica de comparação e limite para a condição crítica. Se esse elemento não estiver incluído, o monitor nunca mudará para o estado de integridade crítico.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Propriedade | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Especifica se a condição está habilitada. Se definido como **false**, a condição será desabilitada mesmo que as propriedades Threshold e Operator possam ser definidas. |
| `threshold` | Não | Define o limite para comparar o valor avaliado. |
| `operator`  | Não | Define o operador de comparação a ser usado na expressão de limite. Valores possíveis: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Regra de coleta de dados de exemplo
Para obter uma regra de coleta de dados de exemplo habilitando o monitoramento de convidado, consulte [habilitar uma máquina virtual usando o modelo do Resource Manager](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre [regras de coleta de dados](../agents/data-collection-rule-overview.md).