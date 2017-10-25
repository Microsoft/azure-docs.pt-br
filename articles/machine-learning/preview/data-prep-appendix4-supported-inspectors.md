---
title: "Inspetores com suporte disponíveis com a preparação dos dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de inspetores disponíveis para a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-inspectors-for-this-preview"></a>Inspetores com suporte para essa versão prévia
Este documento descreve o conjunto de inspetores disponíveis nesta versão prévia.

## <a name="the-halo-effect"></a>O efeito de halo 
Alguns inspetores dão suporte ao efeito de halo, esse efeito usa duas cores diferentes para mostrar imediatamente de forma visual a alteração de uma transformação. O cinza representa o valor anterior antes da transformação mais recente, o azul mostra o valor atual. Esse efeito pode ser habilitado/desabilitado nas opções.

## <a name="graphical-filtering"></a>Filtragem de gráfico 
Alguns dos inspetores dão suporte à filtragem de dados usando o inspetor como um editor. Fazer isso envolve a seleção de elementos gráficos e, em seguida, o uso da barra de tarefas na parte superior direita da janela do Inspetor para filtrar para dentro e para fora os valores selecionados. 

## <a name="column-statistics"></a>Estatísticas da coluna
Para colunas numéricas, esse inspetor fornece uma variedade de diferentes estatísticas sobre a coluna. As estatísticas incluem:
- Mínimo
- Quartil inferior
- Mediana
- Quartil superior
- Máximo
- Média
- Desvio padrão


### <a name="options"></a>Opções 
- Nenhum

## <a name="histogram"></a>Histograma 
Calcula e exibe um histograma de uma única coluna numérica. O número padrão de buckets é calculado usando a Regra de Scott, a regra pode ser substituída pelas Opções.
O inspetor dá suporte ao efeito de halo.


### <a name="options"></a>Opções
- Número Mínimo de Buckets (aplica-se mesmo quando o bucketing padrão está marcado)
- Número Padrão de Buckets (Regra de Scott) 
- Mostrar halo
- Sobreposição de Gráfico de Densidade de Kernel (Kernel Gaussiano) 


### <a name="actions"></a>Ações
Esse inspetor dá suporte à filtragem por meio de buckets, buckets únicos ou de seleção múltipla e aplica os filtros como descrito anteriormente.

## <a name="value-counts"></a>Contagens de valor
Esse inspetor apresenta uma tabela de frequência de valores para a coluna selecionada no momento. A exibição padrão é para as principais 6, o limite pode ser alterado para ser qualquer número ou para contar de baixo e não de cima. O inspetor dá suporte ao efeito de halo.

### <a name="options"></a>Opções 
- Número de valores principais
- Decrescente
- Incluir valores nulos/erro
- Mostrar halo


### <a name="actions"></a>Ações 
Esse inspetor dá suporte à filtragem por meio de barras, barras únicas ou de seleção múltipla e aplica os filtros como descrito anteriormente.

## <a name="box-plot"></a>Gráfico de caixa 
Um gráfico de caixa de uma coluna numérica

### <a name="options"></a>Opções 
- Agrupar Por Coluna

## <a name="scatter-plot"></a>Gráfico de Dispersão
Um gráfico de dispersão de duas colunas numéricas, os dados são reduzidos por questões de desempenho, o tamanho da amostra pode ser substituído nas opções.

### <a name="options"></a>Opções  
- Coluna do eixo X
- Coluna do eixo Y
- Tamanho da Amostra
- Agrupar por Coluna


## <a name="time-series"></a>Série temporal
Um gráfico de linha com reconhecimento de tempo no eixo X

### <a name="options"></a>Opções
- Coluna de Data
- Coluna Numérica
- Tamanho da Amostra


### <a name="actions"></a>Ações
Esse inspetor dá suporte à filtragem por meio de um método de Clicar e Arrastar a Seleção para selecionar um intervalo no gráfico. Depois de concluir a seleção, aplique os filtros conforme descritos anteriormente.


## <a name="map"></a>Mapa 
Um mapa com pontos traçados assumindo que a latitude e a longitude foram especificadas. A latitude deve ser selecionada primeiro.

### <a name="options"></a>Opções
- Coluna de Latitude
- Coluna de Longitude
- Clustering em
- Agrupar por Coluna


### <a name="actions"></a>Ações
Esse inspetor dá suporte à filtragem por meio da seleção de ponto do mapa. Pressione a tecla Control e clique e arraste o mouse para formar um quadrado em torno dos pontos. Em seguida, aplique os filtros conforme descrito anteriormente.
É possível dimensionar rapidamente o mapa para mostrar todos os pontos possíveis e não mais pressionando **E** no lado esquerdo do mapa.
