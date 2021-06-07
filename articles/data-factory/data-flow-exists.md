---
title: Existe transformação no fluxo de dados de mapeamento
description: Verificar as linhas existentes usando a transformação Exists no fluxo de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82982625"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação Exists é uma transformação de filtragem de linha que verifica se os dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo à esquerda que existem ou não existem no fluxo correto. A transformação Exists é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuração

1. Escolha qual fluxo de dados você está verificando a existência no menu suspenso de **fluxo à direita** .
1. Especifique se você está procurando os dados existentes ou não existentes na configuração de **tipo exist** .
1. Selecione se deseja ou não uma **expressão personalizada**.
1. Escolha quais colunas de chave você deseja comparar como suas condições de existência. Por padrão, o fluxo de dados procura igualdade entre uma coluna em cada fluxo. Para comparar por meio de um valor calculado, passe o mouse sobre a lista suspensa da coluna e selecione **Coluna computada**.

![Configurações existentes](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Várias condições existentes

Para comparar várias colunas de cada fluxo, adicione uma nova condição EXISTS clicando no ícone de adição ao lado de uma linha existente. Cada condição adicional é unida por uma instrução "and". Comparar duas colunas é o mesmo que a seguinte expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores diferentes de "and" e "Equals to", selecione o campo **expressão personalizada** . Insira uma expressão personalizada por meio do construtor de expressões de fluxo de dados clicando na caixa azul.

![Existe configurações personalizadas](media/data-flow/exists1.png "existe personalizado")

## <a name="broadcast-optimization"></a>Otimização de transmissão

![União de transmissão](media/data-flow/broadcast.png "União de transmissão")

Em transformação de junções, pesquisas e ocorrências, se um ou ambos os fluxos de dados se ajustarem à memória do nó de trabalho, você poderá otimizar o desempenho habilitando a **Difusão**. Por padrão, o mecanismo do Spark decidirá automaticamente se deseja ou não transmitir um lado. Para escolher manualmente o lado a ser transmitido, selecione **Fixo**.

Não é recomendável desabilitar a transmissão por meio da opção **Desativar**, a menos que suas uniões estejam tendo erros de tempo limite.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação Exists chamada `checkForChanges` que usa fluxo à esquerda `NameNorm2` e fluxo à direita `TypeConversions` .  A condição EXISTS é a expressão `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` que retornará true se as `EMPID` `Region` colunas e em cada fluxo corresponderem. Como estamos verificando a existência, `negate` é false. Não estamos habilitando nenhuma difusão na guia otimizar, portanto, `broadcast` tem valor `'none'` .

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Exemplo de Exists](media/data-flow/exists-script.png "Exemplo de Exists")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Próximas etapas

As transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
