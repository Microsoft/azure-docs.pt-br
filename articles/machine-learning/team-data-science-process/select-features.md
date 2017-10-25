---
title: "Seleção de recursos no Processo de Ciência de Dados de Equipe | Microsoft Docs"
description: "Explica a finalidade da seleção de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados do aprendizado de máquina."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: 1d45c8beb78101b04b9e998e3e202477cb5ce6c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de recursos no TDSP (Processo de Ciência de Dados de Equipe)
Este artigo explica as finalidades da seleção de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados de aprendizado de máquina. Esses exemplos foram extraídos do Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A engenharia e a seleção de recursos faz parte do Processo de Ciência de Dados de Equipe (TDSP) descrito em [O que é o Processo de Ciência de Dados de Equipe?](overview.md). A engenharia e a seleção de recursos fazem parte da etapa **Desenvolver recursos** do TDSP.

* **engenharia de recursos**: esse processo tenta criar outros recursos relevantes por meio dos recursos brutos existentes nos dados e aumentar a capacidade de previsão do algoritmo de aprendizado.
* **seleção de recursos**: este processo seleciona o subconjunto principal de recursos de dados originais para tentar reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e, em seguida, a etapa de **seleção de recursos** é feita para eliminar recursos redundantes, altamente correlacionados ou irrelevantes.

## <a name="filtering-features-from-your-data---feature-selection"></a>Filtrando recursos de seus dados - Seleção de recursos
A seleção de recursos é um processo que geralmente é aplicado à criação de conjuntos de dados de treinamento para tarefas de modelagem de previsão, como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto de recursos no conjunto de dados original, reduzindo suas dimensões usando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Esse subconjunto de recursos representa, em seguida, os únicos recursos a serem incluídos para treinar o modelo. A seleção de recursos atende a duas finalidades principais.

* Primeiro, a seleção de recursos frequentemente aumenta a precisão de classificação, eliminando recursos irrelevantes, redundantes ou altamente correlacionados.
* Em segundo lugar, ela reduz o número de recursos, o que torna o processo de treinamento de modelo mais eficiente. Isso é particularmente importante para tecnologias que sejam dispendiosas de treinar, como máquinas de vetor de suporte.

Embora a seleção de recursos busque reduzir o número de recursos no conjunto de dados usado para treinar o modelo, ela não costuma ser mencionada usando o termo "redução de dimensionalidade". Métodos de seleção de recursos extraem um subconjunto dos recursos originais dos dados sem alterá-los.  Métodos de redução de dimensionalidade empregam recursos de engenharia que podem transformar os recursos originais e, assim, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem a Análise de Componente Principal, a análise de correlação canônica e a Decomposição de Valor Singular.

Entre outros, uma categoria amplamente aplicada de métodos de seleção de recursos em um contexto supervisionado é a "seleção de recursos baseada em filtros". Ao avaliar a correlação entre cada recurso e o atributo de destino, esses métodos se aplicam a uma medida estatística para atribuir uma pontuação a cada recurso. Em seguida, os recursos são classificados pela pontuação, o que pode ser usado para ajudar a definir o limiar para manter ou eliminar um recurso específico. Correlação de Pearson, informações mútuas e o teste de quadrados de Chi pessoa são exemplos das medidas estatísticas usadas nesses métodos.

No Estúdio do Azure Machine Learning, há módulos fornecidos para seleção de recursos. Conforme mostrado na figura a seguir, esses módulos incluem [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection] e [Análise Discriminante Linear de Fisher][fisher-linear-discriminant-analysis].

![Exemplo de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, o uso do módulo [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection]. Para fins de conveniência, continuamos usando o exemplo de mineração de texto descrito acima. Suponha que desejamos criar um modelo de regressão após a criação de um conjunto de 256 recursos por meio do módulo [Hash de Recursos][feature-hashing] e que a variável de resposta é "Col1" e representa as avaliações de um livro, que variam de 1 a 5. Ao definir o "Método de pontuação de recursos" como "Correlação de Pearson", a “Coluna de destino" como "Col1" e o "Número de recursos desejados" como 50. O módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection] produzirá um conjunto de dados que contém 50 recursos, em conjunto com o atributo de destino "Col1". A figura a seguir mostra o fluxo desse experimento e os parâmetros de entrada que acabamos de descrever.

![Exemplo de seleção de recursos](./media/select-features/feature-Selection1.png)

A figura a seguir mostra os conjuntos de dados resultantes. Cada recurso é pontuado com base na Correlação de Pearson entre ele mesmo e o atributo de destino "Col1". Os recursos com pontuação mais alta são mantidos.

![Exemplo de seleção de recursos](./media/select-features/feature-Selection2.png)

As pontuações dos recursos selecionados são mostradas na figura a seguir.

![Exemplo de seleção de recursos](./media/select-features/feature-Selection3.png)

Ao aplicar o módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection], 50 dos 256 recursos são selecionados, porque eles têm o maior número de recursos correlacionados à variável de destino "Col1", com base no método de pontuação "Correlação de Pearson".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de recursos são dois recursos de engenharia e seleção comuns que aumentam a eficiência do processo de treinamento, que tenta extrair as informações importantes contidas nos dados. Eles também melhoram a capacidade desses modelos de classificar os dados de entrada com precisão e prever resultados de interesse com mais robustez. Também é possível combinar seleção e engenharia de recursos para que o aprendizado seja mais tratável por computação. Ele faz isso aperfeiçoando e, em seguida, reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Matematicamente, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e preveem os resultados com êxito.

Observe que nem sempre é necessário realizar a engenharia ou a seleção de recursos. Essa necessidade depende dos dados que temos ou coletamos, do algoritmo que escolhemos e do objetivo do experimento.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

