---
title: Transformação de junção no fluxo de dados de mapeamento
description: Combinar dados de duas fontes de dados usando a transformação de junção no fluxo de dados de mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042650"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Transformação de junção no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação de junção para combinar dados de duas fontes ou fluxos em um fluxo de dados de mapeamento. O fluxo de saída incluirá todas as colunas de ambas as fontes correspondentes, com base em uma condição de junção. 

## <a name="join-types"></a>Tipos de junção

Os fluxos de dados de mapeamento atualmente são compatíveis com cinco tipos diferentes de junção.

### <a name="inner-join"></a>Junção interna

A junção interna só gera linhas que têm valores correspondentes em ambas as tabelas.

### <a name="left-outer"></a>Externa esquerda

A junção externa esquerda retorna todas as linhas do fluxo à esquerda e os registros correspondentes do fluxo à direita. Se uma linha do fluxo à esquerda não tiver correspondência, as colunas de saída do fluxo à direita serão definidas como NULL. A saída será as linhas retornadas por uma junção interna mais as linhas não correspondentes do fluxo à esquerda.

> [!NOTE]
> O mecanismo do Spark usado pelos fluxos de dados ocasionalmente falhará devido a possíveis produtos cartesianos em suas condições de junção. Se isso ocorrer, você poderá mudar para uma junção cruzada personalizada e inserir manualmente sua condição de junção. Isso pode resultar em um desempenho mais lento em seus fluxos de dados, pois o mecanismo de execução talvez precise calcular todas as linhas de ambos os lados do relacionamento e, em seguida, filtrá-las.

### <a name="right-outer"></a>Externa direita

A junção direita esquerda retorna todas as linhas do fluxo à direita e os registros correspondentes do fluxo à esquerda. Se uma linha do fluxo à direita não tiver correspondência, as colunas de saída do fluxo à esquerda serão definidas como NULL. A saída será as linhas retornadas por uma junção interna mais as linhas não correspondentes do fluxo à direita.

### <a name="full-outer"></a>Externa completa

A junção externa completa gera todas as colunas e linhas de ambos os lados com valores NULL para colunas sem correspondência.

### <a name="custom-cross-join"></a>União cruzada personalizada

A união cruzada gera o produto cruzado dos dois fluxos com base em uma condição. Se você estiver usando uma condição que não seja de igualdade, especifique uma expressão personalizada como condição de união cruzada. O fluxo de saída será todas as linhas que atendam à condição de junção.

Você pode usar esse tipo de junção nas junções não equivalentes e nas condições ```OR```.

Para produzir explicitamente um produto cartesiano completo, use a transformação Coluna Derivada em cada um dos dois fluxos independentes, antes da junção, para criar uma chave sintética na qual corresponder. Por exemplo, crie uma nova coluna na Coluna Derivada em cada fluxo chamado ```SyntheticKey``` e defina-a como ```1```. Em seguida, use ```a.SyntheticKey == b.SyntheticKey``` como sua expressão de junção personalizada.

> [!NOTE]
> Não se esqueça de incluir pelo menos uma coluna de cada lado do relacionamento esquerda e direita de uma união cruzada personalizada. A execução de uniões cruzadas com valores estáticos, em vez de colunas de cada lado, resulta em exames completos de todo o conjunto de dados, fazendo com que o seu fluxo de dados tenha um desempenho inadequado.

## <a name="configuration"></a>Configuração

1. Escolha o fluxo de dados com o qual você está fazendo a junção, na lista suspensa **Fluxo à direita**.
1. Selecione seu **Tipo de junção**
1. Escolha as colunas de chave que serão correspondentes à sua condição de junção. Por padrão, o fluxo de dados procura igualdade entre uma coluna em cada fluxo. Para comparar por meio de um valor calculado, passe o mouse sobre a lista suspensa da coluna e selecione **Coluna computada**.

![Transformação de junção](media/data-flow/join.png "Join")

### <a name="non-equi-joins"></a>Uniões não equivalentes

Para usar um operador condicional como diferente de (!=) ou maior que (>) em suas condições de junção, altere a lista suspensa do operador entre as duas colunas. Uniões não equivalentes exigem que pelo menos um dos dois fluxos sejam transmitidos usando a transmissão **Fixa** na guia **Otimizar**.

![União não equivalente](media/data-flow/non-equi-join.png "União não equivalente")

## <a name="optimizing-join-performance"></a>Otimizando o desempenho da junção

Ao contrário da junção de mesclagem em ferramentas como o SSIS, a transformação de junção não é uma operação de junção de mesclagem obrigatória. As chaves de junção não exigem classificação. A operação de junção ocorre com base na operação de junção ideal no Spark, seja junção de difusão ou do lado do mapa.

![Otimização da transformação de junção](media/data-flow/joinoptimize.png "Otimização de junção")

Em transformação de junções, pesquisas e ocorrências, se um ou ambos os fluxos de dados se ajustarem à memória do nó de trabalho, você poderá otimizar o desempenho habilitando a **Difusão**. Por padrão, o mecanismo do Spark decidirá automaticamente se deseja ou não transmitir um lado. Para escolher manualmente o lado a ser transmitido, selecione **Fixo**.

Não é recomendável desabilitar a transmissão por meio da opção **Desativar**, a menos que suas uniões estejam tendo erros de tempo limite.

## <a name="self-join"></a>Autojunção

Para fazer a autojunção de um fluxo de dados, atribua um alias a um fluxo existente com uma transformação de seleção. Crie um novo branch clicando no ícone de adição ao lado de uma transformação e selecionando **Novo branch**. Adicione uma transformação de seleção para atribuir o alias ao fluxo original. Adicione uma transformação de junção e escolha o fluxo original como **Fluxo à esquerda** e a transformação de seleção como **Fluxo à direita**.

![Autojunção](media/data-flow/selfjoin.png "Autojunção")

## <a name="testing-join-conditions"></a>Testando condições de junção

Ao testar as transformações de junção com a visualização de dados no modo de depuração, use um pequeno conjunto de dados conhecidos. Ao fazer a amostragem de linhas de um grande conjunto de grande, você não consegue prever quais linhas e chaves serão lidas para o teste. O resultado é não determinístico, o que significa que suas condições de junção podem não retornar correspondência alguma.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Exemplo de junção interna

O exemplo abaixo é de uma transformação de junção chamada `JoinMatchedData` que usa `TripData` de fluxo à esquerda e `TripFare` de fluxo à direita.  A condição de junção é a expressão `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` que retorna true quando as colunas `hack_license`, `medallion`, `vendor_id` e `pickup_datetime` de cada fluxo correspondem. O `joinType` é `'inner'`. Estamos habilitando a difusão somente no fluxo à esquerda para que `broadcast` tenha o valor `'left'`.

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Captura de tela mostra a transformação com a guia Configurações de junção selecionada e um tipo de junção interno.](media/data-flow/join-script1.png "Exemplo de junção")

O script de fluxo de dados dessa transformação está no snippet abaixo:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Exemplo de união cruzada personalizada

O exemplo abaixo é de uma transformação de junção chamada `JoiningColumns`, que usa `LeftStream` de fluxo à esquerda e `RightStream` de fluxo à direita. Essa transformação usa dois fluxos e une todas as linhas nas quais a coluna `leftstreamcolumn` é superior à coluna `rightstreamcolumn`. O `joinType` é `cross`. A difusão não habilitada tem `broadcast` com valor `'none'`.

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Captura de tela mostra a transformação com a guia Configurações de junção selecionada e um tipo de junção personalizado (Cruz).](media/data-flow/join-script2.png "Exemplo de junção")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Próximas etapas

Depois de unir os dados, crie uma [coluna derivada](data-flow-derived-column.md) e [colete](data-flow-sink.md) (sink) seus dados em um armazenamento de dados de destino.
