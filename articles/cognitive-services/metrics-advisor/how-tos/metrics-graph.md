---
title: Grafo de métricas do Orientador de métricas
titleSuffix: Azure Cognitive Services
description: Como configurar o grafo de métricas e Visualizar anomalias relacionadas em seus dados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043146"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Como: criar um grafo de métricas para analisar métricas relacionadas

Cada métrica no Metrics Advisor é monitorada separadamente por um modelo que aprende com dados históricos para prever tendências futuras. Cada métrica tem um modelo separado que é aplicado a ela. No entanto, em alguns casos, várias métricas podem se relacionar entre si, e as anomalias precisam ser analisadas em várias métricas. O **grafo de métricas** ajuda com isso. 

Por exemplo, se você tiver fluxos diferentes de telemetria em métricas separadas, o revisor de métricas os monitorará separadamente. Se as anomalias em uma métrica causarem anomalias em outros, encontrar essas relações e a causa raiz em seus dados poderá ser útil ao lidar com incidentes. O grafo de métricas permite que você crie um grafo de topologia visual de anomalias encontradas. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Selecione uma métrica para colocar o primeiro nó no grafo

Clique na guia **grafo de métricas** na barra de navegação. A primeira etapa na criação de um grafo de métricas é colocar um nó no grafo. Selecione um feed de dados e uma métrica na parte superior da página. Um nó aparecerá no painel inferior. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Selecionar métrica":::

## <a name="add-a-noderelation-on-existing-node"></a>Adicionar um nó/relação no nó existente

Em seguida, você precisa adicionar outro nó e especificar uma relação com um nó (s) existente (ões). Selecione um nó existente e clique com o botão direito do mouse nele. Um menu de contexto será exibido com várias opções. 

Clique em **Adicionar relação**, e você poderá escolher outra métrica e especificar o tipo de relação entre os dois nós. Você também pode aplicar filtros de dimensão específicos. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Adicionar um nó e uma relação":::

Depois de repetir as etapas acima, você terá um grafo de métricas que descreve as relações entre todas as métricas relacionadas.
**Dica sobre as cores do nó**
> [!TIP]
> - Quando você seleciona um filtro de métrica e dimensão, todos os nós com a mesma métrica e o mesmo filtro de dimensão no grafo serão coloridos como **<font color=blue>azul</font>**.
> - Os nós não selecionados que representam uma métrica no grafo ficarão **<font color=green>verdes</font>** em cores.
> - Se houver uma anomalia observada na métrica atual, o nó ficará **<font color=red>vermelho</font>**.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Exibir status de anomalias de métricas relacionadas no Hub de incidentes

Quando o grafo de métricas é criado, sempre que uma anomalia é detectada nas métricas no grafo, você poderá exibir os status de anomalias relacionados e obter uma exibição de alto nível do incidente. 

Clique em um incidente dentro do grafo e role para baixo até a **análise de métrica cruzada**, abaixo das informações de diagnóstico.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Exibir métricas e anomalias relacionadas":::

## <a name="next-steps"></a>Próximas etapas

- [Ajustar a detecção de anomalias usando os comentários](anomaly-feedback.md)
- [Diagnosticar um incidente](diagnose-incident.md).
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
