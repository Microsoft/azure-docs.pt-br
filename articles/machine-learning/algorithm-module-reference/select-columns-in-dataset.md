---
title: 'Selecionar colunas no conjunto de conjuntos: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo selecionar colunas no conjunto de informações em Azure Machine Learning para escolher um subconjunto de colunas a ser usado em operações de downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890284"
---
# <a name="select-columns-in-dataset-module"></a>Selecionar colunas no módulo DataSet

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para escolher um subconjunto de colunas a ser usado em operações de downstream. O módulo não remove fisicamente as colunas do conjunto de fonte de origem; em vez disso, ele cria um subconjunto de colunas, assim como uma *exibição* ou *projeção* de banco de dados.

Esse módulo é útil quando você precisa limitar as colunas disponíveis para uma operação downstream ou se deseja reduzir o tamanho do conjunto de um removendo colunas desnecessárias.

As colunas no DataSet são geradas na mesma ordem que nos dados originais, mesmo se você especificá-las em uma ordem diferente.

## <a name="how-to-use"></a>Como usar

Este módulo não tem parâmetros. Use o seletor de coluna para escolher as colunas a serem incluídas ou excluídas.

### <a name="choose-columns-by-name"></a>Escolher colunas por nome

Há várias opções no módulo para escolher colunas por nome: 

+ Filtrar e Pesquisar

    Clique na opção **por nome** .

    Se você tiver conectado um conjunto de um que já está populado, uma lista de colunas disponíveis deverá aparecer. Se nenhuma coluna aparecer, talvez seja necessário executar módulos upstream para exibir a lista de colunas.

    Para filtrar a lista, digite na caixa de pesquisa. Por exemplo, se você digitar a letra `w` na caixa de pesquisa, a lista será filtrada para mostrar os nomes de coluna que contêm a letra `w` .

    Selecione colunas e clique no botão de seta para a direita para mover as colunas selecionadas para a lista no painel à direita.

    + Para selecionar um intervalo contínuo de nomes de coluna, pressione **Shift + clique**.
    + Para adicionar colunas individuais à seleção, pressione **Ctrl + clique**.

    Clique no botão de marca de seleção para salvar e fechar.

+ Usar nomes em combinação com outras regras

    Clique na opção **com regras** .
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome para adicioná-las à lista de seleção.

+ Digite ou cole uma lista separada por vírgulas de nomes de coluna

    Se seu conjunto de seus conjuntos de seus for amplo, pode ser mais fácil usar índices ou listas de nomes geradas, em vez de selecionar colunas individualmente. Supondo que você preparou a lista com antecedência:

    1. Clique na opção **com regras** . 
    2. Selecione **nenhuma coluna**, selecione  **incluir** e, em seguida, clique dentro da caixa de texto com o ponto de exclamação vermelho. 
    3. Cole ou digite uma lista separada por vírgulas de nomes de coluna validados anteriormente. Você não poderá salvar o módulo se alguma coluna tiver um nome inválido, portanto, certifique-se de verificar os nomes com antecedência.
    
    Você também pode usar esse método para especificar uma lista de colunas usando seus valores de índice. 

### <a name="choose-by-type"></a>Escolher por tipo

Se você usar a opção **with Rules** , poderá aplicar várias condições nas seleções de coluna. Por exemplo, talvez seja necessário obter apenas colunas de recursos de um tipo de dados numérico.

A opção **begin with** determina seu ponto de partida e é importante para entender os resultados. 

+ Se você selecionar a opção **todas as colunas** , todas as colunas serão adicionadas à lista. Em seguida, você deve usar a opção **excluir** para *remover* colunas que atendam a determinadas condições. 

    Por exemplo, você pode iniciar com todas as colunas e, em seguida, remover colunas por nome ou por tipo.

+ Se você selecionar a opção **sem colunas** , a lista de colunas começará vazia. Em seguida, especifique as condições para *Adicionar* colunas à lista. 

    Se você aplicar várias regras, cada condição será **aditiva**. Por exemplo, digamos que você inicie sem colunas e, em seguida, adicione uma regra para obter todas as colunas numéricas. No conjunto de linhas de preço do automóvel, isso resulta em 16 colunas. Em seguida, clique no **+** sinal para adicionar uma nova condição e selecione **incluir todos os recursos**. O conjunto de resultados resultante inclui todas as colunas numéricas, além de todas as colunas de recurso, incluindo algumas colunas de recursos de cadeia de caracteres.

### <a name="choose-by-column-index"></a>Escolher por índice de coluna

O índice de coluna refere-se à ordem da coluna dentro do DataSet original.

+ As colunas são numeradas sequencialmente a partir de 1.  
+ Para obter um intervalo de colunas, use um hífen. 
+ Especificações abertas, como `1-` ou `-3` não são permitidas.
+ Valores de índice duplicados (ou nomes de coluna) não são permitidos e podem resultar em um erro.

Por exemplo, supondo que o conjunto de seus conjuntos de seus tem pelo menos oito colunas, você pode colar qualquer um dos seguintes exemplos para retornar várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta em um erro; no entanto, ele retorna uma única instância da coluna `4` .



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **permitir duplicatas e preservar a ordem das colunas na seleção** começa com uma lista vazia e adiciona colunas que você especifica por nome ou por índice. Ao contrário de outras opções, que sempre retornam colunas em sua "ordem natural", essa opção gera as colunas na ordem em que você as nomeou ou lista. 

Por exemplo, em um conjunto de linhas com as colunas Col1, Col2, Col3 e COL4, você pode inverter a ordem das colunas e deixar a coluna 2, especificando uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 