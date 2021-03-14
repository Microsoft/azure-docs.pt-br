---
title: 'ML Studio (clássico): interpretar resultados do modelo-Azure'
description: Como escolher o parâmetro ideal definido para um algoritmo usando e visualizando saídas do modelo de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516117"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Interpretar os resultados do modelo no Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Essa é uma marca de seleção, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Este tópico explica como Visualizar e interpretar os resultados de previsão em Azure Machine Learning Studio (clássico). Após treinar um modelo e fazer previsões sobre ele ("pontuado o modelo"), você precisa compreender e interpretar o resultado da previsão.

Há quatro tipos principais de modelos de aprendizado de máquina no Azure Machine Learning Studio (clássico):

* Classificação
* Clustering
* Regressão
* Sistemas de recomendação

Os módulos usados para previsão sobre esses modelos são:

* Módulo [Modelo de Pontuação][score-model] para classificação e regressão
* Módulo [Atribuir aos Clusters][assign-to-clusters] para clustering
* [Recomendação da Caixa da Pontuação][score-matchbox-recommender] para sistemas de recomendação

Saiba como [escolher parâmetros para otimizar seus algoritmos no ml Studio (clássico)](algorithm-parameters-optimize.md).

Para saber como avaliar seus modelos, consulte [como avaliar o desempenho do modelo](evaluate-model-performance.md).

Se você for novo no ML Studio (clássico), [saiba como criar um experimento simples](create-experiment.md).

## <a name="classification"></a>Classificação
Há duas subcategorias de problemas de classificação:

* Problemas com apenas duas classes (classificação de duas classes ou binária)
* Problemas com mais de duas classes (classificação multiclasse)

Azure Machine Learning Studio (clássico) tem diferentes módulos para lidar com cada um desses tipos de classificação, mas os métodos para interpretar os resultados da previsão são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classes
**Teste de exemplo**

Um exemplo de problema de classificação de duas classes é a classificação de flores íris. A tarefa é classificar as flores íris com base em suas características. O conjunto de dados íris fornecido na Azure Machine Learning Studio (clássico) é um subconjunto do [conjunto de dados íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) popular que contém instâncias de apenas duas espécies de flor (classes 0 e 1). Há quatro características para cada flor (comprimento da sépala, largura da sépala, comprimento da pétala e largura da pétala).

![Captura de tela do experimento íris](./media/interpret-model-results/1.png)

Figura 1. Experimento do problema de classificação de duas classes da íris

Foi realizado um experimento para resolver esse problema, conforme mostrado na Figura 1. Um modelo de árvore de decisão aumentado das duas classes foi treinado e pontuado. Agora é possível visualizar os resultados da previsão do módulo [Modelo de Pontuação][score-model] clicando na porta de saída do módulo [Modelo de Pontuação][score-model] e, em seguida, clicando em **Visualizar**.

![Módulo de Modelo de pontuação](./media/interpret-model-results/1_1.png)

Isso exibe os resultados da pontuação, conforme mostrado na Figura 2.

![Resultados do experimento de classificação de duas classes da íris](./media/interpret-model-results/2.png)

Figura 2. Visualizar o resultado de um modelo de pontuação na classificação de duas classes

**Interpretação de resultado**

Há seis colunas na tabela de resultados. As quatro colunas à esquerda são os quatro recursos. À direita há duas colunas: Rótulos Pontuados e Probabilidades Pontuadas, que são os resultados da previsão. A coluna Probabilidades Pontuadas mostra a probabilidade de uma flor pertencer à classe positiva (Classe 1). Por exemplo, o primeiro número na coluna (0,028571) significa que há 0,028571 de probabilidade de a primeira flor pertencer à Classe 1. A coluna Rótulos Pontuados mostra a classe prevista para cada flor. Isso se baseia na coluna Probabilidades Pontuadas. Se a probabilidade pontuada de uma flor for superior a 0,5, ela será prevista como Classe 1. Caso contrário, ela será prevista como Classe 0.

**Publicação de serviço Web**

Após a compreensão e aceitação dos resultados da previsão, será possível publicar a experiência como um serviço Web, para que você possa implantá-la em vários aplicativos e chamá-la para obter previsões de classe sobre qualquer flor de íris nova. Para saber como alterar um teste de treinamento para um experimento de Pontuação e publicá-lo como um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md). Esse procedimento fornece um teste de pontuação conforme mostra a Figura 3.

![Captura de tela do teste de pontuação](./media/interpret-model-results/3.png)

Figura 3. Pontuação do experimento de problema de classificação de duas classes da íris

Agora, você precisa definir a entrada e a saída para o serviço Web. A entrada é a porta de entrada certa do [Modelo de pontuação][score-model], que é a entrada de recursos de flor de íris. A escolha da saída depende se você está interessado na classe prevista (rótulo pontuado), na probabilidade pontuada ou em ambas. Neste exemplo, supõe-se que tem interesse em ambas. Para selecionar as colunas de saída desejadas, use um módulo [Selecionar Colunas no Conjunto de Dados][select-columns]. Clique em [Selecionar Colunas no Conjunto de Dados][select-columns], clique em **Seletor de colunas de inicialização** e selecionamos **Rótulos Pontuados** e **Probabilidades Pontuadas**. Após a configuração da porta de saída de [Selecionar Colunas no Conjunto de Dados][select-columns] e executá-la novamente, você deverá estar pronto para publicar o teste de pontuação como um serviço Web clicando em **PUBLICAR SERVIÇO WEB**. O teste final se parece com a Figura 4.

![O experimento de classificação de duas classes da íris](./media/interpret-model-results/4.png)

Figura 4. Teste de pontuação final do problema de classificação de duas classes da íris

Após a execução do serviço Web e a inserção de alguns valores de recursos de uma instância de teste, o resultado retornará dois números. O primeiro número é o rótulo pontuado, e o segundo é a probabilidade pontuada. Com uma probabilidade de 0,9655, essa flor é prevista como Classe 1.

![Modelo de pontuação de interpretação do teste](./media/interpret-model-results/4_1.png)

![Resultados do teste de pontuação](./media/interpret-model-results/5.png)

Figura 5. Resultado do serviço Web da classificação de duas classes de íris

### <a name="multi-class-classification"></a>Classificação multiclasse
**Teste de exemplo**

Nesse experimento, você executa uma tarefa de reconhecimento de letra como um exemplo de classificação multiclasse. O classificador tenta prever uma determinada letra% 28class %29 com base em alguns valores de atributos escritos manualmente extraídos das imagens escritas à mão.

![Exemplo de reconhecimento de letra](./media/interpret-model-results/5_1.png)

Nos dados de treinamento, há 16 recursos extraídos de imagens de letra manuscrita. 26 letras formam as nossas 26 classes. A Figura 6 mostra um teste que treinará um modelo de classificação multiclasse para o reconhecimento de letra e para prever o mesmo recurso definido em um conjunto de dados de teste.

![Teste de classificação multiclasse de reconhecimento de letra](./media/interpret-model-results/6.png)

Figura 6. Teste de problema de classificação multiclasse de reconhecimento de letra

Visualize os resultados do módulo [Modelo de Pontuação][score-model] clicando na porta de saída do módulo [Modelo de Pontuação][score-model] e, em seguida, clicando em **Visualizar**. Você deverá ver uma janela igual à exibida na Figura 7.

![Resultados do modelo de pontuação](./media/interpret-model-results/7.png)

 Figura 7. Visualizar os resultados do modelo de pontuação na classificação multiclasses

**Interpretação de resultado**

As 16 colunas à esquerda representam os valores do recurso do conjunto de teste. As colunas com nomes como Probabilidades Pontuadas para a Classe "XX" são como a coluna Probabilidades Pontuadas no caso de duas classes. Elas mostram a probabilidade de que a entrada correspondente se encaixe em uma determinada classe. Por exemplo, para a primeira entrada, há 0, 3571 probabilidade de que seja uma probabilidade "A", 0, 451, que é um "B" e assim por diante. A última coluna (Rótulos Pontuados) é a mesma que Rótulos Pontuados no caso de duas classes. Ela seleciona a classe com a maior probabilidade de pontuação como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, o rótulo pontuado é "F", pois ele tem a maior probabilidade de ser um "F" (0,916995).

**Publicação de serviço Web**

Também é possível obter o rótulo pontuado para cada entrada e a probabilidade do rótulo pontuado. A lógica básica é encontrar a maior probabilidade dentre todas as probabilidades pontuadas. Para fazer isso, você precisa usar o módulo [Executar Script R][execute-r-script]. O código R é exibido na Figura 8, e o resultado do teste é exibido na Figura 9.

![Exemplo de código R](./media/interpret-model-results/8.png)

Figura 8. Código R para extração de Rótulos Pontuados e das probabilidades associadas dos rótulos

![Resultado do teste](./media/interpret-model-results/9.png)

Figura 9. Teste de pontuação final do problema de classificação multiclasse de reconhecimento de letra

Após a publicação e a execução do serviço Web e a inserção de alguns valores de característica de entrada, o resultado será semelhante à Figura 10. Essa letra escrita à mão, com seus 16 recursos extraídos, é prevista para ser um "T" com probabilidade de 0,9715.

![Modelo de pontuação de interpretação do teste](./media/interpret-model-results/9_1.png)

![Resultado do teste](./media/interpret-model-results/10.png)

Figura 10. Resultado do serviço Web da classificação multiclasses

## <a name="regression"></a>Regressão
Os problemas de regressão são diferentes dos problemas de classificação. Em um problema de classificação, você tenta prever classes distintas, como a qual classe uma flor íris pertence. Mas, como é possível ver no exemplo de problema de regressão a seguir, você está tentando prever uma variável contínua, como o preço de um carro.

**Teste de exemplo**

Use a previsão de preços de automóveis como exemplo de regressão. Você está tentando prever o preço de um carro com base em suas características, incluindo marca, tipo de combustível, tipo de carroceria e tração. O teste é mostrado na Figura 11.

![Teste de regressão do preço de automóvel](./media/interpret-model-results/11.png)

Figura 11. Teste de problema de regressão do preço de automóveis

Visualizando o módulo [Modelo de Pontuação][score-model], o resultado é semelhante à Figura 12.

![Resultados de pontuação para o problema de previsão do preço de automóveis](./media/interpret-model-results/12.png)

Figura 12. Resultado de pontuação para o problema de previsão do preço de automóveis

**Interpretação de resultado**

Nesse resultado de pontuação, Rótulos Pontuados representa a coluna de resultados. Os números são o preço previsto para cada carro.

**Publicação de serviço Web**

Você pode publicar o teste de regressão em um serviço Web e chamá-lo para a previsão do preço dos automóveis da mesma maneira como no caso de uso de classificação de duas classes.

![Teste de pontuação do problema de regressão do preço de automóveis](./media/interpret-model-results/13.png)

Figura 13. Teste de pontuação de um problema de regressão do preço de automóveis

Executando o serviço Web, o resultado é semelhante à Figura 14. O preço previsto para esse carro é de R$ 15.085,52.

![Modelo de pontuação de interpretação do teste](./media/interpret-model-results/13_1.png)

![Resultados do módulo de pontuação](./media/interpret-model-results/14.png)

Figura 14. Resultado do serviço Web de um problema de regressão do preço de automóveis

## <a name="clustering"></a>Clustering
**Teste de exemplo**

Vamos usar o conjunto de dados íris novamente para criar um experimento de clustering. Aqui, é possível filtrar os rótulos de classe no conjunto de dados para que ele tenha apenas as características e possa ser usado para clustering. Neste caso de uso de Íris, especifique o número de clusters como dois durante o processo de treinamento, o que significa que você agruparia as flores em duas classes. O teste é mostrado na Figura 15.

![Teste do problema de clustering de íris](./media/interpret-model-results/15.png)

Figura 15. Teste do problema de clustering de íris

O clustering difere da classificação, pois o conjunto de dados de treinamento não tem rótulos de verdade de terra por si só. O Clustering agrupa as instâncias de conjunto de dados de treinamento em clusters distintos. Durante o processo de treinamento, o modelo rotula as entradas aprendendo as diferenças entre os seus recursos. Depois disso, o modelo treinado pode usado para classificar com mais detalhes as entradas futuras. Há duas partes do resultado nas quais estamos interessados dentro de um problema de clustering. A primeira parte é a identificação do conjunto de dados de treinamento, e a segundo é a classificação de um novo conjunto de dados com o modelo treinado.

A primeira parte do resultado pode ser visualizada clicando na porta de saída à esquerda do módulo [Modelo de Clustering de Treino][train-clustering-model] e depois em **Visualizar**. A visualização é mostrada na Figura 16.

![Resultado do clustering](./media/interpret-model-results/16.png)

Figura 16. Visualizar resultado do clustering para o conjunto de dados de treinamento

O resultado da segunda parte, fazendo o clustering de novas entradas com o modelo de clustering treinado, é mostrado na Figura 17.

![Visualizar resultado do clustering](./media/interpret-model-results/17.png)

Figura 17. Visualizar resultado do clustering em um novo conjunto de dados

**Interpretação de resultado**

Embora os resultados de duas partes provenham de diferentes estágios do teste, eles têm a mesma aparência e são interpretados da mesma forma. As quatro primeiras colunas são recursos. A última coluna, Atribuições, é o resultado da previsão. As entradas que recebem o mesmo número são previstas como sendo do mesmo cluster, ou seja, elas têm alguma forma de semelhança (este teste usa a métrica de distância Euclidiana padrão). Como você especificou o número de clusters como dois, as entradas nas Atribuições são rotuladas 0 ou 1.

**Publicação de serviço Web**

Você pode publicar o experimento de clustering em um serviço Web e chamá-lo para a previsão do clustering da mesma maneira como no caso de uso de classificação de duas classes.

![Teste de pontuação do problema de clustering da íris](./media/interpret-model-results/18.png)

Figura 18. Teste de pontuação de um problema de clustering da íris

Após a execução do serviço Web, o resultado retornado será semelhante à Figura 19. É previsto que essa flor esteja no cluster 0.

![Modelo de pontuação de interpretação do teste](./media/interpret-model-results/18_1.png)

![Resultado do módulo de pontuação](./media/interpret-model-results/19.png)

Figura 19. Resultado do serviço Web da classificação de duas classes de íris

## <a name="recommender-system"></a>Sistema de recomendação
**Teste de exemplo**

Para sistemas de recomendação, você pode usar o problema de recomendação de restaurante como um exemplo: recomende restaurantes para clientes com base em seu histórico de classificação. Os dados de entrada consistem em três partes:

* Classificações de restaurante feitas pelos clientes
* Dados de recursos do cliente
* Dados de recurso de restaurante

Há várias coisas que podemos fazer com o módulo [treinar recomendador Matchbox][train-matchbox-recommender] no Azure Machine Learning Studio (clássico):

* Prever as classificações de um determinado usuário e item
* Itens recomendados a um determinado usuário
* Localizar usuários relacionados a um determinado usuário
* Localizar itens relacionados a um determinado item

Você pode escolher o quer fazer selecionando dentre as quatro opções no menu **Tipo de previsão de recomendação**. Aqui, você pode percorrer todos os quatro cenários.

![Recomendação do Matchbox](./media/interpret-model-results/19_1.png)

Um experimento típico de Azure Machine Learning Studio (clássico) para um sistema de recomendação é semelhante à figura 20. Para obter informações sobre como usar esses módulos de sistema de recomendações, confira [Recomendação da caixa de treino][train-matchbox-recommender] e [Recomendação da caixa de pontuação][score-matchbox-recommender].

![Teste do sistema de recomendação](./media/interpret-model-results/20.png)

Figura 20. Teste do sistema de recomendação

**Interpretação de resultado**

**Prever as classificações de um determinado usuário e item**

Ao selecionar **Previsão de Classificação** em **Tipo de previsão de recomendação**, você pede que o sistema de recomendação preveja a classificação de um determinado usuário e item. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 21.

![Resultado da pontuação do sistema de recomendação - previsão de classificação](./media/interpret-model-results/21.png)

Figura 21. Visualizar o resultado da pontuação do sistema de recomendação - previsão de classificação

As duas primeiras colunas são pares de item de usuário fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um usuário para um determinado item. Por exemplo, na primeira linha, é previsto que o cliente U1048 classifique o restaurante 135026 como 2.

**Itens recomendados a um determinado usuário**

Ao selecionar **Recomendação de Item** em **Tipo de previsão de recomendação**, você pede que o sistema de recomendação recomende itens a um determinado usuário. O último parâmetro a ser escolhido é *Seleção de item recomendado*. A opção **De itens de classificação (para a avaliação de modelo)** é usada principalmente para a avaliação do modelo durante o processo de treinamento. Escolhemos **De Todos os Itens** para este estágio da previsão. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 22.

![Resultado da pontuação do sistema de recomendação - recomendação de item](./media/interpret-model-results/22.png)

Figura 22. Visualizar o resultado da pontuação do sistema de recomendação - recomendação de item

A primeira das seis colunas representa as IDs de usuário fornecidas para as quais recomendar os itens, conforme fornecidas pelos dados de entrada. As outras cinco colunas representam os itens recomendados ao usuário, em ordem decrescente, em termos de relevância. Por exemplo, na primeira linha, o restaurante mais recomendado para o cliente U1048 é o 134986, seguido pelo 135018, 134975, 135021 e 132862.

**Localizar usuários relacionados a um determinado usuário**

Ao selecionar **Usuários Relacionados** em **Tipo de previsão de recomendação**, você pede que o sistema de recomendação encontre usuários relacionados a um determinado usuário. Os usuários relacionados são os usuários que têm preferências semelhantes. O último parâmetro a ser escolhido é *Seleção de usuário relacionado*. A opção **de usuários que classificaram itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de treinamento. Escolha **De Todos os Usuários** para este estágio da previsão. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 23.

![Resultado da pontuação do sistema de recomendação - usuários relacionados](./media/interpret-model-results/23.png)

Figura 23. Visualizar o resultado da pontuação do sistema de recomendação--usuários relacionados

A primeira das seis colunas mostra as IDs de usuário fornecidas necessárias para localizar usuários relacionados, conforme fornecidas pelos dados de entrada. As outras cinco colunas armazenam os usuários relacionados previstos do usuário, em ordem decrescente, de acordo com a relevância. Por exemplo, na primeira linha, o cliente mais relevante para o cliente U1048 é o U1051, seguido pelo U1066, U1044, U1017 e U1072.

**Localizar itens relacionados a um determinado item**

Ao selecionar **Itens Relacionados** em **Tipo de previsão de recomendação**, você pede que o sistema de recomendação encontre usuários relacionados a um determinado usuário. Itens relacionados são os itens com maior probabilidade de interessarem ao mesmo usuário. O último parâmetro a ser escolhido é *Seleção de item relacionado*. A opção **De itens de classificação (para a avaliação de modelo)** é usada principalmente para a avaliação do modelo durante o processo de treinamento. Escolhemos **De todos os itens** para este estágio da previsão. A visualização da saída [Recomendação da caixa de pontuação][score-matchbox-recommender] é semelhante à Figura 24.

![Resultado da pontuação do sistema de recomendação - itens relacionados](./media/interpret-model-results/24.png)

Figura 24. Visualizar os resultados da pontuação do sistema de recomendação - itens relacionados

A primeira das seis colunas representa as IDs de um determinado item necessárias para encontrar itens relacionados, conforme fornecidas pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item, em ordem decrescente em termos de relevância. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é o 135074, seguido pelo 135035, 132875, 135055 e 134992.

**Publicação de serviço Web**

O processo de publicação desses testes como serviços Web para obter previsões é semelhante para cada um dos quatro cenários. Aqui, adotamos o segundo cenário (recomendar itens para um determinado usuário) como exemplo. Você pode seguir o mesmo procedimento com os outros três.

Ao salvar o sistema de recomendação treinado como um modelo treinado e filtrar os dados de entrada para uma única coluna de ID de usuário único, conforme solicitado, você pode acionar o teste, como mostra a Figura 25, e publicá-lo como um serviço Web.

![Teste de pontuação do problema de recomendação de restaurante](./media/interpret-model-results/25.png)

Figura 25. Teste de pontuação do problema de recomendação de restaurante

Ao executar o serviço Web, o resultado é semelhante à Figura 26. Os cinco restaurantes recomendados para o usuário U1048 são 134986, 135018, 134975, 135021 e 132862.

![Exemplo de serviço de sistema de recomendação](./media/interpret-model-results/25_1.png)

![Resultados do teste de exemplo](./media/interpret-model-results/26.png)

Figura 26. Resultado do serviço Web do problema de recomendação de restaurante

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender