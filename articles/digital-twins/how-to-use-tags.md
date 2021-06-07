---
title: Adicionar marcas ao gêmeos digital
titleSuffix: Azure Digital Twins
description: Veja como implementar marcas no digital gêmeos
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 70bf46de072a97eca810dda60a5331df14172ed6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555148"
---
# <a name="add-tags-to-digital-twins"></a>Adicionar marcas ao gêmeos digital 

Você pode usar o conceito de marcas para identificar e categorizar ainda mais seu gêmeos digital. Em particular, os usuários podem querer replicar marcas de sistemas existentes, como [marcas de palheiro](https://project-haystack.org/doc/TagModel), em suas instâncias de gêmeos digitais do Azure. 

Este documento descreve os padrões que podem ser usados para implementar marcas no digital gêmeos.

As marcas são adicionadas pela primeira vez como propriedades dentro do [modelo](concepts-models.md) que descrevem uma teledigital. Essa propriedade é, então, definida no conjunto de entrelaçamento quando ela é criada com base no modelo. Depois disso, as marcas podem ser usadas em [consultas](concepts-query-language.md) para identificar e filtrar seu gêmeos.

## <a name="marker-tags"></a>Marcas de marcador 

Uma **marca de marcador** é uma cadeia de caracteres simples que é usada para marcar ou categorizar um texto digital, como "azul" ou "vermelho". Essa cadeia de caracteres é o nome da marca e as marcas de marcador não têm nenhum valor significativo — a marca é significativa apenas por sua presença (ou ausência). 

### <a name="add-marker-tags-to-model"></a>Adicionar marcas de marcador ao modelo 

Marcas de marcador são modeladas como um mapa de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` para `boolean` . O booliano `mapValue` é ignorado, já que a presença da marca é tudo importante. 

Aqui está um trecho de um modelo de entrelaçamento que implementa uma marca de marcador como uma propriedade:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Adicionar marcas de marcador ao gêmeos digital

Depois que a `tags` propriedade faz parte de um modelo de teledigital, você pode definir a marca de marcador na folha de valores digitais, definindo o valor dessa propriedade. 

Aqui está um exemplo que popula o marcador `tags` para três gêmeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Consulta com marcas de marcador

Depois que as marcas forem adicionadas ao gêmeos digital, as marcas poderão ser usadas para filtrar o gêmeos em consultas. 

Aqui está uma consulta para obter todos os gêmeos que foram marcados como "vermelho": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Você também pode combinar marcas para consultas mais complexas. Aqui está uma consulta para obter todos os gêmeos que estão redondos e não vermelho: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Marcas de valor 

Uma **marca de valor** é um par chave-valor que é usado para fornecer a cada marca um valor, como `"color": "blue"` ou `"color": "red"` . Depois que uma marca de valor é criada, ela também pode ser usada como uma marca de marcador, ignorando o valor da marca. 

### <a name="add-value-tags-to-model"></a>Adicionar marcas de valor ao modelo 

Marcas de valor são modeladas como um mapa de [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) de `string` para `string` . Tanto o `mapKey` quanto o `mapValue` são significativos. 

Aqui está um trecho de um modelo de entrelaçamento que implementa uma marca de valor como uma propriedade:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Adicionar marcas de valor a gêmeos digital

Assim como nas marcas de marcador, você pode definir a marca de valor em um FileUp digital definindo o valor dessa `tags` Propriedade do modelo. Para usar uma marca de valor como marca de marcador, você pode definir o `tagValue` campo como o valor da cadeia de caracteres vazia ( `""` ). 

Aqui está um exemplo que popula o valor `tags` para três gêmeos:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Observe que `red` e `purple` são usados como marcas de marcador neste exemplo.

### <a name="query-with-value-tags"></a>Consulta com marcas de valor

Assim como nas marcas de marcador, você pode usar marcas de valor para filtrar o gêmeos em consultas. Você também pode usar marcas de valor e marcas de marcador juntos.

No exemplo acima, `red` está sendo usado como uma marca de marcador. Lembre-se de que essa é uma consulta para obter todos os gêmeos que foram marcados como "vermelho": 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Aqui está uma consulta para obter todas as entidades que são pequenas (marca de valor) e não vermelho: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre como projetar e gerenciar modelos de entrelaçamento digital:
* [*Como: gerenciar modelos de DTDL*](how-to-manage-model.md)

Leia mais sobre como consultar o grafo de entrelaçamento:
* [*Como consultar o gráfico de entrelaçamento*](how-to-query-graph.md)