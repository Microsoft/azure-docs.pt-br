---
title: Engenharia de recursos no Machine Learning – processo de ciência de dados de equipe
description: Saiba mais sobre a engenharia de recursos e a função dela no processo de melhoria de dados do aprendizado de máquina.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: b20a6744644678879fedf44e960854f558eb0f03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610430"
---
# <a name="feature-engineering-in-machine-learning"></a>Engenharia de recursos no Machine Learning

Neste artigo, você aprende sobre a engenharia de recursos e a função dela no aprimoramento de dados no aprendizado de máquina. Aprenda com exemplos ilustrativos desenhados de experimentos do [Azure Machine Learning Studio (clássico)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio). 

* **Engenharia de recursos**: o processo de criação de recursos de dados brutos para aumentar a potência preditiva do algoritmo de aprendizado. Recursos de engenharia devem capturar informações adicionais que não sejam facilmente aparentes no conjunto de recursos original.
* **Seleção de recursos**: o processo de seleção do subconjunto principal de recursos para reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e, em seguida, a **seleção de recursos** é feita para eliminar recursos redundantes, altamente correlacionados ou irrelevantes.

A engenharia e a seleção de recursos fazem parte do [estágio de modelagem](lifecycle-modeling.md) do TDSP (processo de ciência de dados de equipe). Para saber mais sobre o TDSP e o ciclo de vida de ciência de dados, confira [O que é o TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>O que é a engenharia de recursos?

Os dados de treinamento consistem em uma matriz composta por linhas e colunas. Cada linha na matriz é uma observação ou um registro. As colunas de cada linha são os recursos que descrevem cada registro. Os recursos especificados no design experimental devem caracterizar os padrões nos dados.

Embora muitos dos campos de dados brutos possam ser usados diretamente para treinar um modelo, muitas vezes é necessário criar recursos adicionais (projetados) para um conjunto de dados de treinamento avançado.

Recursos de engenharia que aprimoram o treinamento fornecem informações que diferenciam melhor os padrões nos dados. Mas esse processo tem algo de artístico. Decisões sensatas e produtivas frequentemente exigem alguma experiência na área.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Exemplo 1: Adicionar recursos temporais para um modelo de regressão

Vamos usar o experimento [Previsão de demanda de locações de bicicletas](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) no Azure Machine Learning Studio (clássico) para demonstrar como fazer a engenharia de recursos para uma tarefa de regressão. O objetivo do experimento é prever a demanda de locações de bicicletas dentro de um mês/dia/hora específica.

### <a name="bike-rental-dataset"></a>Conjunto de dados de locação de bicicletas

O [Conjunto de dados UCI de locação de bicicletas](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) baseia-se em dados reais de uma empresa de compartilhamento de bicicletas com sede nos Estados Unidos. Ele representa o número de locações de bicicletas em uma hora específica de um dia nos anos 2011 e 2012. Ele contém 17.379 linhas e 17 colunas.

O conjunto de recursos brutos contém condições climáticas (temperatura/umidade/velocidade do vento) e o tipo de dia (feriado/dia da semana). O campo a ser previsto é a contagem, que representa as locações de bicicleta em uma hora específica. A contagem varia de 1 a 977.

### <a name="create-a-feature-engineering-experiment"></a>Criar um experimento de engenharia de recursos

Com o objetivo de construir recursos efetivos nos dados de treinamento, quatro modelos de regressão são criados usando o mesmo algoritmo, mas com quatro conjuntos de dados de treinamento diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada brutos, mas com um número crescente de características definido. Os recursos são agrupados em quatro categorias:

1. A = características de clima + feriado + dia da semana + final de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das últimas 12 horas
3. C = número de bicicletas que foram alugadas em cada um dos últimos 12 dias, na mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das últimas 12 semanas, na mesma hora e mesmo dia

Além do conjunto de recursos A, que já existe nos dados brutos originais, três outros conjuntos de recursos são criados por meio do processo de engenharia de recursos. O conjunto de recursos B captura a demanda recente pelas bicicletas. O conjunto de recursos C captura a demanda por bicicletas em uma hora específica. O conjunto de recursos D captura a demanda por bicicletas em uma hora específica de um dia específico da semana. Os quatro conjuntos de dados de treinamento incluem os conjuntos de recursos A, A + B, A + B + C e A + B + C + D, respectivamente.

### <a name="feature-engineering-using-studio-classic"></a>Engenharia de recursos usando o Studio (clássico)

No experimento do Studio (clássico), esses quatro conjuntos de dados de treinamento são formados por meio de quatro ramificações do conjunto de dados de entrada pré-processado. Exceto pela ramificação mais à esquerda, cada uma dessas ramificações contém um módulo [Executar Script R](/azure/machine-learning/studio-module-reference/execute-r-script), no qual os recursos derivados (conjuntos de recursos B, C e D) são construídos e acrescentados ao conjunto de dados importado.

A figura a seguir demonstra o script R sendo usado para criar o conjunto de recursos B na segunda ramificação à esquerda.

![criar recursos](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Resultados

Uma comparação dos resultados de desempenho dos quatro modelos é resumida na tabela a seguir: 

![comparação de resultados](./media/create-features/result1.png)

Os melhores resultados são mostrados pelos recursos A + B + C. A taxa de erro diminui quando o conjunto de recursos adicional é incluído nos dados de treinamento. Isso confirma a suposição de que o conjunto de recursos B, C fornece informações adicionais relevantes para a tarefa de regressão. Mas adicionar o recurso D não parece fornecer qualquer redução adicional da taxa de erro.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Exemplo 2: Criar recursos para mineração de texto

A engenharia de recursos é amplamente aplicada a tarefas relacionadas à mineração de texto, como classificação de documentos e análise de sentimento. Já que trechos individuais do conteúdo de texto bruto normalmente funcionam como dados de entrada, o processo de engenharia de recurso é necessário para criar os recursos que envolvem frequências de palavra/expressão.

### <a name="feature-hashing"></a>Hash de recursos

Para realizar essa tarefa, uma técnica chamada [hash de recursos](/azure/machine-learning/studio-module-reference/feature-hashing) é aplicada para transformar de maneira eficiente recursos de texto arbitrários em índices. Em vez de associar cada recurso de texto (palavras/expressões) a um índice em particular, este método aplica uma função de hash aos recursos e usando seus valores de hash como índices diretamente.

No Studio (clássico), há um módulo [Hash de Recursos](/azure/machine-learning/studio-module-reference/feature-hashing) que cria esses recursos de palavra/expressão de maneira conveniente. A figura a seguir mostra um exemplo de uso deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro, que varia de 1 a 5, e o conteúdo real da resenha. O objetivo desse módulo é recuperar um monte de novos recursos que mostram a frequência de ocorrência das palavras/expressões correspondentes dentro de uma resenha de livro. Para usar este módulo, conclua as seguintes etapas:

* Primeiro, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, defina "Bitsize de hashing" como 8, o que significa que 2^8 = 256 recursos serão criados. A palavra/expressão em todo o texto será colocada em hash, em 256 índices. O parâmetro "Bitsize de hashing" varia de 1 a 31. As palavras/expressões têm menos probabilidades de serem colocadas em hash no mesmo índice se a configuração for para um número maior.
* Em terceiro lugar, defina o parâmetro “N-grams” como 2. Esse valor obtém a frequência de ocorrência de unigrams (um recurso para cada palavra) e bigrams (um recurso para cada par de palavras adjacentes) do texto de entrada. O parâmetro "N-grams" vai de 0 a 10, o que indica o número máximo de palavras sequenciais a serem incluídas em um recurso.  

![Módulo "Hash de Recursos"](./media/create-features/feature-Hashing1.png)

A figura a seguir mostra como ficariam esses novos recursos.

![Exemplo de "Hash de Recursos"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
Os recursos de engenharia e selecionados aumentam a eficiência do processo de treinamento, que tenta extrair as informações importantes contidas nos dados. Eles também melhoram a capacidade desses modelos de classificar os dados de entrada com precisão e prever resultados de interesse com mais robustez.

Também é possível combinar seleção e engenharia de recursos para que o aprendizado seja mais tratável por computação. Ele faz isso aperfeiçoando e, em seguida, reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Matematicamente, os recursos selecionados são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e preveem os resultados com êxito.

Nem sempre é necessário realizar a engenharia ou a seleção de recursos. Isso depende dos dados, do algoritmo selecionado e do objetivo do experimento.

## <a name="next-steps"></a>Próximas etapas

Para criar recursos para dados em ambientes específicos, consulte os artigos a seguir:

* [Criar recursos de dados no SQL Server](create-features-sql-server.md)
* [Criar recursos de dados em um cluster Hadoop usando as consultas do Hive](create-features-hive.md)