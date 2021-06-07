---
title: 'SVD Recomendador de Pontuação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de recomendação do SVD de pontuação no Azure Machine Learning para pontuar previsões de recomendação para um conjunto de informações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893711"
---
# <a name="score-svd-recommender"></a>Pontuar o sistema de recomendação SVD

Este artigo descreve como usar o módulo de recomendação SVD de pontuação no designer de Azure Machine Learning. Use este módulo para criar previsões usando um modelo de recomendação treinado com base no algoritmo de decomposição de valor único (SVD).

O recomendador SVD pode gerar dois tipos diferentes de previsões:

- [Prever as classificações de um determinado usuário e item](#prediction-of-ratings)
- [Itens recomendados a um usuário](#recommendations-for-users)

Quando você estiver criando o segundo tipo de previsões, poderá operar em um destes modos:

- O **modo de produção** considera todos os usuários ou itens. Ele é normalmente usado em um serviço Web.

  Você pode criar pontuações para novos usuários, não apenas os usuários vistos durante o treinamento. Para obter mais informações, consulte as [notas técnicas](#technical-notes). 

- O **modo de avaliação** opera em um conjunto reduzido de usuários ou itens que podem ser avaliados. Normalmente, ele é usado durante operações de pipeline.

Para obter mais informações sobre o algoritmo de recomendador SVD, consulte as [técnicas de fatoração de matriz de papel de pesquisa para sistemas de recomendação](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Como configurar o SVD Recomendador de Pontuação

Esse módulo dá suporte a dois tipos de previsões, cada um com requisitos diferentes. 

###  <a name="prediction-of-ratings"></a>Previsão de classificações

Quando você prevê as classificações, o modelo calcula como um usuário reagirá a um determinado item, considerando os dados de treinamento. Os dados de entrada para Pontuação devem fornecer um usuário e o item a serem classificados.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado**. Você deve criar o modelo usando o módulo [treinar recomendador SVD](train-SVD-recommender.md) .

2. Para **tipo de previsão de recomendação**, selecione **previsão de classificação**. Nenhum outro parâmetro é necessário.

3. Adicione os dados para os quais você deseja fazer previsões e conecte-os ao **DataSet para pontuar**.

   Para o modelo prever classificações, o conjunto de dados de entrada deve conter pares de item-usuário.

   O conjunto de linhas pode conter uma terceira coluna opcional de classificações para o par de item-usuário na primeira e segunda colunas. Mas a terceira coluna será ignorada durante a previsão.

4. Envie o pipeline.

### <a name="results-for-rating-predictions"></a>Resultados para previsões de classificação 

O conjunto de dados de saída contém três colunas: usuários, itens e a classificação prevista para cada usuário de entrada e item.

###  <a name="recommendations-for-users"></a>Recomendações para usuários 

Para recomendar itens para os usuários, você fornece uma lista de usuários e itens como entrada. A partir desses dados, o modelo usa seu conhecimento sobre itens e usuários existentes para gerar uma lista de itens com um apelo provável para cada usuário. Você pode personalizar o número de recomendações retornadas. E você pode definir um limite para o número de recomendações anteriores que são necessárias para gerar uma recomendação.

1. Adicione um modelo de recomendação treinado ao seu pipeline e conecte-o ao **recomendador SVD treinado**.  Você deve criar o modelo usando o módulo [treinar recomendador SVD](train-svd-recommender.md) .

2. Para recomendar itens para uma lista de usuários, defina o **tipo de previsão** de recomendação como recomendações de **Item**.

3. Para **seleção de item recomendado**, indique se você está usando o módulo de pontuação em produção ou para avaliação de modelo. Escolha um destes valores:

    - **De todos os itens**: Selecione esta opção se você estiver configurando um pipeline para usar em um serviço Web ou em produção.  Essa opção habilita o *modo de produção*. O módulo faz recomendações de todos os itens vistos durante o treinamento.

    - **De itens classificados (para avaliação de modelo)**: Selecione esta opção se você estiver desenvolvendo ou testando um modelo. Essa opção habilita o *modo de avaliação*. O módulo faz recomendações apenas dos itens no conjunto de dados de entrada que foram classificados.
    
    - **De itens sem classificação (para sugerir novos itens para os usuários)**: Selecione esta opção se desejar que o módulo faça recomendações apenas desses itens no conjunto de módulos de treinamento que não foram classificados. 

4. Adicione o conjunto de um para o qual você deseja fazer previsões e conecte-o ao **conjunto de pontos para Pontuação**.

    - Para **de todos os itens**, o conjunto de dados de entrada deve consistir em uma coluna. Ele contém os identificadores de usuários para os quais fazer recomendações.

      O conjunto de valores pode incluir duas colunas extras de identificadores e classificações de item, mas essas duas colunas são ignoradas. 

    - Para **itens classificados (para avaliação de modelo)**, o conjunto de dados de entrada deve consistir em pares de item de usuário. A primeira coluna deve conter o identificador de usuário. A segunda coluna deve conter os identificadores de item correspondentes.

      O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.

    - Para **itens sem classificação (para sugerir novos itens para os usuários)**, o conjunto de dados de entrada deve consistir em pares de item-usuário. A primeira coluna deve conter o identificador de usuário. A segunda coluna deve conter os identificadores de item correspondentes.

     O conjunto de valores pode incluir uma terceira coluna de classificações de usuário-item, mas essa coluna é ignorada.

5. **Número máximo de itens para recomendar a um usuário**: Insira o número de itens a serem retornados para cada usuário. Por padrão, o módulo recomenda cinco itens.

6. **Tamanho mínimo do pool de recomendações por usuário**: Insira um valor que indica quantas recomendações anteriores são necessárias. Por padrão, esse parâmetro é definido como 2, o que significa que pelo menos dois outros usuários recomendam o item.

   Use esta opção somente se você estiver pontuando no modo de avaliação. A opção não estará disponível se você selecionar **de todos os itens** ou **de itens sem classificação (para sugerir novos itens para os usuários)**.

7.  Para **itens sem classificação (para sugerir novos itens para os usuários)**, use a terceira porta de entrada, denominada **dados de treinamento**, para remover os itens que já foram classificados dos resultados da previsão.

    Para aplicar esse filtro, conecte o conjunto de dados de treinamento original à porta de entrada.

8. Envie o pipeline.

### <a name="results-of-item-recommendation"></a>Resultados da recomendação do item

O conjunto de pontos de Pontuação retornado por Score SVD Recomendador lista os itens recomendados para cada usuário:

- A primeira coluna contém os identificadores de usuário.
- Várias colunas adicionais são geradas, dependendo do valor que você definiu para o **número máximo de itens a serem recomendados a um usuário**. Cada coluna contém um item recomendado (por identificador). As recomendações são ordenadas por afinidade de usuário-item. O item com a afinidade mais alta é colocado no **Item** de coluna 1.


##  <a name="technical-notes"></a>Observações técnicas

Se você tiver um pipeline com o recomendador SVD e mover o modelo para produção, lembre-se de que há diferenças importantes entre usar o recomendador no modo de avaliação e usá-lo no modo de produção.

A avaliação, por definição, exige previsões que podem ser verificadas em relação a *verdades* em um conjunto de teste. Quando você avalia o recomendador, ele deve prever apenas os itens que foram classificados no conjunto de teste. Isso restringe os possíveis valores que são previstos.

Ao colocar o modelo em operação, você normalmente altera o modo de previsão para fazer recomendações com base em todos os itens possíveis, a fim de obter as melhores previsões. Para muitas dessas previsões, não há nenhuma verdade de terra correspondente. Portanto, a precisão da recomendação não pode ser verificada da mesma maneira que durante as operações de pipeline.


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
