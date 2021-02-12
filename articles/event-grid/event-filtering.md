---
title: Evento de filtragem para a Grade de Eventos do Azure
description: Descreve como filtrar eventos durante a criação de uma assinatura de Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: bc3e84037693fcd909961ba409871d947ef1de7d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574899"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Compreender a filtragem para assinaturas da Grade de Eventos

Este artigo descreve as diferentes maneiras para filtrar quais eventos são enviados para o ponto de extremidade. Ao criar uma assinatura de evento, você tem três opções de filtragem:

* Tipos de evento
* Assunto começa com ou termina com
* Campos avançados e operadores

## <a name="event-type-filtering"></a>Filtragem de tipo de evento

Por padrão, todos os [tipos de evento](event-schema.md) para a origem do evento são enviadas para o ponto de extremidade. Você pode optar por enviar somente determinados tipos de evento para seu ponto de extremidade. Por exemplo, você pode ser notificado sobre atualizações para seus recursos, mas não é notificado para outras operações, como exclusões. Nesse caso, filtre pelo `Microsoft.Resources.ResourceWriteSuccess` tipo de evento. Forneça uma matriz com os tipos de evento ou especifique `All` para obter todos os tipos de evento para a origem do evento.

A sintaxe JSON para filtrar por tipo de evento é:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtro de assunto

Para filtragem simples por assunto, especifique um valor inicial ou final para o assunto. Por exemplo, você pode especificar o assunto que termina com `.txt` para obter apenas os eventos relacionados ao carregamento de um arquivo de texto para a conta de armazenamento. Ou, você pode filtrar o assunto começa com `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contêiner, mas não para outros contêineres na conta de armazenamento.

Ao publicar eventos em tópicos personalizados, crie assuntos para os eventos que tornem mais fácil aos assinantes reconhecer se estão interessados no evento. Os assinantes usam a propriedade de assunto para filtrar e rotear eventos. Considere adicionar o caminho do acontecimento do evento para que os assinante possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtrem eventos de maneira restrita ou ampla. Se você fornecer um caminho de três segmentos como `/A/B/C` no assunto, os assinantes poderão filtrar pelo primeiro segmento `/A` para obter um conjunto amplo de eventos. Esses assinantes recebem eventos com assuntos como `/A/B/C` ou `/A/D/E`. Outros assinantes podem filtrar por `/A/B` para obter um conjunto de eventos mais restrito.

A sintaxe JSON para filtragem por assunto é:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtragem avançada

Para filtrar por valores nos campos de dados e especificar o operador de comparação, use a opção de filtragem avançada. Na filtragem avançada, você especifica o:

* tipo de operador: o tipo de comparação.
* chave – o campo nos dados do evento que você está usando para filtragem. Ele pode ser um número, booliano ou cadeia de caracteres.
* Values-o valor ou valores para comparar com a chave.

Se você especificar um único filtro com vários valores, uma operação **ou** será executada, portanto, o valor do campo de chave deverá ser um desses valores. Veja um exemplo:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Se você especificar vários filtros diferentes, uma operação and será feita, portanto, cada condição **de** filtro deverá ser atendida. Aqui está um exemplo: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operadores

Os operadores disponíveis para **números** são:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

O operador disponível para **boolianos** é: 
- BoolEquals

Os operadores disponíveis para **cadeias de caracteres** são:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Todas as comparações de cadeia de caracteres **não** diferenciam maiúsculas de minúsculas.

> [!NOTE]
> Se o evento JSON não contiver a chave de filtro avançada, Filter será evaulated como **não correspondente** aos seguintes operadores: 
> - NumberGreaterThan
> - NumberGreaterThanOrEquals
> - NumberLessThan
> - NumberLessThanOrEquals
> - NumberIn
> - BoolEquals
> - StringContains
> - StringBeginsWith
> - StringEndsWith
> - StringIn
> 
>O filtro é evaulated como **correspondido** para os seguintes operadores:
> - NumberNotIn
> - StringNotIn

### <a name="key"></a>Chave

Para eventos no esquema de Grade de Eventos do Azure, use os seguintes valores para a chave:

* ID
* Tópico
* Assunto
* EventType
* DataVersion
* Dados de evento (como Data.key1)

Para eventos no esquema de Eventos de Nuvem, use os seguintes valores para a chave:

* EventId
* Fonte
* EventType
* EventTypeVersion
* Dados de evento (como Data.key1)

Para o esquema de entrada personalizada, use os campos de dados de evento (como Data.key1).

### <a name="values"></a>Valores

Os valores podem ser:

* número
* string
* booleano
* matriz

### <a name="limitations"></a>Limitações

No entanto, o MakeCert tem as seguintes limitações:

* 5 filtros avançados e 25 valores de filtro em todos os filtros por assinatura de grade de eventos
* 512 caracteres por valor de cadeia de caracteres
* Cinco valores para **em** e **não está nos** operadores
* Chaves com um caractere **`.` (ponto)** . Por exemplo: `http://schemas.microsoft.com/claims/authnclassreference` ou `john.doe@contoso.com`. No momento, não há suporte para caracteres de escape em chaves. 

A mesma chave pode ser usada em mais de um filtro.

### <a name="examples"></a>Exemplos

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como filtrar eventos com o PowerShell e CLI do Azure, consulte [Filtrar eventos de Grade de Eventos](how-to-filter-events.md).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
