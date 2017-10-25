---
title: "Recurso de engenharia em ciência de dados | Microsoft Docs"
description: "Explica a finalidade da seleção de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados do aprendizado de máquina."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: c0933fec67bbcfc2a8c450c1e2472440cf38f338
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="feature-engineering-in-data-science"></a>Engenharia de recursos em ciência de dados
Este tópico explica a finalidade da criação de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados do aprendizado de máquina. Os exemplos usados para ilustrar esse processo são extraídos do Azure Machine Learning Studio. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Este **menu** leva você até os tópicos que descrevem como criar recursos para dados em vários ambientes. Essa tarefa é uma etapa no [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

A engenharia de recurso tenta aumentar a capacidade de previsão dos algoritmos de aprendizado criando recursos de dados brutos que facilitam o processo de aprendizado de recursos. A engenharia e a seleção de recursos faz parte do processo de TDSP descrito em [O que é o ciclo de vida de Processo de Ciência de Dados de Equipe?](overview.md) A engenharia e a seleção de recursos fazem parte da etapa **Desenvolver recursos** do TDSP. 

* **engenharia de recursos**: esse processo tenta criar outros recursos relevantes com base nos recursos brutos existentes nos dados e aumentar a capacidade de previsão do algoritmo de aprendizado.
* **seleção de recursos**: este processo seleciona o subconjunto principal de recursos de dados originais para tentar reduzir a dimensionalidade do problema de treinamento.

Normalmente, a **engenharia de recursos** é aplicada primeiro para gerar recursos adicionais e a etapa de **seleção do recurso** é feita para eliminar recursos redundantes, altamente correlacionados ou irrelevantes.

Os dados de treinamento usados no aprendizado de máquina frequentemente podem ser aprimorados pela extração de recursos dos dados brutos coletados. Um exemplo de recurso de engenharia no contexto de aprender a classificar imagens de caracteres manuscritos é a criação de um mapa de densidade de bits construído por meio dos dados brutos de distribuição de bits. Este mapa pode ajudar a localizar as bordas dos caracteres da forma mais eficiente do que simplesmente usar a distribuição bruta diretamente.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="creating-features-from-your-data---feature-engineering"></a>Criando recursos por meio de seus dados - Engenharia de recursos
Os dados de treinamento consistem de uma matriz composta de exemplos (registros ou observações armazenadas em linhas), cada um deles dos quais tem um conjunto de recursos (variáveis ou campos armazenados em colunas). Espera-se que os recursos especificados no design experimental caracterizem os padrões nos dados. Embora muitos dos campos de dados brutos possam ser incluídos diretamente no conjunto de recursos selecionado para treinar um modelo, frequentemente é necessário que recursos adicionais (engenharia) precisem ser construídos por meio dos recursos nos dados brutos para gerar um conjunto de dados de treinamento aperfeiçoado.

Que tipos de recursos devem ser criados para aperfeiçoar o conjunto de dados ao treinar um modelo? Recursos de engenharia que aprimoram o treinamento fornecem informações que diferenciam melhor os padrões nos dados. Esperamos que os novos recursos forneçam informações adicionais que não são capturadas claramente ou que não ficam aparentes com facilidade no conjunto de recursos original ou existente. Mas esse processo tem algo de artístico. Decisões sensatas e produtivas frequentemente exigem alguma experiência de domínio.

Ao começar a usar o Azure Machine Learning, é mais fácil entender esse processo concretamente usando exemplos fornecidos no Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão, [Previsão do número de locações de bicicletas](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) , em um experimento supervisionado em que os valores de destino são conhecidos
* Um exemplo de classificação de mineração de texto usando [Hash de recursos](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-adding-temporal-features-for-regression-model"></a>Exemplo 1: adicionando recursos temporais para o modelo de regressão
Vamos usar o experimento "Previsão de demanda de bicicletas" no Azure Machine Learning Studio para demonstrar como fazer a engenharia de recursos para uma tarefa de regressão. O objetivo do experimento é prever a demanda de bicicletas, ou seja, o número de locações de bicicletas dentro de um mês/dia/hora específica. O “Conjunto de dados de UCI de locação de bicicletas” é usado como os dados brutos de entrada. Esse conjunto de dados baseia-se em dados reais da empresa Capital Bikeshare, que mantém uma rede de aluguel de bicicletas em Washington, D.C. nos Estados Unidos. O conjunto de dados representa o número de locações de bicicletas em uma hora específica de um dia nos anos 2011 e 2012 e contém 17379 linhas e 17 colunas. O conjunto de recursos brutos contém condições climáticas (temperatura/umidade/velocidade do vento) e o tipo de dia (feriado/dia da semana). O campo a ser previsto é "cnt", uma contagem que representa os aluguéis de bicicleta em uma hora específica e que varia de 1 a 977.

Com o objetivo de construir recursos efetivos nos dados de treinamento, quatro modelos de regressão são criados usando o mesmo algoritmo, mas com quatro conjuntos de dados de treinamento diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada brutos, mas com um número crescente de características definido. Os recursos são agrupados em quatro categorias:

1. A = características de clima + feriado + dia da semana + final de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das últimas 12 horas
3. C = número de bicicletas que foram alugadas em cada um dos últimos 12 dias, na mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das últimas 12 semanas, na mesma hora e mesmo dia

Além do conjunto de recursos A, que já existe nos dados brutos originais, três outros conjuntos de recursos são criados por meio do processo de engenharia de recursos. O conjunto de recursos B captura a demanda recente pelas bicicletas. O conjunto de recursos C captura a demanda por bicicletas em uma hora específica. O conjunto de recursos D captura a demanda por bicicletas em uma hora específica de um dia específico da semana. Os quatro conjuntos de dados de treinamento incluem os conjuntos de recursos A, A + B, A + B + C e A + B + C + D, respectivamente.

No experimento do Azure Machine Learning, esses quatro conjuntos de dados de treinamento são formados por meio de quatro ramificações do conjunto de dados de entrada pré-processado. Exceto pela ramificação mais à esquerda, cada uma dessas ramificações contém um módulo [Executar Script R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) , no qual um conjunto de recursos derivados (conjuntos de recursos B, C e D) são construídos e acrescentados ao conjunto de dados importado. A figura a seguir demonstra o script R sendo usado para criar o conjunto de recursos B na segunda ramificação à esquerda.

![criar recursos](./media/create-features/addFeature-Rscripts.png)

A comparação dos resultados de desempenho dos quatro modelos é resumida na tabela a seguir. Os melhores resultados são mostrados pelos recursos A + B + C. Observe que a taxa de erro diminui quando o conjunto de recursos adicional é incluído nos dados de treinamento. Isso confirma nossa suposição de que o conjunto de recursos B, C fornece informações adicionais relevantes para a tarefa de regressão. Mas adicionar o recurso D não parece fornecer qualquer redução adicional da taxa de erro.

![comparação de resultados](./media/create-features/result1.png)

## <a name="example2"></a> Exemplo 2: Criando recursos com mineração de texto
A engenharia de recursos é amplamente aplicada a tarefas relacionadas à mineração de texto, como classificação de documentos e análise de sentimento. Por exemplo, quando queremos classificar documentos em várias categorias, um pressuposto típico é que as palavras/expressões incluídas em uma categoria de documentos têm menor probabilidade de ocorrer em outra categoria de documentos. Em outras palavras, a frequência da distribuição de palavras/expressões é capaz de caracterizar diferentes categorias de documentos. Para aplicações associadas à mineração de texto, como trechos individuais do conteúdo de texto normalmente funcionam como dados de entrada, o processo de engenharia de recurso é necessário para criar os recursos que envolvem frequências de palavra/expressão.

Para realizar essa tarefa, uma técnica chamada **hash de recursos** é aplicada para transformar de maneira eficiente recursos de texto arbitrários em índices. Em vez de associar cada recurso de texto (palavras/expressões) a um índice em particular, este método funciona aplicando uma função de hash aos recursos e usando seus valores de hash como índices diretamente.

No Azure Machine Learning, há um módulo [Hash de Recursos](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) que cria esses recursos de palavra/expressão de maneira conveniente. A figura a seguir mostra um exemplo de uso deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro, que varia de 1 a 5, e o conteúdo real da resenha. O objetivo desse módulo de [Hash de Recursos](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) é recuperar um monte de novos recursos que mostram a frequência de ocorrência das palavras/expressões correspondentes dentro de uma resenha de livro. Para usar este módulo, precisamos concluir as seguintes etapas:

* Primeiro, selecione a coluna que contém o texto de entrada ("Col2" neste exemplo).
* Em segundo lugar, defina "Bitsize de hashing" como 8, o que significa que 2^8 = 256 recursos serão criados. A palavra/expressão em todo o texto será colocada em hash, em 256 índices. O parâmetro "Bitsize de hashing" varia de 1 a 31. As palavras/expressões têm menos probabilidades de serem colocadas em hash no mesmo índice se a configuração for para um número maior.
* Em terceiro lugar, defina o parâmetro “N-grams” como 2. Isso obtém a frequência de ocorrência de unigrams (um recurso para cada palavra) e bigrams (um recurso para cada par de palavras adjacentes) do texto de entrada. O parâmetro "N-grams" vai de 0 a 10, o que indica o número máximo de palavras sequenciais a serem incluídas em um recurso.  

![Módulo "Hash de Recursos"](./media/create-features/feature-Hashing1.png)

A figura a seguir mostra como ficariam esses novos recursos.

![Exemplo de "Hash de Recursos"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusão
Recursos de engenharia e selecionados aumentam a eficiência do processo de treinamento, que tenta extrair as informações importantes contidas nos dados. Eles também melhoram a capacidade desses modelos de classificar os dados de entrada com precisão e prever resultados de interesse com mais robustez. Também é possível combinar seleção e engenharia de recursos para que o aprendizado seja mais tratável por computação. Ele faz isso aperfeiçoando e, em seguida, reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Matematicamente, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e preveem os resultados com êxito.

Observe que nem sempre é necessário realizar a engenharia ou a seleção de recursos. Essa necessidade depende dos dados que temos ou coletamos, do algoritmo que escolhemos e do objetivo do experimento.

