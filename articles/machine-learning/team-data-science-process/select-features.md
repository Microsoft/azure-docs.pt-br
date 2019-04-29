---
title: Seleção de recursos no Processo de Ciência de dados de Equipe
description: Explica a finalidade da seleção de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados do aprendizado de máquina.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a74f2c21746deb16372174d4a769f9abb825a1cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809558"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Seleção de recursos no TDSP (Processo de Ciência de Dados de Equipe)
Este artigo explica as finalidades da seleção de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados de aprendizado de máquina. Esses exemplos foram extraídos do Azure Machine Learning Studio. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A engenharia e a seleção de recursos faz parte do Processo de Ciência de Dados de Equipe (TDSP) descrito no artigo [O que é o Processo de Ciência de Dados de Equipe?](overview.md). A engenharia e a seleção de recursos fazem parte da etapa **Desenvolver recursos** do TDSP.

* **engenharia de recursos**: Esse processo tenta criar recursos relevantes adicionais a partir dos recursos brutos existentes nos dados e aumentar a capacidade preditiva do algoritmo de aprendizado.
* **seleção de recursos**: Esse processo seleciona o subconjunto de chaves dos recursos de dados originais na tentativa de reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e, em seguida, a etapa de **seleção de recursos** é feita para eliminar recursos redundantes, altamente correlacionados ou irrelevantes.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrando recursos de seus dados - seleção de recursos
A seleção de recursos é um processo que geralmente é aplicado à criação de conjuntos de dados de treinamento para tarefas de modelagem de previsão, como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto de recursos no conjunto de dados original, reduzindo suas dimensões usando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Esse subconjunto de recursos é usado para treinar o modelo. A seleção de recursos atende a duas finalidades principais.

* Primeiro, a seleção de recursos frequentemente aumenta a precisão de classificação, eliminando recursos irrelevantes, redundantes ou altamente correlacionados.
* Em segundo lugar, ela reduz o número de recursos, o que torna o processo de treinamento de modelo mais eficiente. A eficiência é particularmente importante para tecnologias que sejam dispendiosas de treinar, como máquinas de vetor de suporte.

Embora a seleção de recursos busque reduzir o número de recursos no conjunto de dados usado para treinar o modelo, ela não é referida pelo termo "redução de dimensionalidade". Métodos de seleção de recursos extraem um subconjunto dos recursos originais dos dados sem alterá-los.  Métodos de redução de dimensionalidade empregam recursos de engenharia que podem transformar os recursos originais e, assim, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem a Análise de Componente Principal, a análise de correlação canônica e a Decomposição de Valor Singular.

Entre outros, uma categoria amplamente aplicada de métodos de seleção de recursos em um contexto supervisionado é a "seleção de recursos baseada em filtro". Ao avaliar a correlação entre cada recurso e o atributo de destino, esses métodos se aplicam a uma medida estatística para atribuir uma pontuação a cada recurso. Em seguida, os recursos são classificados pela pontuação, o que pode ser usado para ajudar a definir o limiar para manter ou eliminar um recurso específico. Correlação de Pearson, informações mútuas e o teste de quadrados de Chi pessoa são exemplos das medidas estatísticas usadas nesses métodos.

No Estúdio do Azure Machine Learning, há módulos fornecidos para seleção de recursos. Conforme mostrado na figura a seguir, esses módulos incluem [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection] e [Análise Discriminante Linear de Fisher][fisher-linear-discriminant-analysis].

![Módulos de seleção de recursos](./media/select-features/feature-Selection.png)

Considere, por exemplo, o uso do módulo [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection]. Para sua conveniência, continue usando o exemplo de mineração de texto. Suponha que você deseja criar um modelo de regressão depois que um conjunto de 256 recursos tenha sido criado através do módulo [Hash de Recursos][feature-hashing] e que a variável de resposta é o "Col1" que contém classificações de revisão de livros variando de 1 a 5. Ao definir o "Método de pontuação de recursos" como "Correlação de Pearson", a “Coluna de destino" como "Col1" e o "Número de recursos desejados" como 50. O módulo [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection] produz um conjunto de dados que contém 50 recursos, em conjunto com o atributo de destino "Col1". A figura a seguir mostra o fluxo desse experimento e os parâmetros de entrada:

![Propriedades do módulo de Seleção de Recursos com Base em Filtro](./media/select-features/feature-Selection1.png)

A figura a seguir mostra os conjuntos de dados resultantes:

![Conjunto de dados resultante para o módulo de Seleção de recursos com Base em Filtro](./media/select-features/feature-Selection2.png)

Cada recurso é pontuado com base na Correlação de Pearson entre ele mesmo e o atributo de destino "Col1". Os recursos com pontuação mais alta são mantidos.

As pontuações dos recursos selecionados são mostradas na figura a seguir:

![Pontuações para o módulo de Seleção de Recursos com Base em Filtro](./media/select-features/feature-Selection3.png)

Ao aplicar o módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection], 50 dos 256 recursos são selecionados, porque eles têm o maior número de recursos correlacionados à variável de destino "Col1", com base no método de pontuação "Correlação de Pearson".

## <a name="conclusion"></a>Conclusão
A engenharia de recursos e a seleção de recursos são dois recursos de engenharia e seleção comuns que aumentam a eficiência do processo de treinamento, que tenta extrair as informações importantes contidas nos dados. Eles também melhoram a capacidade desses modelos de classificar os dados de entrada com precisão e prever resultados de interesse com mais robustez. Também é possível combinar seleção e engenharia de recursos para que o aprendizado seja mais tratável por computação. Ele faz isso aperfeiçoando e, em seguida, reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Matematicamente, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e preveem os resultados com êxito.

Nem sempre é necessário realizar a engenharia ou a seleção de recursos. Essa necessidade depende dos dados coletados, do algoritmo selecionado e do objetivo do experimento.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

