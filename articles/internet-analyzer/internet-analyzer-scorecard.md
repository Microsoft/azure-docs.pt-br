---
title: Interpretando o Scorecard | Microsoft Docs
description: Saiba como interpretar seu Scorecard. A guia Scorecard contém os resultados agregados e analisados de seus testes.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91330206"
---
# <a name="interpreting-your-scorecard"></a>Interpretar seu scorecard

A guia Scorecard contém os resultados agregados e analisados de seus testes. Cada teste tem seus próprios scorecards. Os scorecards fornecem resumos rápidos e significativos dos resultados da medição para fornecer resultados controlados por dados para seus requisitos de rede. O Internet Analyzer cuida da análise, permitindo que você se concentre na decisão.

A guia Scorecard pode ser encontrada no menu de recursos do Internet Analyzer. 


## <a name="filters"></a>Filtros

* ***Teste:*** Selecione o teste para o qual você gostaria de exibir os resultados para-cada teste tem seu próprio Scorecard. Os dados de teste aparecerão uma vez que há dados suficientes para concluir a análise – na maioria dos casos, isso deve ser dentro de 24 horas. 
* ***Período de tempo & data de término:*** Três scorecards são gerados diariamente – cada Scorecard reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Use o filtro "data de término" para selecionar o último dia do período de tempo que você deseja ver. 
* ***País:*** Para cada país que você tem usuários finais, um scorecard é gerado. O filtro global contém todos os usuários finais.

## <a name="measurement-count"></a>Contagem de medidas

O número de medições afeta a confiança da análise. Quanto maior a contagem, mais preciso será o resultado. No mínimo, os testes devem visar um mínimo de 100 medidas por ponto de extremidade por dia. Se as contagens de medidas forem muito baixas, configure o cliente JavaScript para executar com mais frequência em seu aplicativo. As contagens de medidas para pontos de extremidade A e B devem ser muito semelhantes, embora as pequenas diferenças sejam esperadas e OK. No caso de grandes diferenças, os resultados não devem ser confiáveis.

## <a name="percentiles"></a>Percentis

Latência, medida em milissegundos, é uma métrica popular para medir a velocidade entre uma origem e um destino na Internet. Os dados de latência não são normalmente distribuídos (ou seja, não seguem uma "curva de sino") porque há uma "longa cauda" de valores de latência grandes que distorcem os resultados ao usar estatísticas como a média aritmética. Como alternativa, os percentuais fornecem uma maneira de "distribuição livre" para analisar dados. Por exemplo, a mediana ou a 50 º percentil resume o meio da distribuição-metade dos valores estão acima dela e metade estão abaixo dela. Um valor de 75th percentil significa que ele é maior que 75% de todos os valores na distribuição. O Internet Analyzer refere-se a percentils na forma abreviada como P50, p75 e P95.

Os percentuais do Internet Analyzer são _métricas de exemplo_. Isso está em contraste com a _métrica true Population_. Por exemplo, a latência mediana real da população diária entre os alunos na Universidade do Sul da Califórnia e a Microsoft é o valor médio de latência de todas as solicitações durante esse dia. Na prática, a medição do valor de todas as solicitações é impraticável, portanto, supomos que um exemplo razoavelmente grande é representativo da população real.

Para fins de análise, p50 (mediana), é útil como um valor esperado para uma distribuição de latência. Percentuais mais altos, como P95, são úteis para identificar como a alta latência está nos piores casos. Se você estiver interessado em entender a latência do cliente em geral, o P50 é a métrica correta para se concentrar. Se você estiver preocupado com a compreensão do desempenho para os clientes de pior desempenho, P95 deve ser o foco. P75 é um equilíbrio entre esses dois.


## <a name="deltas"></a>Deltas

Um Delta é a diferença nos valores de métrica para os pontos de extremidade A e B. os deltas são calculados para mostrar o benefício de B acima de um. valores positivos indicam que B executou melhor do que A, enquanto valores negativos indicam que o desempenho do B é pior. Os deltas podem ser absolutos (por exemplo, 10 milissegundos) ou relativos (5%).

## <a name="confidence-interval"></a>intervalo de confiança 

Os intervalos de confiança (CI) são um intervalo de valores que têm uma probabilidade de conter a métrica da população, como mediana, p75 ou Average. Seguimos a Convenção de estatística comum de usar o 95% CI.

Para o Internet Analyzer, um intervalo de confiança estreito é bom porque ele mostra que a métrica de exemplo é provavelmente muito próxima da métrica de população real. Um intervalo de confiança largo significa menos certeza de que nossa métrica de exemplo reflete a métrica de população verdadeira. A melhor maneira de melhorar o CI é aumentar as contagens de medidas.

## <a name="time-series"></a>Série temporal 

Uma série temporal mostra como uma métrica muda ao longo do tempo. Na Internet, há muitos fatores temporais que afetam o desempenho, como períodos de tráfego de pico, diferenças de população de fim de semana e feriados.


## <a name="next-steps"></a>Próximas etapas

Para saber mais, Confira nossa [visão geral do Internet Analyzer](internet-analyzer-overview.md).
