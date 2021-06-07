---
title: Padrões de coluna no fluxo de dados de mapeamento Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando padrões de coluna em fluxos de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844992"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Várias transformações de fluxo de dados de mapeamento permitem que você referencie colunas de modelo com base em padrões em vez de nomes de coluna embutidos em código. Essa correspondência é conhecida como *padrões de coluna*. Você pode definir padrões para corresponder colunas com base no nome, tipo de dados, fluxo, origem ou posição, em vez de exigir nomes de campo exatos. Há dois cenários em que os padrões de coluna são úteis:

* Se os campos de origem de entrada forem alterados com frequência, como o caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Esse cenário é conhecido como [descompasso de esquema](concepts-data-flow-schema-drift.md).
* Se você quiser fazer uma operação comum em um grande grupo de colunas. Por exemplo, deseja converter cada coluna que tem ' total ' em seu nome de coluna em um duplo.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregação

Para adicionar um padrão de coluna em uma transformação coluna, agregação ou janela derivada, clique em **Adicionar** acima da lista de colunas ou no ícone de adição ao lado de uma coluna derivada existente. Escolha o **padrão adicionar coluna**.

![Captura de tela mostra o ícone de adição para adicionar o padrão de coluna.](media/data-flow/add-column-pattern.png "Padrões de coluna")

Use o [Construtor de expressões](concepts-data-flow-expression-builder.md) para inserir a condição de correspondência. Crie uma expressão booliana que corresponda a colunas com base em `name` ,, `type` `stream` , `origin` e `position` da coluna. O padrão afetará qualquer coluna, descompasso ou definida, em que a condição retorna true.

As duas caixas de expressões abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Use `$$` para fazer referência ao valor existente do campo correspondente. A caixa expressão à esquerda define o nome e a caixa de expressão direita define o valor.

![Captura de tela mostra a guia Configurações da coluna derivada.](media/data-flow/edit-column-pattern.png "Padrões de coluna")

O padrão de coluna acima corresponde a cada coluna do tipo Double e cria uma coluna derivada por correspondência. Ao ser declarada `$$` como o campo nome da coluna, cada coluna correspondente é atualizada com o mesmo nome. O valor de cada coluna é o valor existente arredondado para dois pontos decimais.

Para verificar se a condição de correspondência está correta, você pode validar o esquema de saída das colunas definidas na guia **inspecionar** ou obter um instantâneo dos dados na guia **visualização de dados** . 

![Captura de tela mostra a guia esquema de saída.](media/data-flow/columnpattern3.png "Padrões de coluna")

### <a name="hierarchical-pattern-matching"></a>Correspondência de padrões hierárquicos

Você também pode criar correspondência de padrões dentro de estruturas hierárquicas complexas. Expanda a seção `Each MoviesStruct that matches` em que você será solicitado a fornecer cada hierarquia em seu fluxo de dados. Em seguida, você pode criar padrões correspondentes para propriedades dentro dessa hierarquia escolhida.

![Captura de tela mostra padrão hierárquico de coluna.](media/data-flow/patterns-hierarchy.png "Padrões de coluna em hierarquias")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em Select e Sink

Ao mapear colunas na origem e selecionar transformações, você pode adicionar mapeamento fixo ou mapeamentos baseados em regras. Corresponder com base nas `name` `type` colunas,, `stream` , `origin` e `position` . Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão padronizadas para um mapeamento baseado em regra que corresponde a cada coluna e gera o nome inserido. 

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

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a projeção definida tiver uma hierarquia, você poderá usar o mapeamento baseado em regras para mapear as Subcolunas de hierarquias. Especifique uma condição de correspondência e a coluna complexa cujas Subcolunas você deseja mapear. Todas as Subcolunas correspondentes serão emitidas usando a regra ' nome como ' especificada à direita.

![Captura de tela mostra um mapeamento baseado em regra usando para uma hierarquia.](media/data-flow/rule-based-hierarchy.png "mapeamento baseado em regras")

O exemplo acima corresponde a todas as Subcolunas de coluna complexa `a` . `a` contém duas Subcolunas `b` e `c` . O esquema de saída incluirá duas colunas `b` e `c` como a condição ' nome como ' é `$$` .

## <a name="pattern-matching-expression-values"></a>Valores de expressão correspondentes de padrões.

* `$$` traduz o nome ou valor de cada correspondência em tempo de execução. Imagine `$$` como equivalente a `this` .
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo ou transformação em seu fluxo
* `position` é a posição ordinal de colunas em seu fluxo de dados
* `origin` a transformação em que uma coluna foi originada ou foi atualizada pela última vez

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [linguagem de expressão](data-flow-expression-functions.md) de fluxo de dados de mapeamento para transformações de dados
* Usar padrões de coluna na [transformação do coletor](data-flow-sink.md) e [selecionar a transformação](data-flow-select.md) com mapeamento baseado em regras
