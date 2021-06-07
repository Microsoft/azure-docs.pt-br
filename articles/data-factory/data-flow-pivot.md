---
title: Transformação dinâmica no fluxo de dados de mapeamento
description: Dinamizar dados de linhas em colunas usando a transformação dinâmica do fluxo de dados de mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086652"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformação dinâmica no fluxo de dados de mapeamento


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação dinâmica para criar várias colunas com origem em valores de linha exclusivos de uma única coluna. A dinamização é uma transformação de agregação em que você escolhe colunas agrupar por e gera colunas dinâmicas usando [funções de agregação](data-flow-expression-functions.md#aggregate-functions).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Configuração

A transformação dinâmica requer três entradas diferentes: colunas agrupar por, a chave dinâmica e como gerar as colunas dinamizadas.

### <a name="group-by"></a>Agrupar por

![Agrupar por opções](media/data-flow/pivot2.png "Agrupar por opções")

Selecione as colunas nas quais as colunas dinamizadas devem ser agregadas. Os dados de saída agruparão todas as linhas do mesmo grupo por valores em uma linha. A agregação feita na coluna dinamizada ocorrerá em cada grupo.

Esta seção é opcional. Se nenhuma coluna Agrupar por for selecionada, todo o fluxo de dados será agregado e haverá apenas uma linha na saída.

### <a name="pivot-key"></a>Chave dinâmica

![Chave dinâmica](media/data-flow/pivot3.png "Chave dinâmica")

A chave dinâmica é a coluna cujos valores de linha são dinamizados em novas colunas. Por padrão, a transformação dinâmica criará uma nova coluna para cada valor de linha exclusivo.

Na seção chamada **Valor**, você pode inserir valores de linha específicos a serem dinamizados. Somente os valores de linha inseridos nessa seção serão dinamizados. Habilitar **Null value** (Valor nulo) criará uma coluna dinâmica para os valores nulos da coluna.

### <a name="pivoted-columns"></a>Colunas dinamizadas

![Colunas dinamizadas](media/data-flow/pivot4.png "Colunas dinamizadas")

Para cada valor de chave dinâmica exclusivo que se torna uma coluna, gerar um valor de linha agregado para cada grupo. Você pode criar várias colunas por chave dinâmica. Cada coluna dinâmica deve conter pelo menos uma [função de agregação](data-flow-expression-functions.md#aggregate-functions).

**Padrão de nome de coluna**: selecione como formatar o nome de cada coluna dinâmica. O nome da coluna de saída será uma combinação do valor da chave dinâmica, do prefixo da coluna e do prefixo opcional, do sufixo e caracteres do meio. 

**Disposição da coluna:** se você gerar mais de uma coluna dinâmica por chave dinâmica, escolha como deseja que as colunas sejam ordenadas. 

**Prefixo da coluna:** se você gerar mais de uma coluna dinâmica por chave dinâmica, insira um prefixo de coluna para cada coluna. Essa configuração será opcional se você tiver apenas uma coluna dinâmica.

## <a name="help-graphic"></a>Gráfico de ajuda

O gráfico de ajuda abaixo mostra como os diferentes componentes dinâmicos interagem uns com os outros

![Dinamizar gráficos de ajuda](media/data-flow/pivot5.png "Dinamizar gráfico de ajuda")

## <a name="pivot-metadata"></a>Metadados dinâmicos

Se nenhum valor for especificado na configuração da chave dinâmica, as colunas dinamizadas serão geradas dinamicamente em tempo de execução. O número de colunas dinamizadas será igual ao número de valores de chave dinâmica exclusivos multiplicado pelo número de colunas dinâmicas. Como esse pode ser um número variável, o UX não exibirá os metadados da coluna na guia **Inspect** e não haverá propagação de coluna. Para fazer a transformação dessas colunas, use os recursos de [padrão de coluna](concepts-data-flow-column-pattern.md) do fluxo de dados de mapeamento. 

Se valores de chave dinâmica específicos forem definidos, as colunas dinamizadas serão exibidas nos metadados. Os nomes de coluna estarão disponíveis para você no mapeamento Inspect e Sink.

### <a name="generate-metadata-from-drifted-columns"></a>Gerar metadados de colunas descompassadas

A dinamização gera novos nomes de coluna dinamicamente com base em valores de linha. Você pode adicionar essas novas colunas aos metadados que podem ser referenciadas posteriormente no fluxo de dados. Para isso, use a ação rápida [map drifted](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) (mapa descompassado) na visualização de dados. 

![Dinamizar colunas](media/data-flow/newpivot1.png "Mapear colunas dinâmicas descompassadas")

### <a name="sinking-pivoted-columns"></a>Colunas dinamizadas no coletor

Embora as colunas dinamizadas sejam dinâmicas, elas ainda podem ser gravadas no seu armazenamento de dados de destino. Habilite **Allow schema drift** (Permitir descompasso de esquema) nas configurações do coletor. Isso permitirá que você grave colunas que não estão incluídas nos metadados. Você não verá os novos nomes dinâmicos nos metadados de coluna, mas a opção de descompasso de esquema permitirá que você descarregue os dados.

### <a name="rejoin-original-fields"></a>Reingressar nos campos originais

A transformação dinâmica projetará apenas as colunas agrupar por e dinamizadas. Para que os dados de saída incluam outras colunas de entrada, use um padrão de [autojunção](data-flow-join.md#self-join).

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Exemplo

As telas mostradas na seção de configuração têm o seguinte script de fluxo de dados:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Próximas etapas

Experimente a [transformação de colunas em linhas](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
