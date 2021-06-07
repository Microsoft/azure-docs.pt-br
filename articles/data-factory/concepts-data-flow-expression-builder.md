---
title: Construtor de expressões no fluxo de dados de mapeamento
description: Criar expressões usando o construtor de expressões no mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584916"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Criar expressões no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

No fluxo de dados de mapeamento, muitas propriedades de transformação são inseridas como expressões. Essas expressões são compostas por valores de coluna, parâmetros, funções, operadores e literais que são avaliados como um tipo de dados do Spark em tempo de execução. O mapeamento de fluxos de dados tem uma experiência dedicada para ajudá-lo a criar essas expressões chamadas de **Construtor de expressões**. Utilizando a conclusão de código do  [IntelliSense](/visualstudio/ide/using-intellisense) para realce, verificação de sintaxe e preenchimento automático, o construtor de expressões foi projetado para facilitar a criação de fluxos de dados. Este artigo explica como usar o construtor de expressões para criar efetivamente sua lógica de negócios.

![Construtor de Expressões](media/data-flow/expresion-builder.png "Construtor de Expressões")

## <a name="open-expression-builder"></a>Abrir o construtor de expressões

Há vários pontos de entrada para abrir o construtor de expressões. Todos eles dependem do contexto específico da transformação fluxo de dados. O caso de uso mais comum está em transformações, como [coluna derivada](data-flow-derived-column.md) e [agregação](data-flow-aggregate.md) , onde os usuários criam ou atualizam colunas usando a linguagem de expressão de fluxo de dados. O construtor de expressões pode ser aberto selecionando **abrir Construtor de expressões** acima da lista de colunas. Você também pode clicar em um contexto de coluna e abrir o construtor de expressões diretamente para essa expressão.

![Abrir Construtor de expressões derive](media/data-flow/open-expression-builder-derive.png "Abrir Construtor de expressões derive")

Em algumas transformações como [filtro](data-flow-filter.md), clicar em uma caixa de texto expressão azul abrirá o construtor de expressões. 

![Caixa de expressão azul](media/data-flow/expressionbox.png "Caixa de expressão azul")

Quando você faz referência a colunas em uma condição de correspondência ou agrupamento, uma expressão pode extrair valores de colunas. Para criar uma expressão, selecione **coluna computada**.

![Opção de coluna computada](media/data-flow/computedcolumn.png "Opção de coluna computada")

Nos casos em que uma expressão ou um valor literal são entradas válidas, selecione **adicionar conteúdo dinâmico** para criar uma expressão que seja avaliada como um valor literal.

![Opção adicionar conteúdo dinâmico](media/data-flow/add-dynamic-content.png "Opção adicionar conteúdo dinâmico")

## <a name="expression-elements"></a>Elementos de expressão

No mapeamento de fluxos de dados, as expressões podem ser compostas por valores de coluna, parâmetros, funções, variáveis locais, operadores e literais. Essas expressões devem ser avaliadas como um tipo de dados do Spark, como String, Boolean ou Integer.

![Elementos de expressão](media/data-flow/expression-elements.png "Elementos de expressão")

### <a name="functions"></a>Funções

O mapeamento de fluxos de dados tem funções e operadores internos que podem ser usados em expressões. Para obter uma lista de funções disponíveis, consulte a [referência de linguagem de fluxo de dados de mapeamento](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Endereçar índices de matriz

Ao lidar com colunas ou funções que retornam tipos de matriz, use colchetes ([]) para acessar um elemento específico. Se o índice não existir, a expressão será avaliada como NULL.

![Matriz do construtor de expressões](media/data-flow/expression-array.png "Visualização de dados de expressão")

> [!IMPORTANT]
> No mapeamento de fluxos de dados, as matrizes são baseadas em um, o que significa que o primeiro elemento é referenciado pelo índice um. Por exemplo, myArray [1] acessará o primeiro elemento de uma matriz chamada ' myArray '.

### <a name="input-schema"></a>Esquema de entrada

Se o fluxo de dados usar um esquema definido em qualquer uma de suas fontes, você poderá fazer referência a uma coluna por nome em muitas expressões. Se você estiver utilizando descompasso de esquema, poderá fazer referência a colunas explicitamente usando as `byName()` `byNames()` funções ou ou coincidir usando padrões de coluna.

#### <a name="column-names-with-special-characters"></a>Nomes de coluna com caracteres especiais

Quando você tem nomes de coluna que incluem caracteres especiais ou espaços, coloque o nome entre chaves para fazer referência a eles em uma expressão.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parâmetros

Parâmetros são valores que são passados para um fluxo de dados em tempo de execução de um pipeline. Para fazer referência a um parâmetro, clique no parâmetro na exibição de **elementos de expressão** ou faça referência a ele com um sinal de dólar na frente de seu nome. Por exemplo, um parâmetro chamado parâmetro1 seria referenciado por `$parameter1` . Para saber mais, consulte [parametrizando o mapeamento de fluxos de dados](parameters-data-flow.md).

### <a name="cached-lookup"></a>Pesquisa armazenada em cache

Uma pesquisa armazenada em cache permite que você faça uma pesquisa embutida da saída de um coletor armazenado em cache. Há duas funções disponíveis para uso em cada coletor `lookup()` e `outputs()` . A sintaxe para fazer referência a essas funções é `cacheSinkName#functionName()` . Para obter mais informações, consulte [coletores de cache](data-flow-sink.md#cache-sink).

`lookup()` usa as colunas correspondentes na transformação atual como parâmetros e retorna uma coluna complexa igual à linha que corresponde às colunas de chave no coletor de cache. A coluna complexa retornada contém uma subcoluna para cada coluna mapeada no coletor de cache. Por exemplo, se você tivesse um coletor de código de erro de cache `errorCodeCache` que tinha uma coluna de chave correspondente no código e uma coluna chamada `Message` . Chamar `errorCodeCache#lookup(errorCode).Message` retornaria a mensagem correspondente ao código passado. 

`outputs()` Não usa parâmetros e retorna o coletor de cache inteiro como uma matriz de colunas complexas. Isso não poderá ser chamado se as colunas de chave forem especificadas no coletor e só deverão ser usadas se houver um pequeno número de linhas no coletor de cache. Um caso de uso comum é acrescentar o valor máximo de uma chave de incremento. Se uma única linha agregada armazenada em cache `CacheMaxKey` contiver uma coluna `MaxKey` , você poderá referenciar o primeiro valor chamando `CacheMaxKey#outputs()[1].MaxKey` .

![Pesquisa armazenada em cache](media/data-flow/cached-lookup-example.png "Pesquisa armazenada em cache")

### <a name="locals"></a>Locais

Se você estiver compartilhando a lógica em várias colunas ou quiser criar uma compartimentalização da lógica, você poderá criá-la em um column\. derivado Para fazer referência a um local, clique no local na exibição de **elementos de expressão** ou faça referência a ele com dois-pontos na frente de seu nome. Por exemplo, um local chamado local1 seria referenciado por `:local1` . Saiba mais sobre locais na [documentação da coluna derivada](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Resultados da expressão de visualização

Se o [modo de depuração](concepts-data-flow-debug-mode.md) for ativado, você poderá usar interativamente o cluster de depuração para visualizar o que a expressão avalia. Selecione **Atualizar** ao lado de visualização de dados para atualizar os resultados da visualização de dados. Você pode ver a saída de cada linha de acordo com as colunas de entrada.

![Visualização em andamento](media/data-flow/preview-expression.png "Visualização de dados de expressão")

## <a name="string-interpolation"></a>Interpolação de cadeia de caracteres

Ao criar cadeias de caracteres longas que usam elementos de expressão, use a interpolação de cadeias para criar facilmente uma lógica de cadeia de caracteres complexa. A interpolação de cadeia de caracteres evita o uso extensivo da concatenação de cadeia de caracteres quando os parâmetros são incluídos em cadeias de consulta Use aspas duplas para colocar o texto da cadeia de caracteres literal junto com expressões. Você pode incluir funções, colunas e parâmetros de expressão. Para usar a sintaxe de expressão, coloque-a entre chaves,

Alguns exemplos de interpolação de cadeia de caracteres:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> Ao usar a sintaxe de interpolação de cadeia de caracteres em consultas de origem do SQL, a cadeia de caracteres de consulta deve estar em uma única linha, sem '/n '.

## <a name="commenting-expressions"></a>Expressões de comentário

Adicione comentários às suas expressões usando a sintaxe de linha única e de comentário multilinha.

Os exemplos a seguir são comentários válidos:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Se você colocar um comentário na parte superior da expressão, ele aparecerá na caixa de texto transformação para documentar suas expressões de transformação.

![Comentário na caixa de texto transformação](media/data-flow/comment-expression.png "Comentários")

## <a name="regular-expressions"></a>Expressões regulares

Muitas funções de linguagem de expressão usam sintaxe de expressão regular. Quando você usa funções de expressão regular, o construtor de expressões tenta interpretar uma barra invertida ( \\ ) como uma sequência de caracteres de escape. Quando você usa barras invertidas em sua expressão regular, coloque todo o Regex em backtiques ( \` ) ou use uma barra invertida dupla.

Um exemplo que usa marcas de escala:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Um exemplo que usa barras duplas:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>Atalhos de teclado

Veja abaixo uma lista de atalhos disponíveis no construtor de expressões. A maioria dos atalhos do IntelliSense está disponível durante a criação de expressões.

* CTRL + K CTRL + C: comentar linha inteira.
* CTRL + K Ctrl + U: remover marca de comentário.
* F1: fornecer comandos de ajuda do editor.
* ALT + tecla de seta para baixo: mover para baixo na linha atual.
* ALT + tecla de seta para cima: mover para cima na linha atual.
* CTRL + barra de espaços: mostra a ajuda de contexto.

## <a name="commonly-used-expressions"></a>Expressões comumente usadas

### <a name="convert-to-dates-or-timestamps"></a>Converter em datas ou carimbos de data/hora

Para incluir literais de cadeia de caracteres na saída do carimbo de data/hora, empacote a conversão em ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Para converter milissegundos de época em uma data ou timestamp, use `toTimestamp(<number of milliseconds>)` . Se o tempo for lançado em segundos, multiplique por 1.000.

```toTimestamp(1574127407*1000l)```

O "l" à direita no final da expressão anterior significa conversão para um tipo longo como sintaxe embutida.

### <a name="find-time-from-epoch-or-unix-time"></a>Localizar tempo da época ou do horário do UNIX

tolong (currentTimestamp ()-notimestamp (' 1970-01-01 00:00:00.000 ', ' AAAA-MM-DD HH: mm: SS. SSS ')) * 1000L

## <a name="next-steps"></a>Próximas etapas

[Iniciar a criação de expressões de transformação de dados](data-flow-expression-functions.md)
