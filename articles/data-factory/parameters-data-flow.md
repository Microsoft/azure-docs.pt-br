---
title: Parametrizando os fluxos de dados de mapeamento
description: Saiba como Parametrizar um fluxo de dados de mapeamento de pipelines data factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040744"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizando os fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

O mapeamento de fluxos de dados no Azure Data Factory dá suporte ao uso de parâmetros. Defina parâmetros dentro de sua definição de fluxo de dados e use-os em suas expressões. Os valores de parâmetro são definidos pelo pipeline de chamada por meio da atividade executar fluxo de dados. Você tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

* Usar a linguagem de expressão do fluxo de controle de pipeline para definir um valor dinâmico
* Usar a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Use essa capacidade para tornar seus fluxos de dados de uso geral, flexíveis e reutilizáveis. Você pode parametrizar as configurações de fluxo de dados e expressões com esses parâmetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Criar parâmetros em um fluxo de dados de mapeamento

Para adicionar parâmetros ao fluxo de dados, clique na parte em branco da tela fluxo de dados para ver as propriedades gerais. No painel configurações, você verá uma guia chamada **parâmetro**. Selecione **novo** para gerar um novo parâmetro. Para cada parâmetro, você deve atribuir um nome, selecionar um tipo e, opcionalmente, definir um valor padrão.

![Criar parâmetros de fluxo de dados](media/data-flow/create-params.png "Criar parâmetros de fluxo de dados")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Usar parâmetros em um fluxo de dados de mapeamento 

Os parâmetros podem ser referenciados em qualquer expressão de fluxo de dados. Os parâmetros começam com $ e são imutáveis. Você encontrará a lista de parâmetros disponíveis no construtor de expressões, na guia **parâmetros** .

![Captura de tela mostra os parâmetros disponíveis na guia parâmetros.](media/data-flow/parameter-expression.png "Expressão de parâmetro de fluxo de dados")

Você pode adicionar parâmetros adicionais rapidamente selecionando **novo parâmetro** e especificando o nome e o tipo.

![Captura de tela mostra os parâmetros na guia parâmetros com novos parâmetros adicionados.](media/data-flow/new-parameter-expression.png "Expressão de parâmetro de fluxo de dados")

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetro de um pipeline

Depois de criar um fluxo de dados com parâmetros, você pode executá-lo de um pipeline com a atividade executar fluxo de dados. Depois de adicionar a atividade à tela do pipeline, você verá os parâmetros de fluxo de dados disponíveis na guia **parâmetros** da atividade.

Ao atribuir valores de parâmetro, você pode usar a linguagem de [expressão de pipeline](control-flow-expression-language-functions.md) ou a [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md) com base em tipos do Spark. Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de fluxo de dados e pipeline.

![Captura de tela mostra a guia parâmetros com a expressão de fluxo de dados selecionada para o valor de MyParam.](media/data-flow/parameter-assign.png "Definindo um parâmetro de fluxo de dados")

### <a name="pipeline-expression-parameters"></a>Parâmetros de expressão de pipeline

Os parâmetros de expressão de pipeline permitem que você referencie variáveis de sistema, funções, parâmetros de pipeline e variáveis semelhantes a outras atividades de pipeline. Quando você clicar em **expressão de pipeline**, uma navegação lateral será aberta permitindo que você insira uma expressão usando o construtor de expressões.

![Captura de tela mostra o painel Construtor de expressões.](media/data-flow/parameter-pipeline.png "Definindo um parâmetro de fluxo de dados")

Quando referenciados, os parâmetros de pipeline são avaliados e seu valor é usado na linguagem de expressão de fluxo de dados. O tipo de expressão de pipeline não precisa corresponder ao tipo de parâmetro de fluxo de dados. 

#### <a name="string-literals-vs-expressions"></a>Cadeias de caracteres literais vs expressões

Ao atribuir um parâmetro de expressão de pipeline do tipo cadeia de caracteres, por padrão, as aspas serão adicionadas e o valor será avaliado como um literal. Para ler o valor do parâmetro como uma expressão de fluxo de dados, marque a caixa expressão ao lado do parâmetro.

![Captura de tela mostra a expressão do painel de parâmetros de fluxo de dados selecionado para um parâmetro.](media/data-flow/string-parameter.png "Definindo um parâmetro de fluxo de dados")

Se `stringParam` o parâmetro de fluxo de dados fizer referência a um parâmetro de pipeline com valor `upper(column1)` . 

- Se expression estiver marcado, será `$stringParam` avaliado como o valor de Coluna1 todas as letras maiúsculas.
- Se a expressão não estiver marcada (comportamento padrão), será  `$stringParam` avaliada como `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Passando carimbos de data/hora

Na linguagem de expressão de pipeline, variáveis de sistema como `pipeline().TriggerTime` e Functions como `utcNow()` retornam carimbos de data/hora como cadeias de caracteres no formato ' aaaa-mm-dd \' T \' hh: mm: SS. SSSSSSZ'. Para convertê-los em parâmetros de fluxo de dados do tipo TIMESTAMP, use interpolação de cadeia de caracteres para incluir o carimbo de data/hora desejado em uma `toTimestamp()` função Por exemplo, para converter o tempo de gatilho do pipeline em um parâmetro de fluxo de dados, você pode usar `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Captura de tela mostra a guia parâmetros, na qual você pode inserir um tempo de gatilho.](media/data-flow/parameter-timestamp.png "Definindo um parâmetro de fluxo de dados")

> [!NOTE]
> Os fluxos de dados só podem dar suporte a até 3 dígitos de milissegundos. A `left()` função é usada para cortar dígitos adicionais.

#### <a name="pipeline-parameter-example"></a>Exemplo de parâmetro de pipeline

Digamos que você tenha um parâmetro inteiro `intParam` que faça referência a um parâmetro de pipeline do tipo cadeia de caracteres, `@pipeline.parameters.pipelineParam` . 

![Captura de tela mostra a guia parâmetros com parâmetros chamados stringParam e intParam.](media/data-flow/parameter-pipeline-2.png "Definindo um parâmetro de fluxo de dados")

`@pipeline.parameters.pipelineParam` é atribuído um valor de `abs(1)` em tempo de execução.

![Captura de tela mostra a guia parâmetros com o valor de b s (1) selecionado.](media/data-flow/parameter-pipeline-4.png "Definindo um parâmetro de fluxo de dados")

Quando `$intParam` é referenciado em uma expressão como uma coluna derivada, ele avaliará o `abs(1)` retorno `1` . 

![Captura de tela mostra o valor das colunas.](media/data-flow/parameter-pipeline-3.png "Definindo um parâmetro de fluxo de dados")

### <a name="data-flow-expression-parameters"></a>Parâmetros de expressão de fluxo de dados

A **expressão selecionar fluxo de dados** abrirá o construtor de expressões de fluxo de dados. Você poderá referenciar funções, outros parâmetros e qualquer coluna de esquema definida em todo o fluxo de dados. Esta expressão será avaliada como está quando referenciada.

> [!NOTE]
> Se você passar uma expressão inválida ou fizer referência a uma coluna de esquema que não existe nessa transformação, o parâmetro será avaliado como NULL.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando um nome de coluna como um parâmetro

Um padrão comum é passar um nome de coluna como um valor de parâmetro. Se a coluna for definida no esquema de fluxo de dados, você poderá referenciá-la diretamente como uma expressão de cadeia de caracteres. Se a coluna não estiver definida no esquema, use a `byName()` função. Lembre-se de converter a coluna em seu tipo apropriado com uma função de conversão, como `toString()` .

Por exemplo, se você quisesse mapear uma coluna de cadeia de caracteres com base em um parâmetro `columnName` , poderá adicionar uma transformação coluna derivada igual a `toString(byName($columnName))` .

![Passando um nome de coluna como um parâmetro](media/data-flow/parameterize-column-name.png "Passando um nome de coluna como um parâmetro")

## <a name="next-steps"></a>Próximas etapas
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
