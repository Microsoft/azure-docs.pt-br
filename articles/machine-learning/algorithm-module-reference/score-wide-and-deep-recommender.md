---
title: Usar o módulo de recomendação & profundo de Pontuação
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de recomendação & profundo de pontuação no Azure Machine Learning para pontuar as previsões de recomendações para um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905162"
---
# <a name="score-wide-and-deep-recommender"></a>Pontuar o sistema de recomendação grande e profundo

Este artigo descreve como usar o módulo de recomendação de **Pontuação e de largura profunda** no Azure Machine Learning designer, para criar previsões com base em um modelo de recomendação treinado, com base na ampla & aprendizado profundo do Google.

O recomendador amplo e profundo pode gerar dois tipos diferentes de previsões:

- [Prever as classificações de um determinado usuário e item](#predict-ratings)

- [Itens recomendados a um determinado usuário](#recommend-items)


Ao criar o último tipo de previsões, você pode operar no modo de *produção* ou no *modo de avaliação*.

- O **modo de produção** considera todos os usuários ou itens e normalmente é usado em um serviço Web. Você pode criar pontuações para novos usuários, não apenas os usuários vistos durante o treinamento. 

- O **modo de avaliação** opera em um conjunto reduzido de usuários ou itens que podem ser avaliados e normalmente é usado durante a experimentação.

Mais detalhes sobre o recomendador amplo e profundo e sua teoria subjacente podem ser encontrados no documento de pesquisa relevante:  [Wide & Learning profundo para sistemas de recomendação](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Como configurar a pontuação de largura e de recomendação profunda

Esse módulo dá suporte a diferentes tipos de recomendações, cada um com requisitos diferentes. Clique no link para o tipo de dados que você tem e o tipo de recomendação que você deseja criar.

+ [Prever classificações](#predict-ratings)
+ [Itens recomendados](#recommend-items)

### <a name="predict-ratings"></a>Prever classificações

Quando você prevê as classificações, o modelo calcula como um determinado usuário reagirá a um determinado item, considerando os dados de treinamento. Portanto, os dados de entrada para Pontuação devem fornecer um usuário e o item a serem classificados.

1. Adicione um modelo de recomendação com experiência & profunda ao seu experimento e conecte-o a um **modelo de recomendação amplo e profundo**.  Você deve criar o modelo usando [treinar em todo e recomendado](train-wide-and-deep-recommender.md).

2. **Tipo de previsão de recomendação**: selecione **previsão de classificação**. Nenhum parâmetro adicional é necessário.

3. Adicione os dados para os quais você deseja fazer previsões e conecte-os ao **DataSet para pontuar**.

    Para prever as classificações, o conjunto de dados de entrada deve conter pares de item-usuário.

    O conjunto de linhas pode conter uma terceira coluna opcional de classificações para o par de item-usuário na primeira e segunda colunas, mas a terceira coluna será ignorada durante a previsão.

4.  (Opcional). Se você tiver um conjunto de uma série de recursos do usuário, conecte-o aos **recursos do usuário**.

    O conjunto de os recursos do usuário deve conter o identificador de usuário na primeira coluna. As colunas restantes devem conter valores que caracterizam os usuários, como o gênero, as preferências, o local etc.
  
    Os recursos de usuários que têm itens classificados no conjunto de recursos de treinamento são ignorados por **Pontuação de largura e profunda**, pois já foram aprendidos durante o treinamento. Portanto, filtre seu conjunto de seus com antecedência para incluir somente *usuários de inicialização a frio* ou usuários que não tenham classificado nenhum item.

    > [!WARNING]
    > Se o modelo foi treinado sem usar recursos de usuário, você não pode introduzir recursos de usuário durante a pontuação.

5. Se você tiver um conjunto de uma série de recursos do item, poderá conectá-lo aos **recursos do item**.

    O conjunto de itens de recursos de item deve conter um identificador de item na primeira coluna. As colunas restantes devem conter valores que caracterizam os itens.

    Os recursos de itens classificados no conjunto de recursos de treinamento são ignorados por **Pontuação de largura e de fundo profundo** , pois eles já foram aprendidos durante o treinamento. Portanto, restrinja o conjunto de seus conjuntos de pontos de Pontuação para *itens de início frio* ou itens que não tenham sido classificados por nenhum usuário.

    > [!WARNING]
    > Se o modelo foi treinado sem usar recursos de item, você não pode introduzir recursos de item durante a pontuação.

7. Execute o experimento.

### <a name="results-for-rating-predictions"></a>Resultados para previsões de classificação 

O conjunto de dados de saída contém três colunas, contendo o usuário, o item e a classificação prevista para cada usuário de entrada e item.

Além disso, as seguintes alterações são aplicadas durante a Pontuação:

-  Para uma coluna de recurso numérico de usuário ou item, os valores ausentes são substituídos automaticamente pela **média** de seus valores de conjunto de treinamento não ausentes. Para o recurso categórico, os valores ausentes são substituídos pelo mesmo valor categórico diferente de quaisquer valores possíveis desse recurso.
-  Nenhuma tradução é aplicada aos valores de recurso, para manter sua dispersão.

### <a name="recommend-items"></a>Itens recomendados

Para recomendar itens para os usuários, você fornece uma lista de usuários e itens como entrada. A partir desses dados, o modelo usa seu conhecimento sobre itens e usuários existentes para gerar uma lista de itens com um apelo provável para cada usuário. Você pode personalizar o número de recomendações retornadas e definir um limite para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação de nível treinado e amplo ao seu experimento e conecte-o a um **modelo de recomendações amplo e profundo**.  Você deve criar o modelo usando [treinar em todo e recomendado](train-wide-and-deep-recommender.md).

2. Para recomendar itens para uma determinada lista de usuários, defina **tipo de previsão de recomendado** como **recomendação de item**.

3. **Seleção de item recomendada**: indique se você está usando o módulo de pontuação em produção ou para avaliação de modelo, escolhendo um destes valores:

    - **De itens classificados (para avaliação de modelo)**: Selecione esta opção se você estiver desenvolvendo ou testando um modelo. Essa opção habilita o **modo de avaliação** e o módulo faz recomendações apenas dos itens no conjunto de dados de entrada que foram classificados.
    - **De todos os itens**: Selecione esta opção se você estiver configurando um experimento para usar em um serviço Web ou produção.  Essa opção habilita o **modo de produção** e o módulo faz recomendações de todos os itens vistos durante o treinamento.
    - **De itens sem classificação (para sugerir novos itens para os usuários)**: Selecione esta opção se desejar que o módulo faça recomendações apenas desses itens no conjunto de módulos de treinamento que não foram classificados. 
4. Adicione o conjunto de um para o qual você deseja fazer previsões e conecte-o ao **conjunto de pontos para Pontuação**.

    - Se você escolher a opção, **de todos os itens**, o conjunto de dados de entrada deverá consistir em apenas uma coluna, contendo os identificadores de usuários para os quais fazer recomendações.

        O conjunto de valores pode incluir duas colunas extras de identificadores e classificações de item, mas essas duas colunas são ignoradas. 

    - Se você escolher a opção **de itens classificados (para avaliação de modelo)**, o conjunto de dados de entrada deverá consistir em **pares de item de usuário**. A primeira coluna deve conter o identificador de **usuário** . A segunda coluna deve conter os identificadores de **Item** correspondentes.

        O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.
        
    - Para **itens sem classificação (para sugerir novos itens para os usuários)**, o conjunto de dados de entrada deve consistir em pares de item-usuário. A primeira coluna deve conter o identificador de usuário. A segunda coluna deve conter os identificadores de item correspondentes.

        O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.

5. (Opcional). Se você tiver um conjunto de uma série de **recursos do usuário**, conecte-o aos **recursos do usuário**.

    A primeira coluna no conjunto de recursos User Features deve conter o identificador de usuário. As colunas restantes devem conter valores que caracterizam o usuário, como sexo, preferências, local etc.

    Recursos de usuários que têm itens classificados são ignorados por **Pontuação de largura e de fundo profundo**, pois esses recursos já foram aprendidos durante o treinamento. Portanto, você pode filtrar seu conjunto de seus com antecedência para incluir somente *usuários de inicialização a frio* ou usuários que não tenham classificado nenhum item.

    > [!WARNING]
    >  Se o modelo foi treinado sem usar recursos do usuário, você não pode usar aplicar recursos durante a pontuação.

6. Adicional Se você tiver um conjunto de uma série de **recursos do item**, poderá conectá-lo aos **recursos do item**.

    A primeira coluna no conjunto de recursos do item Features deve conter o identificador de item. As colunas restantes devem conter valores que caracterizam os itens.

    Os recursos de itens classificados são ignorados por **Pontuação de largura e de fundo profundo**, pois esses recursos já foram aprendidos durante o treinamento. Portanto, você pode restringir o conjunto de seus conjuntos de acordo com os *itens de início frio* ou os itens que não foram classificados por nenhum usuário.

    > [!WARNING]
    >  Se o modelo foi treinado sem usar recursos de item, não use recursos de item ao Pontuação.  

7. **Número máximo de itens a serem recomendados para um usuário**: digite o número de itens a serem retornados para cada usuário. Por padrão, são recomendados 5 itens.

8. **Tamanho mínimo do pool de recomendações por usuário**: digite um valor que indique quantas recomendações anteriores são necessárias.  Por padrão, esse parâmetro é definido como 2, o que significa que o item deve ter sido recomendado por pelo menos dois outros usuários.

    Essa opção deve ser usada somente se você estiver pontuando no modo de avaliação. A opção não estará disponível se você selecionar **de todos os itens** ou **de itens sem classificação (para sugerir novos itens para os usuários)**.

9. Para **itens sem classificação (para sugerir novos itens para os usuários)**, use a terceira porta de entrada, denominada **dados de treinamento**, para remover os itens que já foram classificados dos resultados da previsão.

    Para aplicar esse filtro, conecte o conjunto de dados de treinamento original à porta de entrada.

10. Execute o experimento.
### <a name="results-of-item-recommendation"></a>Resultados da recomendação do item

O conjunto de pontos de Pontuação retornado pelo **recomendável de Pontuação e de profundidade** lista os itens recomendados para cada usuário.

- A primeira coluna contém os identificadores de usuário.
- Várias colunas adicionais são geradas, dependendo do valor definido para o **número máximo de itens a ser recomendado para um usuário**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas por afinidade de usuário-item, com o item com a afinidade mais alta colocada na coluna, **Item 1**.

##  <a name="technical-notes"></a>Observações técnicas

Esta seção contém respostas para algumas perguntas comuns sobre como usar o amplo & recomendador para criar previsões.  

###  <a name="cold-start-users-and-recommendations"></a>Usuários e recomendações de início frio

Normalmente, para criar recomendações, o módulo de **recomendação de Pontuação e de profundidade** exige as mesmas entradas que você usou ao treinar o modelo, incluindo uma ID de usuário. Isso ocorre porque o algoritmo precisa saber se aprendeu algo sobre este usuário durante o treinamento. 

No entanto, para novos usuários, talvez você não tenha uma ID de usuário, apenas alguns recursos do usuário, como idade, gênero e assim por diante.

Você ainda pode criar recomendações para usuários que são novos no sistema tratando-os como *usuários de inicialização a frio*. Para esses usuários, o algoritmo de recomendação não usa o histórico antigo nem as classificações anteriores, somente os recursos de usuário.

Para fins de previsão, um usuário de inicialização a frio é definido como um usuário com uma ID que não foi usada para treinamento. Para garantir que as IDs não correspondam às IDs usadas no treinamento, você pode criar novos identificadores. Por exemplo, você pode gerar IDs aleatórias em um intervalo especificado ou alocar uma série de IDs com antecedência para usuários de inicialização a frio.

No entanto, se você não tiver nenhum dado de filtragem colaborativo, como um vetor de recursos do usuário, será melhor usar um aprendiz de classificação ou regressão.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Uso de produção do recomendador amplo e profundo

Se você experimentou o recomendável amplo e profundo e depois mover o modelo para produção, esteja atento a essas principais diferenças ao usar o recomendador no modo de avaliação e no modo de produção:

- A avaliação, por definição, exige previsões que podem ser verificadas em relação a *verdades* em um conjunto de teste. Portanto, quando você avalia o recomendador, ele deve prever apenas os itens que tenham sido classificados no conjunto de teste. Isso necessariamente restringe os valores possíveis que estão previstos.

    No entanto, ao colocar em operação o modelo, você normalmente altera o modo de previsão para fazer recomendações com base em todos os itens possíveis, para obter as melhores previsões. Para muitas dessas previsões, não há nenhuma verdade de terra correspondente, portanto, a precisão da recomendação não pode ser verificada da mesma maneira que durante a experimentação.

- Se não fornecer uma ID de usuário na produção e fornecer um vetor de recurso, você pode obter como resposta uma lista de todas as recomendações para todos os usuários possíveis. Certifique-se de fornecer uma ID de usuário.

    Para limitar o número de recomendações retornadas, especifique também o número máximo de itens retornados por usuário. 



## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) de Azure Machine Learning. 
