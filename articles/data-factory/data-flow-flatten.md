---
title: Transformação achatar no fluxo de dados de mapeamento
description: Desnormalizar dados hierárquicos usando a transformação achatar
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81413685"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Transformação achatar no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação achatar para pegar valores de matriz dentro de estruturas hierárquicas, como JSON, e desagrupá-los em linhas individuais. Esse processo é conhecido como desnormalização.

## <a name="configuration"></a>Configuração

A transformação achatamento contém as seguintes definições de configuração

![Configurações de mesclagem](media/data-flow/flatten1.png "Configurações de mesclagem")

### <a name="unroll-by"></a>Não acumulado por

Selecione uma matriz para desroll-la. Os dados de saída terão uma linha por item em cada matriz. Se a matriz de não acumulação na linha de entrada for nula ou vazia, haverá uma linha de saída com valores não acumulados como NULL.

### <a name="unroll-root"></a>Desacumular raiz

Por padrão, a transformação achatar desverte uma matriz na parte superior da hierarquia na qual ela existe. Opcionalmente, você pode selecionar uma matriz como sua raiz de desroll. A raiz não acumulada deve ser uma matriz de objetos complexos que seja ou que contenha a matriz unlance by. Se uma raiz não acumulada for selecionada, os dados de saída conterão pelo menos uma linha por item na raiz de deslançamento. Se a linha de entrada não tiver nenhum item na raiz não acumulada, ela será descartada dos dados de saída. A escolha de uma raiz sem distribuição sempre produzirá um número menor ou igual de linhas que o comportamento padrão.

### <a name="flatten-mapping"></a>Mapeamento de achatamento

Semelhante à transformação selecionar, escolha a projeção da nova estrutura de campos de entrada e a matriz desnormalizada. Se uma matriz desnormalizada for mapeada, a coluna de saída será o mesmo tipo de dados que a matriz. Se a matriz de unlance by for uma matriz de objetos complexos que contém submatrizes, o mapeamento de um item desse subarry produzirá uma matriz.

Consulte a guia inspecionar e a visualização de dados para verificar a saída do mapeamento.

## <a name="examples"></a>Exemplos

Consulte o seguinte objeto JSON para ver os exemplos abaixo da transformação achatar

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Nenhuma raiz de distribuição com matriz de cadeia de caracteres

| Não acumulado por | Desacumular raiz | Projeção |
| --------- | ----------- | ---------- |
| bens. Customers | Nenhum | name <br> cliente = bens. Customer |

#### <a name="output"></a>Saída

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Nenhuma raiz de distribuição com matriz complexa

| Não acumulado por | Desacumular raiz | Projeção |
| --------- | ----------- | ---------- |
| bens. Orders. remetited. orderItems | Nenhum | name <br> orderId = bens. Orders. orderId <br> itemName = bens. Orders. remetited. orderItems. itemName <br> itemQty = bens. Orders. remetited. orderItems. itemQty <br> local = local |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Mesma raiz como matriz não revertida

| Não acumulado por | Desacumular raiz | Projeção |
| --------- | ----------- | ---------- |
| bens. Orders | bens. Orders | name <br> mercadorias. Orders. remetited. orderItems. itemName <br> bens. Customers <br> local |

#### <a name="output"></a>Saída

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Desacumular raiz com matriz complexa

| Não acumulado por | Desacumular raiz | Projeção |
| --------- | ----------- | ---------- |
| bens. Orders. remetited. orderItem | bens. Orders |name <br> orderId = bens. Orders. orderId <br> itemName = bens. Orders. remetited. orderItems. itemName <br> itemQty = bens. Orders. remetited. orderItems. itemQty <br> local = local |

#### <a name="output"></a>Saída

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Exemplo

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Próximas etapas

* Use a [transformação dinâmica](data-flow-pivot.md) para dinamizar linhas para colunas.
* Use a [transformação não dinâmica](data-flow-unpivot.md) para dinamizar as colunas para linhas.
