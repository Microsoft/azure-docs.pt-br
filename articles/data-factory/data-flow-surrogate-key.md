---
title: Transformação de chave substituta no fluxo de dados de mapeamento
description: Como usar a transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory para gerar valores de chave sequencial
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: d1f8993b1adc297b1bfadba114df76a66e59afa2
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147157"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Transformação de chave substituta no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação chave substituta para adicionar um valor de chave de incremento a cada linha de dados. Isso é útil ao criar tabelas de dimensões em um modelo de dados analíticos de esquema em estrela. Em um esquema em estrela, cada membro em suas tabelas de dimensões requer uma chave exclusiva que é uma chave não comercial.

## <a name="configuration"></a>Configuração

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

**Coluna de chave:** O nome da coluna de chave substituta gerada.

**Valor inicial:** O valor de chave mais baixo que será gerado.

## <a name="increment-keys-from-existing-sources"></a>Incrementar chaves de fontes existentes

Para iniciar sua sequência a partir de um valor que existe em uma fonte, é recomendável usar um coletor de cache para salvar esse valor e usar uma transformação coluna derivada para adicionar os dois valores juntos. Use uma pesquisa armazenada em cache para obter a saída e acrescentá-la à chave gerada. Para obter mais informações, saiba mais sobre [coletores de cache](data-flow-sink.md#cache-sink) e [pesquisas em cache](concepts-data-flow-expression-builder.md#cached-lookup).

![Pesquisa de chave substituta](media/data-flow/cached-lookup-example.png "Pesquisa de chave substituta")

### <a name="increment-from-existing-maximum-value"></a>Incrementar a partir do valor máximo existente

Para propagar o valor de chave com o máximo anterior, há duas técnicas que você pode usar com base em onde estão os dados de origem.

#### <a name="database-sources"></a>Fontes de banco de dados

Use uma opção de consulta SQL para selecionar MAX () de sua origem. Por exemplo, `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`.

![Consulta de chave substituta](media/data-flow/surrogate-key-max-database.png "Consulta de transformação chave substituta")

#### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior estiver em um arquivo, use a `max()` função na transformação agregação para obter o valor máximo anterior:

![Arquivo de chave substituta](media/data-flow/surrogate-key-max-file.png "Arquivo de chave substituta")

Em ambos os casos, será necessário gravar em um coletor de cache e pesquisar o valor. 


## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Exemplo

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

O script de fluxo de dados para a configuração de chave substituta acima está no trecho de código abaixo.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam as transformações de [junção](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) .
