---
title: Diagnosticar incidentes usando o revisor de métricas
titleSuffix: Azure Cognitive Services
description: Saiba como diagnosticar incidentes usando o Metrics Advisor e obter exibições detalhadas de anomalias em seus dados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: d1f792859aa5407cfaceda0e3ed1b5c21ee160f0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043367"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Como: diagnosticar um incidente usando o assistente de métricas

O Metrics Advisor fornece vários recursos para diagnóstico e fornece uma visão detalhada dos incidentes detectados e fornece análise de causa raiz. Quando um grupo de anomalias detectadas em uma métrica, o revisor de métricas agrupará anomalias em uma hierarquia e analisará sobre ela.

> [!NOTE]
> Atualmente, o assistente de métricas dá suporte ao diagnóstico de incidentes para métricas com pelo menos uma dimensão e a medida com o tipo  *numérico* . Sua métrica precisa ter um valor de dimensão agregado como SUM para cada dimensão, que é usada para criar a hierarquia de diagnóstico. O Metrics Advisor oferece [**configurações de acúmulo automático**](onboard-your-data.md#automatic-roll-up-settings) para ajudar na geração de valores agregados. 

Clique no **Hub de incidentes** na janela de navegação à esquerda para ver todos os incidentes em uma determinada métrica. Na parte superior da página, você pode selecionar diferentes métricas para ver as configurações de detecção e os resultados da detecção e alterar o intervalo de tempo.

> [!TIP]
> Você também pode acessar o **Hub de incidentes** da:
> * Clicando em um ponto de dados na visualização de sua métrica e usando os links na parte inferior da janela **adicionar comentários** que aparece.
> * Clicando em uma das anomalias na guia **incidentes** para sua métrica. 

A seção de **visão geral** contém resultados de detecção, incluindo contagens de anomalias e alertas dentro do intervalo de tempo selecionado.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/incident-hub-overview.png":::

Os incidentes detectados dentro da métrica selecionada e do intervalo de tempo são listados na **lista de incidentes**. Há opções para filtrar e ordenar os incidentes. Por exemplo, por severidade. Clique em um dos incidentes para ir para a página de **incidentes** para obter mais diagnósticos.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/incident-list.png":::

A seção de **diagnóstico** permite executar uma análise detalhada de um incidente e ferramentas para identificar as causas raiz.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Aviso de causa raiz

Quando um grupo de anomalias é detectado em uma métrica e causa um incidente, o Orientador de métricas tentará analisar a causa raiz do incidente. O **Conselho de causa raiz** fornece sugestões automáticas para causas prováveis de um incidente. Esse recurso só estará disponível se houver um valor agregado dentro da dimensão. Se a métrica não tiver nenhuma dimensão, a causa raiz será ela mesma. As causas raiz são listadas no painel do lado direito e pode haver várias razões listadas. Se não houver dados na tabela, isso significa que sua dimensão não atende aos requisitos para executar a análise.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Hub de incidentes":::


Quando a métrica de causa raiz é fornecida com dimensões específicas, você pode clicar em **ir para métrica** para exibir mais detalhes da métrica.

## <a name="incident-tree"></a>Árvore de incidentes

Juntamente com a análise automatizada sobre possíveis causas raiz, o assistente de métricas dá suporte à análise manual da causa raiz, usando a **árvore de incidentes**. Há dois tipos de árvore de incidentes na página de incidentes: a árvore de **diagnóstico rápido** e a **árvore interativa**.

A árvore de diagnóstico rápido é para diagnosticar um incidente atual e o nó raiz é limitado ao nó raiz do incidente atual. Você pode expandir e recolher os nós de árvore clicando nele, e sua série será mostrada junto com a série de incidentes atual no gráfico acima da árvore.

A árvore interativa permite diagnosticar incidentes atuais, bem como incidentes mais antigos, e aqueles relacionados. Ao usar a árvore interativa, clique com o botão direito do mouse em um nó para abrir um menu de ação, no qual você pode escolher uma dimensão para fazer drill up nos nós raiz e uma dimensão para fazer uma busca detalhada de cada nó. Ao clicar no botão Cancelar da lista dimensão na parte superior, você pode remover o drill up ou para baixo dessa dimensão. Clique com o botão esquerdo do nó para selecioná-lo e mostre sua série junto com a série de incidentes atual no gráfico.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Busca detalhada de anomalia

Quando você estiver exibindo informações de incidentes, talvez seja necessário obter informações mais detalhadas, por exemplo, para diferentes dimensões e carimbos de data/hora. Se os dados tiverem uma ou mais dimensões, você poderá usar a função de busca detalhada para obter uma exibição mais detalhada. 

Para usar a função de busca detalhada, clique na guia **análise de métrica** no **Hub de incidentes**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Hub de incidentes":::

A configuração de **dimensões** é uma lista de dimensões para um incidente, você pode selecionar outros valores de dimensão disponíveis para cada um. Depois que os valores de dimensão forem alterados. A configuração de **carimbo de data/hora** permite exibir o incidente atual em momentos diferentes no tempo.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Selecionar opções de análise e escolher uma dimensão

Há dois tipos de opções de busca detalhada: **pesquisa vertical** e **comparação horizontal**.

> [!Note]
> 1. Para fazer drill down, você pode explorar os dados de diferentes valores de dimensão, exceto as dimensões selecionadas do currenly. 
> 2. Para comparação horizontal, você pode explorar os dados de diferentes valores de dimensão, exceto as dimensões todas.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Hub de incidentes":::

### <a name="value-comparison-for-different-dimension-values"></a>Comparação de valor para valores de dimensão diferentes

A segunda seção da guia fazer drill down é uma tabela com comparações para valores de dimensão diferentes. Ele inclui o valor, valor de linha de base, valor de diferença, valor delta e se é uma anomalia.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Comparações de valor e valor esperado para um valor de dimensão diferente

A terceira seção da guia fazer drill down é um histograma com os valores e valores esperados, para valores de dimensão diferentes. O histograma é classificado pela diferença entre o valor e o valor esperado. Você pode encontrar o valor inesperado com o maior impacto facilmente. Por exemplo, na imagem acima, podemos encontrar isso, exceto o valor inteiro, **US7** contribui mais para a anomalia.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualização de valor bruto
A última parte da guia aprofundar é um gráfico de linhas dos valores brutos. Com esse gráfico fornecido, você não precisa navegar até a página métrica para exibir detalhes.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Exibir anomalias semelhantes usando o clustering de série temporal

Ao exibir um incidente, você pode usar a guia **clustering de série temporal semelhante** para ver as várias séries associadas a ele. As séries em um grupo são resumidas juntas. Na imagem acima, podemos saber que há pelo menos dois grupos de séries. Esse recurso só estará disponível se os seguintes requisitos forem atendidos:

1. As métricas devem ter uma ou mais dimensões ou valores de dimensão.
2. A série dentro de uma métrica deve ter uma tendência semelhante.

As dimensões disponíveis são listadas na parte superior da guia e você pode fazer uma seleção para especificar a série.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="Hub de incidentes":::

## <a name="compare-time-series"></a>Comparar série temporal

Às vezes, quando uma anomalia é detectada em uma série temporal específica, é útil compará-la com várias outras séries em uma única visualização. Clique na guia **comparar ferramentas** e, em seguida, clique no botão azul **+ Adicionar** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Hub de incidentes" lightbox="../media/diagnostics/add-series.png":::

Selecione uma série do feed de dados. Você pode escolher a mesma granularidade ou outra. Selecione as dimensões de destino e carregue a tendência da série e clique em **OK** para compará-la com uma série anterior. A série será agrupada em uma visualização. Você pode continuar a adicionar mais séries para comparação e obter mais informações. Clique no menu suspenso na parte superior da guia **comparar ferramentas** para comparar os dados de série temporal durante um período de mudança de tempo.  

> [!Warning]
> Para fazer uma comparação, a análise de dados de série temporal pode exigir turnos em pontos de dados para que a granularidade dos seus dados precise dar suporte a ele. Por exemplo, se seus dados forem semanais e você usar a comparação **dia a dia** , você não receberá nenhum resultado. Neste exemplo, você usaria a comparação **mês a mês** .

Depois de selecionar uma comparação de turno de tempo, você pode selecionar se deseja comparar os valores de dados, os valores Delta ou o Delta de porcentagem.

> [!Note]
> * **Valor de dados** é o valor de dados brutos.
> * O **valor delta** é a diferença entre valor bruto e valor comparado.
> * O **valor delta percentual** é a diferença entre valor bruto e valor comparado dividido pelo valor comparado.

## <a name="related-incidents-across-metrics"></a>Incidentes relacionados entre métricas

Às vezes, talvez seja necessário verificar os incidentes de diferentes métricas ao mesmo tempo ou incidentes relacionados em outras métricas. Você pode encontrar uma lista de incidentes relacionados na seção **análise de métrica cruzada** . 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Hub de incidentes":::

Antes de poder ver incidentes relacionados à métrica atual, você precisa adicionar uma relação entre as métricas. Clique em **configurações de grafo de métricas** para adicionar uma relação. Somente as métricas com os mesmos nomes de dimensão podem ser relacionadas. Use os seguintes parâmetros.

- Métrica de & do feed de dados atual: o feed de dados e a métrica do incidente atual
- Direção: a direção da relação entre duas métricas. (não há efeito na lista de incidentes relacionados agora)
- Outra métrica de & de feed de dados: o feed de dados e a métrica para se conectar com a métrica atual


## <a name="next-steps"></a>Próximas etapas 

- [Ajustar a detecção de anomalias usando os comentários](anomaly-feedback.md)
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
