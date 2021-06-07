---
title: Azure Monitor visualizações do Honey Comb de pasta de trabalho
description: Saiba mais sobre Azure Monitor visualizações do Honey Comb de pasta de trabalho.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736926"
---
# <a name="honey-comb-visualizations"></a>Visualizações do Honey Comb

Honey Combs permitem exibições de alta densidade de métricas ou categorias que podem, opcionalmente, ser agrupadas como clusters. Eles são úteis para identificar visualmente os hotspots e a busca detalhada.

A imagem abaixo mostra a utilização de CPU de máquinas virtuais em duas assinaturas. Cada célula representa uma máquina virtual e a cor/rótulo representa sua utilização média da CPU (os vermelhos são máquinas ativas). As máquinas virtuais são clusterizadas por assinatura.

[![Captura de tela mostra a utilização da CPU de máquinas virtuais em duas assinaturas](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Adicionando um Honey Comb

1. Alterne a pasta de trabalho para o modo de edição clicando no item Editar barra de ferramentas.
2. Use **Adicionar**  na parte inferior e *Adicionar consulta* para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione os "logs" como a *fonte de dados*, "log Analytics" como o *tipo de recurso* e para o ponto de *recurso* para um espaço de trabalho que tenha um log de desempenho de máquina virtual.
4. Use o editor de consultas para inserir o KQL para sua análise.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Executar consulta.
6. Defina a *Visualização* como "grafo".
7. Selecione **configurações de grafo**.
    1. Em *campos de layout* na parte inferior, defina:
        1. Tipo de grafo: **clusters de Hive**.
        2. ID do nó: `Id` .
        3. Agrupar por: `None` .
        4. Tamanho do nó: 100.
        5. Margem entre hexágonos: `5` .
        6. Tipo de coloração: **calor**.
        7. Campo de cor do nó: `CouterValue` .
        8. Paleta de cores: `Red to Green` .
        9. Valor mínimo: `100` .
        10. Valor máximo: `2000` .
    2. Em *configurações de formato de nó* na parte superior, defina:
        1. Conteúdo principal:
            1. Usar coluna: `Computer` .
            2. Renderizador de coluna " `Text` .
        9. Conteúdo do centro:
            1. Usar coluna: `CounterValue` .
            2. Renderizador de coluna: `Big Number` .
            3. Paleta de cores: `None` .
            4. Marque a caixa de *formatação de número personalizado* .
            5. Unidades: `Megabytes` .
            6. Dígitos fracionários máximos: `1` .
8. Selecione o botão **salvar e fechar** na parte inferior do painel.

[![Captura de tela de controle de consulta, configurações de grafo e Honey Comb com a consulta e as configurações acima](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Configurações de layout do Honey Comb

| Configuração | Explicação |
|:------------- |:-------------|
| `Node Id` | Seleciona uma coluna que fornece a ID exclusiva de nós. O valor da coluna pode ser uma cadeia de caracteres ou um número. |
| `Group By Field` | Selecione a coluna na qual agrupar os nós. |
| `Node Size` | Define o tamanho das células de hexágonos. Use com a `Margin between hexagons` propriedade para personalizar a aparência do gráfico de Honey Comb. |
| `Margin between hexagons` | Define a lacuna entre as células de hexágonos. Use com a `Node size` propriedade para personalizar a aparência do gráfico de Honey Comb. |
| `Coloring type` | Seleciona o esquema a ser usado para colorir o nó. |
| `Node Color Field` | Seleciona uma coluna que fornece a métrica na qual as áreas de nó serão baseadas. |

## <a name="node-coloring-types"></a>Tipos de cores de nó

| Tipo de cores | Explicação |
|:------------- |:-------------|
| `None` | Todos os nós têm a mesma cor. |
| `Categorical` | Os nós recebem cores com base no valor ou na categoria de uma coluna no conjunto de resultados. No exemplo acima, a cor é baseada no _tipo_ de coluna do conjunto de resultados. As paletas com suporte são `Default` , `Pastel` e `Cool tone` .  |
| `Heatmap` | Nesse tipo, as células são coloridas com base em uma coluna de métrica e em uma paleta de cores. Isso fornece uma maneira simples de realçar as métricas espalhadas pelas células. |
| `Thresholds` | Nesse tipo, as cores de célula são definidas por regras de limite (por exemplo, _cpu > 90% => vermelho, 60% > cpu > 90% => amarelo, cpu < 60% => verde_) |
| `Field Based` | Nesse tipo, uma coluna fornece valores RGB específicos a serem usados para o nó. Fornece mais flexibilidade, mas geralmente requer mais trabalho a ser habilitado.  |
      
## <a name="node-format-settings"></a>Configurações de formato de nó

Os autores do Honey Comb podem especificar qual conteúdo vai para as diferentes partes de um nó: superior, esquerdo, centro, direito e inferior. Os autores são livres para usar qualquer uma das pastas de trabalho dos renderizadores com suporte (texto, número grande, linhas do Spark, ícone, etc.).

## <a name="next-steps"></a>Próximas etapas

- Saiba como criar um [renderizador de barra de composição em pastas de trabalho](workbooks-composite-bar.md).
- Saiba como [importar Azure monitor dados de log](./powerbi.md)para o Power bi.