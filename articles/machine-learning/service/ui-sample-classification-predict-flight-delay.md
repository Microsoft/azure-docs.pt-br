---
title: 'Classificação: Prever atrasos nos voos'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como criar um modelo de machine learning para prever atrasos de voo usando a interface visual do arrastar e soltar e o código R personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607631"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Exemplo 6 - classificação: Prever atrasos de voo usando R

Esse teste usa históricos de voo e dados meteorológicos para prever se um voo comercial atrasará será atrasado por mais de 15 minutos.

Esse problema pode ser abordado como um problema de classificação, prevendo duas classes – atrasadas, ou no tempo. Para criar um classificador, esse modelo usando um grande número de exemplos de dados históricos de voo.

Aqui está o grafo de experimento final:

[![Grafo do experimento](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **abrir** botão para o experimento de exemplo 6:

    ![Abra o teste](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Obter os dados

Esse teste usa o **dados de atrasos de voo** conjunto de dados. Parte da coleta de dados TranStats dos Estados Unidos Departamento de transportes. O conjunto de dados contém informações de atraso de voo de abril a outubro de 2013. Antes de carregar os dados para a interface visual, ele ter sido previamente processado da seguinte maneira:

* Filtrado para incluir os 70 aeroportos mais movimentados no território continental dos Estados Unidos.
* Para voos, novamente rotulados como atrasados por mais de 15 minutos foi cancelada.
* Voos desviados filtrados.
* 14 colunas selecionadas.

Para complementar os dados de voo, o **conjunto de dados de clima** é usado. Os dados de clima contém por hora observações climáticas terrestres da NOAA e representa as observações de estações meteorológica de aeroportos, que abrangem o mesmo período de tempo de abril a outubro de 2013. Antes de carregar a interface visual do AM do Azure, ele ter sido previamente processado da seguinte maneira:

* IDs da estação meteorológica foram mapeadas para as IDs do aeroporto correspondentes.
* As estações meteorológicas não associadas os 70 aeroportos mais movimentados foram removidas.
* A coluna de data foi dividida em colunas separadas: Ano, mês e dia.
* 26 colunas selecionadas.

## <a name="pre-process-the-data"></a>Pré-processar os dados

Um conjunto de dados geralmente requer algum pré-processamento antes que eles podem ser analisados.

![processo de dados](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dados de voo

As colunas **operadora**, **OriginAirportID**, e **DestAirportID** são salvos como inteiros. No entanto, eles são atributos categóricos, use o **editar metadados** módulo convertê-las em categórica.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Em seguida, use o **selecionar colunas** no módulo do conjunto de dados para excluir as colunas de conjunto de dados que são leakers de destino possíveis: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year**. 

Para associar os registros de voo com os registros de clima por hora, use a hora de partida agendados como uma das chaves de junção. Para fazer a junção, a coluna CSRDepTime deve ser arredondada para baixo até a hora mais próxima, que é feita na **Executar Script R** módulo. 

### <a name="weather-data"></a>Dados de clima

As colunas que têm uma grande proporção de valores ausentes são excluídas usando o **colunas do projeto** módulo. Essas colunas incluem todas as colunas com valor de cadeia de caracteres: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, and **StationPressure**.

O **limpar dados ausentes** módulo é então aplicado para as colunas restantes para remover linhas com dados ausentes.

Tempos de observação de clima são arredondados para a hora completa mais próxima. Horários de voo programado atrasará e os horários de observação de clima são arredondados em direções opostas para garantir que o modelo usa apenas clima antes da hora de voo. 

Uma vez que os dados de clima são relatados na hora local, as diferenças de fuso horário são tratadas de subtraindo-se as colunas de fuso horário de partida agendados tempo e o tempo de observação de clima. Essas operações são executadas usando o **Executar Script R** módulo.

### <a name="joining-datasets"></a>Unindo conjuntos de dados

Registros de voo são Unidos com dados de clima de origem do voo (**OriginAirportID**) usando o **unir dados** módulo.

 ![ingressar em voo e clima por origem](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Os registros de voo são associados com os dados de clima usando o destino do voo (**DestAirportID**).

 ![Junte-se de voo e clima por destino](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparando-se exemplos de teste e treinamento

O **dividir dados** módulo divide os dados em abril por meio de registros de setembro para treinamento e os registros de outubro para teste.

 ![Dividir o treinamento e dados de teste](media/ui-sample-classification-predict-flight-delay/split.png)

Colunas de fuso horário, mês e ano são removidas do conjunto de dados de treinamento usando o módulo selecionar colunas.

## <a name="define-features"></a>definir recursos

No aprendizado de máquina, os recursos são propriedades individuais mensuráveis de algo que você está interessado. Localizar um conjunto robusto de recursos requer experimentação e conhecimento do domínio. Alguns recursos são melhores para prever o destino do que outros. Além disso, alguns recursos podem ter uma forte correlação com outros recursos e não adicionará novas informações para o modelo. Esses recursos podem ser removidos.

Para criar um modelo, você pode usar todos os recursos disponíveis ou selecionar um subconjunto dos recursos.

## <a name="choose-and-apply-a-learning-algorithm"></a>escolher e aplicar um algoritmo de aprendizado

Criar um modelo usando o **Regressão logística de duas classes** módulo e treiná-lo no conjunto de dados de treinamento. 

O resultado do **modelo de treinamento** módulo é um modelo de classificação treinado que pode ser usado para pontuar novas amostras para fazer previsões. Use o teste definido para gerar pontuações dos modelos treinados. Em seguida, use o **avaliar modelo** módulo para analisar e comparar a qualidade dos modelos.

Depois de executar o teste, você pode exibir a saída a **modelo de pontuação** módulo clicando na porta de saída e selecionando **visualizar**. A saída inclui os rótulos pontuados e as probabilidades para os rótulos.

Por fim, para testar a qualidade dos resultados, adicione a **avaliar modelo** módulo para o teste de tela e conecte a porta de entrada esquerda à saída do módulo modelo de pontuação. Execute o experimento e exiba a saída do **modelo de avaliação** módulo, clicando na porta de saída e selecionando **visualizar**.

## <a name="evaluate"></a>Avaliar
O modelo de regressão logística tem AUC de 0.631 no teste definido.

 ![avaliar](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Próximas etapas

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1: regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2: regressão: Comparar algoritmos para previsão de preço de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3: classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4: classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5: classificação: Prever a variação](ui-sample-classification-predict-churn.md)
