---
title: Usar o módulo de recomendação aprofundado de & de treinamento
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo de recomendação & profundo de treinamento no Azure Machine Learning designer para treinar um modelo de recomendações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655369"
---
# <a name="train-wide--deep-recommender"></a>Treinar amplo & Recomendador profundo
Este artigo descreve como usar o módulo de recomendação **& Deep Wide de treinamento** no Azure Machine Learning designer, para treinar um modelo de recomendações. Esse módulo baseia-se em amplo & aprendizado profundo, que é proposto pelo Google.

O módulo **treinamento amplo & profundo de recomendação** lê um conjunto de módulos de percurso de classificação de usuário-item e, opcionalmente, alguns recursos de usuário e item. Ele retorna um recomendador profundo & de larga experiência.  Você pode usar o modelo treinado para gerar previsões ou recomendações de classificação usando o módulo de [recomendação de Pontuação e de profundidade](score-wide-and-deep-recommender.md) .  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Mais sobre modelos de recomendação e o amplo & recomendador  

O objetivo principal de um sistema de recomendação é recomendar um ou mais *itens* aos *usuários* do sistema. Exemplos de um item podem ser um filme, restaurante, livro ou música. Um usuário pode ser uma pessoa, grupo de pessoas ou outra entidade com as preferências de itens.  

Há duas abordagens principais para sistemas recomendadores. 

+ A primeira é a abordagem **com base em conteúdo**, que usa recursos para usuários e itens. Os usuários podem ser descritos por propriedades, como idade e sexo; e itens podem ser descritos por propriedades, como autor e fabricante. Exemplos típicos de sistemas de recomendação baseados em conteúdo podem ser encontrados nos sites de compatibilidade social. 
+ A segunda abordagem é **filtragem de colaboração**, que usa apenas identificadores dos usuários e itens e obtém informações implícitas sobre essas entidades de uma matriz (esparsa) de classificações fornecidas pelos usuários para os itens. Podemos aprender sobre um usuário por meio dos itens que ele classificou e de outros usuários que classificaram os mesmos itens.  

O recomendador amplo &s combina essas abordagens, usando a filtragem colaborativa com uma abordagem baseada em conteúdo. Portanto, ele é considerado um **recomendador híbrido**. 

Como isso funciona: quando um usuário é relativamente novo no sistema, as previsões são aprimoradas com o uso das informações de recurso sobre o usuário, abordando assim o problema "início frio" bem conhecido. No entanto, após ter coletado um número suficiente de classificações de um determinado usuário, é possível fazer previsões totalmente personalizadas para eles com base em suas classificações específicas e não em seus recursos apenas. Portanto, há uma transição suave das recomendações com base em conteúdo para recomendações baseadas na filtragem de colaboração. Mesmo que os recursos de usuário ou item não estejam disponíveis, a maioria & recomendadores profundos ainda funcionarão em seu modo de filtragem colaborativa.  

Mais detalhes sobre o recomendador de grande & e seu algoritmo probabilística subjacente podem ser encontrados no documento de pesquisa relevante: [Wide & Learning Deep para sistemas de recomendação](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Como configurar a recomendação do Training & Deep  

+ [Preparar os dados de treinamento](#prepare-data)
+ [Treinar o modelo](#train-the-model)

### <a name="prepare-data"></a>Preparar dados

Antes de tentar usar o módulo, é essencial que os dados estejam no formato esperado pelo modelo de recomendação. Um conjunto de dados de treinamento de **percurso de classificação de usuário-item** é necessário, mas você também pode incluir recursos de usuário e recursos de item (se disponíveis), em conjuntos de dados separados.

#### <a name="required-dataset-of-user-item-ratings"></a>Conjunto de usuários necessário de classificações de usuário-item

Os dados de entrada usados para treinamento devem conter o tipo certo de dados no formato correto: 

+ A primeira coluna deve conter identificadores de usuário.
+ A segunda coluna deve conter identificadores de item.
+ A terceira coluna contém a classificação para o par usuário-item. Os valores de classificação devem ser do tipo numérico. 

Por exemplo, um conjunto típico de classificações de item de usuário pode ser assim:

|UserId|FilmeID|Classificação|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Conjunto de usuários de recursos do usuário (opcional)

O conjunto de os **recursos de usuário** deve conter identificadores para usuários e usar os mesmos identificadores que foram fornecidos na primeira coluna do conjunto de e usuários-itens-classificações. As colunas restantes podem conter qualquer número de recursos que descrevam os usuários.  

Por exemplo, um conjunto típico de recursos de usuário pode ser assim: 

|UserId|Idade|Sexo|Preocupações|Local|
|------------|--------------|-----------------------|---------------|------------|
|1|25|masculino| Drama    |Europa|
|223|40|feminino|Hora oficial românica|Ásia|

#### <a name="item-features-dataset-optional"></a>Conjunto de itens de recursos de item (opcional)

O conjunto de dados de recursos de item deve conter identificadores de item na primeira coluna. As colunas restantes podem conter qualquer número de recursos descritivos para os itens.  

Por exemplo, um conjunto típico de recursos de item pode ser assim:  

|FilmeID|Título|Idioma original|Gêneros|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|O Godfather|Inglês|Drama|1972|
|31381|Saiu com o vento|Inglês|Histórico|1939|

### <a name="train-the-model"></a>Treinar o modelo

1.  Adicione o módulo **treinamento largo e profundo** ao seu experimento no designer e conecte-o ao conjunto de testes de treinamento.  
  
2. Se você tiver um conjunto de usuários separado de recursos de usuário e/ou recursos de item, conecte-os ao módulo de **recomendação de treinamento amplo e profundo** .  
  
    - **Conjunto** de dados de recursos do usuário: Conecte o conjunto que descreve os usuários à segunda entrada.
    - **Conjunto** de dados de recursos de item: Conecte o conjunto que descreve os itens à terceira entrada.  
    
3.  **Épocas**: indique quantas vezes o algoritmo deve processar os dados de treinamento inteiros. 

    Quanto maior esse número, mais adequado o treinamento; no entanto, o treinamento custa mais tempo e pode causar sobreajuste.

4. **Tamanho do lote**: digite o número de exemplos de treinamento utilizados em uma etapa de treinamento. 

     Esse hiperparâmetro pode influenciar a velocidade de treinamento. Um tamanho de lote mais alto leva a uma época de custo menos demorada, mas pode aumentar o tempo de convergência. E se o lote for muito grande para se ajustar à GPU/CPU, um erro de memória poderá ser gerado.

5.  **Otimizador de parte larga**: selecione um otimizador para aplicar gradientes à parte larga do modelo.

6.  **Taxa de aprendizagem do otimizador amplo**: Insira um número entre 0,0 e 2,0 que define a taxa de aprendizado do otimizador de parte largo.

    Esse hiperparâmetro determina o tamanho da etapa em cada etapa de treinamento ao mover para uma função de perda mínima. Uma taxa de aprendizagem muito grande pode fazer com que o aprendizado passe pelo mínimo, enquanto uma taxa de aprendizagem muito pequena pode causar um problema de convergência.

7.  **Dimensão de recurso cruzada**: digite a dimensão inserindo as IDs de usuário desejadas e os recursos de ID de item. 

    O recomendador amplo & profundo executa a transformação entre produtos por padrão por ID de usuário e recursos de ID de item. O resultado será transformado em hash de acordo com esse número para garantir a dimensão.

8.  **Otimizador de parte profunda**: selecione um otimizador para aplicar gradientes à parte profunda do modelo.

9.  **Taxa de aprendizagem do otimizador profundo**: Insira um número entre 0,0 e 2,0 que define a taxa de aprendizado do otimizador de parte aprofundado.

10.  **Dimensão de inserção de usuário**: digite um inteiro para especificar a dimensão da inserção da ID de usuário.

     O recomendador amplo & profundo cria as incorporações de ID de usuário compartilhadas e de ID de item para toda parte e parte profunda.

11.  **Dimensão de incorporação de itens**: digite um inteiro para especificar a dimensão de inserção de ID de item.

12.  **Dimensão de incorporação de recursos categóricos**: Insira um número inteiro para especificar as dimensões de incorporações de recursos categóricos.

     Em um componente profundo de amplo & recomendador profundo, um vetor de incorporação é Aprenda para cada recurso categórico. E esses vetores de incorporação compartilham a mesma dimensão.

13.  **Unidades ocultas**: digite o número de nós ocultos do componente profundo. O número de nós em cada camada é separado por vírgulas. Por exemplo, por tipo "1.000.500.100", você especifica que o componente profundo tem três camadas, com a primeira camada até a última, respectivamente, tem 1000 nós, 500 nós e 100 nós.

14.  **Função de ativação**: selecione uma função de ativação aplicada a cada camada, o padrão é ReLU.

15.  **Abandono**: Insira um número entre 0,0 e 1,0 para determinar a probabilidade de que as saídas serão descartadas em cada camada durante o treinamento.

     Abandono é um método de regularização para impedir que redes neurais se sobreajustem. Uma decisão comum para esse valor é começar com 0,5, que parece estar perto do ideal para uma ampla gama de redes e tarefas.

16.  **Normalização do lote**: Selecione esta opção para usar a normalização do lote após cada camada oculta no componente profundo.

     A normalização do lote é uma técnica para combater o problema de mudança de coregularidade interna durante o treinamento de redes. Em geral, ele pode ajudar a melhorar a velocidade, o desempenho e a estabilidade das redes. 

17.  Execute o pipeline.

## <a name="results"></a>Resultados

Após a execução do pipeline ser concluída, para usar o modelo de pontuação, conecte o [treinamento em toda e profundo](train-wide-and-deep-recommender.md) para [pontuar a pontuação de uma e mais profunda](score-wide-and-deep-recommender.md), para prever valores para novos exemplos de entrada.

##  <a name="technical-notes"></a>Observações técnicas

A ampla & profundamente treina modelos lineares largos e redes neurais profundas para combinar os pontos fortes de memorização e generalização. O amplo componente aceita um conjunto de recursos brutos e transformações de recursos para memorizar interações de recursos. E com menos engenharia de recursos, o componente profundo generalizado para combinações de recursos não vistos por meio de incorporações de recursos densas de baixo dimensão. 

Na implementação de amplo & recomendador profundo, o módulo usa uma estrutura de modelo padrão. O componente amplo usa incorporações de usuários, incorporações de itens e a transformação entre produtos de IDs de usuário e IDs de item como entrada. Para a parte profunda do modelo, um vetor de incorporação é Aprenda para cada recurso categórico. Junto com outros vetores de recursos numéricos, esses vetores são então alimentados na rede neural de avanço de alimentação profunda. A parte larga e a parte profunda são combinadas ao somar suas chances de log de saída final como a previsão, que finalmente passa para uma função de perda comum para o treinamento em conjunto.


## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) de Azure Machine Learning. 
