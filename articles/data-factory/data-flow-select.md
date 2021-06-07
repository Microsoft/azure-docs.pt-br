---
title: Selecionar transformação no fluxo de dados de mapeamento
description: Fluxo de dados de mapeamento de Azure Data Factory selecionar transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040233"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selecionar transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação selecionar para renomear, descartar ou reordenar colunas. Essa transformação não altera os dados da linha, mas escolhe quais colunas são propagadas downstream. 

Em uma transformação selecionar, os usuários podem especificar mapeamentos fixos, usar padrões para fazer o mapeamento baseado em regras ou habilitar o mapeamento automático. Os mapeamentos fixos e baseados em regras podem ser usados na mesma transformação SELECT. Se uma coluna não corresponder a um dos mapeamentos definidos, ela será descartada.

## <a name="fixed-mapping"></a>Mapeamento fixo

Se houver menos de 50 colunas definidas em sua projeção, todas as colunas definidas terão um mapeamento fixo por padrão. Um mapeamento fixo usa uma coluna de entrada definida e mapeia um nome exato.

![Mapeamento fixo](media/data-flow/fixedmapping.png "Mapeamento fixo")

> [!NOTE]
> Não é possível mapear ou renomear uma coluna descompasso usando um mapeamento fixo

### <a name="mapping-hierarchical-columns"></a>Mapeando colunas hierárquicas

Mapeamentos fixos podem ser usados para mapear uma subcoluna de uma coluna hierárquica para uma coluna de nível superior. Se você tiver uma hierarquia definida, use a lista suspensa coluna para selecionar uma subcoluna. A transformação selecionar criará uma nova coluna com o valor e o tipo de dados da subcoluna.

![mapeamento hierárquico](media/data-flow/select-hierarchy.png "mapeamento hierárquico")

## <a name="rule-based-mapping"></a>mapeamento baseado em regras


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Se você quiser mapear várias colunas de uma vez ou passar as colunas desfeitas, use o mapeamento baseado em regras para definir seus mapeamentos usando padrões de coluna. Corresponder com base nas `name` `type` colunas,, `stream` e `position` . Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão padronizadas para um mapeamento baseado em regra que corresponde a cada coluna e gera o nome inserido. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regra**.

![Captura de tela mostra o mapeamento baseado em regras selecionado em Adicionar mapeamento.](media/data-flow/rule2.png "mapeamento baseado em regras")

Cada mapeamento baseado em regras requer duas entradas: a condição na qual corresponder e o nome de cada coluna mapeada. Os dois valores são inseridos por meio do [Construtor de expressões](concepts-data-flow-expression-builder.md). Na caixa expressão à esquerda, insira sua condição de correspondência booliana. Na caixa expressão à direita, especifique a qual a coluna correspondente será mapeada.

![Captura de tela mostra um mapeamento.](media/data-flow/rule-based-mapping.png "mapeamento baseado em regras")

Use a `$$` sintaxe para fazer referência ao nome de entrada de uma coluna correspondente. Usando a imagem acima como um exemplo, digamos que um usuário queira corresponder a todas as colunas de cadeia de caracteres cujos nomes tenham menos de seis caracteres. Se uma coluna de entrada tiver sido nomeada `test` , a expressão `$$ + '_short'` renomeará a coluna `test_short` . Se esse for o único mapeamento existente, todas as colunas que não atenderem à condição serão descartadas dos dados de saída.

Padrões correspondem a colunas descompassos e definidas. Para ver quais colunas definidas são mapeadas por uma regra, clique no ícone de óculos ao lado da regra. Verifique a saída usando a visualização de dados.

### <a name="regex-mapping"></a>Mapeamento de Regex

Se você clicar no ícone de divisa inferior, poderá especificar uma condição de mapeamento de Regex. Uma condição de mapeamento Regex corresponde a todos os nomes de coluna que correspondem à condição Regex especificada. Isso pode ser usado em combinação com mapeamentos padrão baseados em regras.

![Captura de tela mostra a condição de mapeamento Regex com correspondências de nível de hierarquia e nome.](media/data-flow/regex-matching.png "mapeamento baseado em regras")

O exemplo acima corresponde ao padrão Regex `(r)` ou a qualquer nome de coluna que contenha um r com letras minúsculas. Semelhante ao mapeamento baseado em regra padrão, todas as colunas correspondentes são alteradas pela condição à direita usando a `$$` sintaxe.

Se você tiver várias correspondências de Regex em seu nome de coluna, poderá consultar correspondências específicas usando `$n` onde ' n' refere-se a qual correspondência. Por exemplo, ' $2 ' refere-se à segunda correspondência dentro de um nome de coluna.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a projeção definida tiver uma hierarquia, você poderá usar o mapeamento baseado em regras para mapear as Subcolunas de hierarquias. Especifique uma condição de correspondência e a coluna complexa cujas Subcolunas você deseja mapear. Todas as Subcolunas correspondentes serão emitidas usando a regra ' nome como ' especificada à direita.

![Captura de tela mostra um mapeamento baseado em regra usando para uma hierarquia.](media/data-flow/rule-based-hierarchy.png "mapeamento baseado em regras")

O exemplo acima corresponde a todas as Subcolunas de coluna complexa `a` . `a` contém duas Subcolunas `b` e `c` . O esquema de saída incluirá duas colunas `b` e `c` como a condição ' nome como ' é `$$` .

### <a name="parameterization"></a>Parametrização

Você pode parametrizar nomes de coluna usando o mapeamento baseado em regra. Use a palavra-chave ```name``` para corresponder nomes de coluna de entrada em um parâmetro. Por exemplo, se você tiver um parâmetro de fluxo de dados ```mycolumn``` , poderá criar uma regra que corresponda a qualquer nome de coluna que seja igual a ```mycolumn``` . Você pode renomear a coluna correspondente para uma cadeia de caracteres embutida em código, como ' chave comercial ' e fazer referência a ela explicitamente. Neste exemplo, a condição de correspondência é ```name == $mycolumn``` e a condição de nome é ' chave comercial '. 

## <a name="auto-mapping"></a>Mapeamento automático

Ao adicionar uma transformação selecionar, o **mapeamento automático** pode ser habilitado alternando o controle deslizante de mapeamento automático. Com o mapeamento automático, a transformação selecionar mapeia todas as colunas de entrada, excluindo duplicatas, com o mesmo nome de sua entrada. Isso incluirá colunas descompassos, o que significa que os dados de saída podem conter colunas não definidas em seu esquema. Para obter mais informações sobre colunas desativadas, consulte [descompasso de esquemas](concepts-data-flow-schema-drift.md).

![Mapeamento automático](media/data-flow/automap.png "Mapeamento automático")

Com o mapeamento automático ativado, a transformação selecionar honrará as configurações de ignorar duplicadas e fornecerá um novo alias para as colunas existentes. A criação de alias é útil ao fazer várias junções ou pesquisas no mesmo fluxo e em cenários de autojunção. 

## <a name="duplicate-columns"></a>Duplicar colunas

Por padrão, a transformação selecionar descarta colunas duplicadas na projeção de entrada e saída. Colunas de entrada duplicadas geralmente são provenientes de transformações de junção e pesquisa, em que os nomes de coluna são duplicados em cada lado da junção. Colunas de saída duplicadas podem ocorrer se você mapear duas colunas de entrada diferentes para o mesmo nome. Escolha se deseja descartar ou passar colunas duplicadas alternando a caixa de seleção.

![Ignorar duplicatas](media/data-flow/select-skip-dup.png "Ignorar duplicatas")

## <a name="ordering-of-columns"></a>Ordenação de colunas

A ordem dos mapeamentos determina a ordem das colunas de saída. Se uma coluna de entrada for mapeada várias vezes, somente o primeiro mapeamento será respeitado. Para qualquer descarte de coluna duplicada, a primeira correspondência será mantida.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Exemplo

Abaixo está um exemplo de um mapeamento Select e seu script de fluxo de dados:

![Selecionar exemplo de script](media/data-flow/select-script-example.png "Selecionar exemplo de script")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Próximas etapas
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
