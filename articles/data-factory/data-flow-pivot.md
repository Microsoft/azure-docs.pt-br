---
title: Transformação dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Dados de linhas para colunas usando o Azure Data Factory mapeamento fluxo Pivot transformação de dados de tabela dinâmica
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348398"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação de dinâmica de fábrica de dados do Azure
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use Dinamizar no Fluxo de Dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Basicamente, você pode dinamizar os valores de linha em novas colunas (transformar dados em metadados).

![Opções de dinamização](media/data-flow/pivot1.png "dinamização 1")

## <a name="group-by"></a>Agrupar por

![Opções de dinamização](media/data-flow/pivot2.png "dinamização 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de dinamização. Você pode definir mais de uma coluna aqui com o sinal + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave dinâmica

![Opções de dinamização](media/data-flow/pivot3.png "dinamização 3")

A Chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar em valores de coluna. Esta é a coluna que determina as novas colunas que serão criadas.

## <a name="pivoted-columns"></a>Colunas dinâmicas

![Opções de dinamização](media/data-flow/pivot4.png "dinamização 4")

Por fim, você escolherá a agregação que deseja usar para os valores dinamizados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, dinamizar “Vendas” por “Região” resultaria em novos valores de coluna de cada valor de vendas, ou seja, "25", "50", "1000", etc. No entanto, se você definir um valor de prefixo de vendas "-", cada valor de coluna adicionaria "vendas-" para o início do valor.

![Opções de dinamização](media/data-flow/pivot5.png "dinamização 5")

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinamizadas com seus valores agregados. Definir a disposição das colunas como “Lateral” causará a alternância entre coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que você deseja usar para os valores de dinamização, clique no campo na parte inferior do painel Colunas Dinamizadas. Você entrará no Construtor de Expressões de Fluxo de Dados do ADF, no qual poderá criar uma expressão de agregação e fornecer um nome de alias descritivo a seus novos valores de agregação.

Use a Linguagem de Expressão do Fluxo de Dados do ADF para descrever as transformações da coluna dinamizada no Construtor de Expressões: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Metadados de tabela dinâmica

A transformação dinâmica produzirá os novos nomes de coluna são dinâmicos com base nos dados de entrada. A chave dinâmica produz os valores para cada novo nome de coluna. Se você não especificar valores individuais e deseja criar nomes de coluna dinâmica para cada valor exclusivo na sua chave dinâmica, em seguida, a interface do usuário não exibirá os metadados em inspecionar e não haverá nenhuma propagação de coluna para a transformação de coletor. Se você definir valores para a chave dinâmica, o ADF pode determinar os novos nomes de coluna e esses nomes de coluna serão disponíveis na inspecionar e mapeamento do coletor.

### <a name="landing-new-columns-in-sink"></a>Novas colunas no coletor de aterrissagem

Mesmo com nomes de coluna dinâmica no Pivot, você ainda pode coletar seus novos nomes de coluna e valores no seu armazenamento de destino. Basta defina "Permitir que o descompasso do esquema" como nas configurações do coletor. Você não verá os novos nomes dinâmicos em seus metadados de coluna, mas a opção de descompasso do esquema permitirá que você descarregar os dados.

### <a name="view-metadata-in-design-mode"></a>Metadados de exibição no modo de design

Se você deseja exibir os novos nomes de coluna como metadados no inspecionar e você deseja ver as colunas propagar explicitamente para a transformação de coletor, em seguida, defina valores explícitos na guia de chave dinâmica.

### <a name="how-to-rejoin-original-fields"></a>Como reingressar nos campos originais
A transformação dinâmica só projetará as colunas usadas nas ações de agregação, agrupamento e dinamização. Se você quiser incluir outras colunas da etapa anterior em seu fluxo, use um novo Branch da etapa anterior e usar o padrão de associação a mesmo para conectar o fluxo com os metadados originais.

## <a name="next-steps"></a>Próximas etapas

Experimente o [transformação não dinâmica](data-flow-unpivot.md) para transformar os valores de coluna em valores de linha. 
