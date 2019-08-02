---
title: Azure Data Factory mapeando parâmetros de fluxo de dados
description: Saiba como Parametrizar um fluxo de dados de mapeamento de pipelines data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 998f8080509e7ba18ea1a759dff2ed8b8742c910
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253096"
---
# <a name="mapping-data-flow-parameters"></a>Mapeando parâmetros de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

O mapeamento de fluxos de dados no Azure Data Factory dá suporte ao uso de parâmetros. Você pode definir parâmetros dentro de sua definição de fluxo de dados, que você pode usar em todas as expressões. Os valores de parâmetro podem ser definidos pelo pipeline de chamada por meio da atividade executar fluxo de dados. Você tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

* Usar a linguagem de expressão do fluxo de controle de pipeline para definir um valor dinâmico
* Usar a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Use essa capacidade para tornar seus fluxos de dados de uso geral, flexíveis e reutilizáveis. Você pode parametrizar as configurações de fluxo de dados e expressões com esses parâmetros.

> [!NOTE]
> Para usar expressões de fluxo de controle de pipeline, o parâmetro de fluxo de dados deve ser do tipo cadeia de caracteres.

## <a name="create-parameters-in-mapping-data-flow"></a>Criar parâmetros no fluxo de dados de mapeamento

Para adicionar parâmetros ao fluxo de dados, clique na parte em branco da tela fluxo de dados para ver as propriedades gerais. No painel configurações, você verá uma guia chamada "parâmetros". Clique no botão ' novo ' para gerar um novo parâmetro. Para cada parâmetro, você deve atribuir um nome, selecionar um tipo e, opcionalmente, definir um valor padrão.

![Criar parâmetros de fluxo de dados](media/data-flow/create-params.png "Criar parâmetros de fluxo de dados")

Os parâmetros podem ser utilizados em qualquer expressão de fluxo de dados. Os parâmetros começam com $ e são imutáveis. Você encontrará a lista de parâmetros disponíveis no construtor de expressões, na guia ' parâmetros '.

![Expressão de parâmetro de fluxo de dados](media/data-flow/parameter-expression.png "Expressão de parâmetro de fluxo de dados")

## <a name="use-parameters-in-your-data-flow"></a>Usar parâmetros em seu fluxo de dados

* Você pode usar valores de parâmetro dentro de suas expressões de transformação. Você encontrará a lista de parâmetros na guia parâmetros no construtor de expressões. ![Usar parâmetros de fluxo de dados](media/data-flow/params9.png "Usar parâmetros de fluxo de dados")

* Os parâmetros também são usados para configurar valores dinâmicos para suas configurações de transformação de origem e coletor. Ao clicar dentro de campos configuráveis, você verá o link "Adicionar contect dinâmico" aparecerá. Clicar nele levará você para um construtor de expressões, no qual você pode usar parâmetros para usar valores dinâmicos. ![Conteúdo dinâmico do fluxo de dados](media/data-flow/params6.png "Conteúdo dinâmico do fluxo de dados")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Definir parâmetros de fluxo de dados de mapeamento do pipeline

Depois de criar o fluxo de dados com parâmetros, você pode executá-lo de um pipeline com a atividade executar fluxo de dados. Depois de adicionar a atividade à tela do pipeline, você verá os parâmetros de fluxo de dados disponíveis na guia ' parâmetros ' da atividade.

![Definindo um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "Definindo um parâmetro de fluxo de dados")

Se o tipo de dados do parâmetro for cadeia de caracteres, quando você clicar na caixa de texto para definir valores de parâmetro, poderá optar por inserir um pipeline ou uma expressão de fluxo de dados. Se você escolher expressão de pipeline, verá o painel expressão de pipeline. Certifique-se de incluir funções de pipeline dentro da sintaxe `'@{<expression>}'`de interpolação de cadeia de caracteres usando, por exemplo:

```'@{pipeline().RunId}'```

Se o parâmetro não for do tipo cadeia de caracteres, você sempre será apresentado com o construtor de expressões de fluxo de dados. Aqui, você pode inserir qualquer expressão ou valores literais que deseja que correspondam ao tipo de dados do parâmetro. Abaixo estão exemplos de expressão de fluxo de dados e uma cadeia de caracteres literal do construtor de expressões:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de fluxo de dados e pipeline. 

![Exemplo de parâmetros de fluxo de dados](media/data-flow/parameter-example.png "Exemplo de parâmetros de fluxo de dados")



## <a name="next-steps"></a>Próximas etapas
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
