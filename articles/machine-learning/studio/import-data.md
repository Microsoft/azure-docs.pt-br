---
title: Importar dados para o Machine Learning Studio | Microsoft Docs
description: "Como importar os dados para o Azure Machine Learning Studio de diferentes fontes de dados. Saiba quais tipos e formatos de dados têm suporte."
keywords: importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: garye;bradsev
ms.openlocfilehash: 98a8fa15922ef100a7f8cadd92fb7ffef0d3bcde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importar os dados de treinamento para o Azure Machine Learning Studio de diferentes fontes de dados
Para usar seus próprios dados no Machine Learning Studio para desenvolver e treinar uma solução de análise preditiva, você pode: 

* carregar dados de um **arquivo local** antes do tempo por meio do disco rígido para criar um módulo de conjunto de dados no espaço de trabalho
* acessar dados de uma das várias **fontes de dados online** enquanto o teste é executado usando o módulo [Importar Dados][import-data] 
* usar dados de outro **teste** do Azure Machine Learning salvo como um conjunto de dados
* usar dados de um **banco de dados do SQL Server** local

Cada uma dessas opções são descritas em um dos tópicos no menu abaixo. Esse tópicos mostram como importar dados de várias fontes de dados para usar no Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Há diversos conjuntos de dados de exemplo disponíveis no Machine Learning Studio que você pode usar para treinamento de dados. Para obter informações, consulte [Usar os conjuntos de dados de amostra no Azure Machine Learning Studio](use-sample-datasets.md).
> 
> 

Este tópico introdutório também mostra como preparar dados para uso no Machine Learning Studio e descreve quais formatos e tipos de dados tem suporte. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Preparar dados para uso no Azure Machine Learning Studio.
O Machine Learning Studio foi criado para trabalhar com dados tabulares ou retangulares, como dados de texto delimitados ou dados estruturados de um banco de dados, embora em algumas circunstâncias seja possível usar dados não retangulares.

É melhor se os dados estiverem relativamente limpos. Ou seja, você deve cuidar de problemas como cadeias de caracteres sem aspas antes de carregar os dados para seu experimento.

No entanto, há módulos disponíveis no Machine Learning Studio que permitem fazer alguma manipulação de dados dentro do teste. Dependendo dos algoritmos de aprendizado de máquina que for usar, talvez seja necessário decidir como você lidará com problemas estruturais dos dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Veja a seção **Transformação de Dados** da paleta de módulos para ver os módulos que executam essas funções.

A qualquer momento do teste, é possível exibir ou baixar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, pode haver opções de download diferentes disponíveis ou a possibilidade de visualizar os dados no seu navegador da Web no Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Formatos e tipos de dados compatíveis
Você pode importar vários tipos de dados para seu experimento, dependendo de qual mecanismo usar para importar os dados e de onde eles estão vindo:

* Texto sem formatação (.txt)
* Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo do Excel
* Tabela do Azure
* Tabela do Hive
* Tabela de Banco de Dados SQL
* Valores de OData
* Dados SVMLight (.svmlight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
* Attribute Relation File Format (ARFF) (.arff) (consulte a [definição de ARFF](http://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
* Arquivo zip (.zip)
* Arquivo de espaço de trabalho ou objeto R (.RData)

Se você importar dados em um formato como ARFF, que inclui metadados, o Machine Learning Studio usa esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar dados em um formato como TSV ou CSV, que não incluem esses metadados, o Machine Learning Studio infere o tipo de dados de cada coluna por amostragem dos dados. Se os dados também não tiverem os cabeçalhos das colunas, o Machine Learning Studio fornece nomes padrão.

É possível especificar explicitamente ou alterar os cabeçalhos e tipos de dados das colunas usando [Editar Metadados][edit-metadata].

Os **tipos de dados** a seguir são reconhecidos pelo Machine Learning Studio:

* Cadeia de caracteres
* Número inteiro
* Duplo
* Booliano
* DateTime
* TimeSpan

O Machine Learning Studio usa um tipo de dados interno chamado ***Tabela de Dados*** para transmitir dados entre módulos. É possível converter explicitamente os dados para o formato de tabela de dados usando o módulo [Converter para Conjunto de Dados][convert-to-dataset].

Qualquer módulo que aceitar formatos diferentes do Data Table converterá os dados para Data Table silenciosamente antes de transmiti-los para o módulo seguinte.

Se necessário, você pode converter o formato Data Table de volta para o formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão.
Veja a seção **Conversões de Formato de Dados** da paleta de módulos para ver os módulos que executam essas funções.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
