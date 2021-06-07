---
title: Evento de filtragem para a Grade de Eventos do Azure
description: Descreve como filtrar eventos durante a criação de uma assinatura de Grade de Eventos do Azure.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: fa63296f97bfa888cb0f425d0c03a5e4a7e46525
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419840"
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
* chave – o campo nos dados do evento que você está usando para filtragem. Pode ser um número, booliano, Cadeia de caracteres ou uma matriz.
* Values-o valor ou valores para comparar com a chave.

## <a name="key"></a>Chave
Chave é o campo nos dados de evento que você está usando para filtragem. Pode ser um dos seguintes tipos:

- Número
- Boolean
- String
- Matriz. Você precisa definir a `enableAdvancedFilteringOnArrays` propriedade como true para usar esse recurso. Atualmente, o portal do Azure não dá suporte à habilitação desse recurso. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Para eventos no **esquema da grade de eventos**, use os seguintes valores para os dados de evento Key:,,,, `ID` `Topic` `Subject` `EventType` `DataVersion` ou (como `data.key1` ).

Para eventos no **esquema de eventos de nuvem**, use os seguintes valores para os `eventid` dados de evento Key:,, `source` `eventtype` , `eventtypeversion` ou (como `data.key1` ).

Para **esquema de entrada personalizado**, use os campos de dados de evento (como `data.key1` ). Para acessar campos na seção de dados, use a `.` notação (ponto). Por exemplo, `data.sitename` `data.appEventTypeDetail.action` para acessar `sitename` ou `action` para o seguinte evento de exemplo.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Valores
Os valores podem ser: número, Cadeia de caracteres, booliano ou matriz

## <a name="operators"></a>Operadores

Os operadores disponíveis para **números** são:

## <a name="numberin"></a>NumberIn
O operador Numbere será avaliado como true se o valor da **chave** for um dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção é 5 ou 1. 

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


Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a, b, c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
O NumberNotIn será avaliado como true se o  valor da chave **não** for um dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção não é 41 e 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a, b, c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
O operador NumberLessThan será avaliado como true se o valor da **chave** for **menor que** o valor do **filtro** especificado. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção é menor que 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação ao valor do filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
O operador NumberGreaterThan será avaliado como true se o valor da **chave** for **maior que** o valor do **filtro** especificado. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção é maior que 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação ao valor do filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
O operador NumberLessThanOrEquals será avaliado como true se o valor da **chave** for **menor ou igual** ao valor do **filtro** especificado. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção é menor ou igual a 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação ao valor do filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
O operador NumberGreaterThanOrEquals será avaliado como true se o valor da **chave** for **maior ou igual** ao valor do **filtro** especificado. No exemplo a seguir, ele verifica se o valor do `counter` atributo na `data` seção é maior ou igual a 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação ao valor do filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
O operador NumberInRange será avaliado como true se o valor da **chave** estiver em um dos **intervalos de filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção está em um dos dois intervalos: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

A `values` propriedade é uma matriz de intervalos. No exemplo anterior, é uma matriz de dois intervalos. Aqui está um exemplo de uma matriz com um intervalo a ser verificado. 

**Matriz com um intervalo:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: uma matriz de intervalos. Neste pseudo-código, `a` e `b` são valores baixos e altos de cada intervalo na matriz. Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
O operador NumberNotInRange será avaliado como true se o  valor da chave **não** estiver em nenhum dos **intervalos de filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção está em um dos dois intervalos: 3,14159-999,95, 3000-4000. Se for, o operador retornará false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
A `values` propriedade é uma matriz de intervalos. No exemplo anterior, é uma matriz de dois intervalos. Aqui está um exemplo de uma matriz com um intervalo a ser verificado.

**Matriz com um intervalo:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: uma matriz de intervalos. Neste pseudo-código, `a` e `b` são valores baixos e altos de cada intervalo na matriz. Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


O operador disponível para **boolianos** é: 

## <a name="boolequals"></a>BoolEquals
O operador BoolEquals será avaliado como true se o valor da **chave** for o **filtro** de valor booliano especificado. No exemplo a seguir, ele verifica se o valor do `isEnabled` atributo na `data` seção é `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação ao valor booliano do filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Os operadores disponíveis para **cadeias de caracteres** são:

## <a name="stringcontains"></a>StringContains
O **StringContains** será avaliado como true se o valor da **chave** **contiver** qualquer um dos valores de **filtro** especificados (como subcadeias). No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção contém uma das subcadeias de caracteres especificadas: `microsoft` ou `azure` . Por exemplo, `azure data factory` tem `azure` nele. 

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

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
O operador **StringNotContains** será avaliado como true se a **chave** **não contiver** os valores de **filtro** especificados como subcadeias. Se a chave contiver um dos valores especificados como uma Subcadeia, o operador será avaliado como false. No exemplo a seguir, o operador retornará true somente se o valor do `key1` atributo na `data` seção não tiver `contoso` e `fabrikam` como subcadeias de caracteres. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Consulte a seção [limitações](#limitations) para obter a limitação atual deste operador.

## <a name="stringbeginswith"></a>StringBeginsWith
O operador **StringBeginsWith** será avaliado como true se o valor da **chave** **começar com** qualquer um dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção começa com `event` ou `grid` . Por exemplo, `event hubs` começa com `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
O operador **StringNotBeginsWith** será avaliado como true se o  valor da chave **não começar com** nenhum dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção não começa com `event` ou `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
O operador **StringEndsWith** será avaliado como true se o valor da **chave** **terminar com** um dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção termina com `jpg` ou `jpeg` ou `png` . Por exemplo, `eventgrid.png` termina com `png` .


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

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
O operador **StringNotEndsWith** será avaliado como true se o  valor da chave **não terminar com** nenhum dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção não termina com `jpg` or `jpeg` ou `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
O operador **Stringe** verifica se o valor da **chave** **corresponde exatamente** a um dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção é `exact` ou `string` ou `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
O operador **StringNotIn** verifica se o valor da **chave** **não corresponde** a nenhum dos valores de **filtro** especificados. No exemplo a seguir, ele verifica se o valor do `key1` atributo na `data` seção não é `aws` e `bridge` . 

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

Se a chave for uma matriz, todos os valores na matriz serão verificados em relação à matriz de valores de filtro. Aqui está o pseudocódigo com a chave: `[v1, v2, v3]` e o filtro: `[a,b,c]` . Quaisquer valores de chave com tipos de dados que não correspondem ao tipo de dados do filtro são ignorados.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Todas as comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas

> [!NOTE]
> Se o JSON de evento não contiver a chave de filtro avançada, o filtro será evaulated como **não correspondente** aos seguintes operadores: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, Numberem, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, Stringe.
> 
>O filtro é evaulated como **correspondido** para os seguintes operadores: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
O operador IsNullOrUndefined será avaliado como true se o valor da chave for nulo ou indefinido. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

No exemplo a seguir, a key1 está ausente, portanto, o operador deve ser avaliado como true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

No exemplo a seguir, key1 é definido como NULL, portanto, o operador deve ser avaliado como true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

se key1 tiver qualquer outro valor nesses exemplos, o operador será avaliado como false. 

## <a name="isnotnull"></a>IsNotNull
O operador IsNotNull será avaliado como true se o valor da chave não for nulo ou indefinido. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OU e e e
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

Se você especificar vários filtros diferentes, uma operação and será feita, portanto, cada condição **de** filtro deverá ser atendida. Veja um exemplo: 

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

## <a name="cloudevents"></a>CloudEvents 
Para eventos no **esquema CloudEvents**, use os seguintes valores para os dados de evento Key: `eventid` , `source` , `eventtype` , `eventtypeversion` ou (como `data.key1` ). 

Você também pode usar [atributos de contexto de extensão no CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). No exemplo a seguir, `comexampleextension1` e `comexampleothervalue` são atributos de contexto de extensão. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Aqui está um exemplo de como usar um atributo de contexto de extensão em um filtro.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Limitações

No entanto, o MakeCert tem as seguintes limitações:

* 5 filtros avançados e 25 valores de filtro em todos os filtros por assinatura de grade de eventos
* 512 caracteres por valor de cadeia de caracteres
* Cinco valores para **em** e **não está nos** operadores
* O `StringNotContains` operador não está disponível no portal no momento.
* Chaves com um caractere **`.` (ponto)** . Por exemplo: `http://schemas.microsoft.com/claims/authnclassreference` ou `john.doe@contoso.com`. No momento, não há suporte para caracteres de escape em chaves. 

A mesma chave pode ser usada em mais de um filtro.





## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como filtrar eventos com o PowerShell e CLI do Azure, consulte [Filtrar eventos de Grade de Eventos](how-to-filter-events.md).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
