---
title: Expressões de estilo controladas por dados no SDK da Web do Azure Maps | Mapas do Microsoft Azure
description: Saiba mais sobre expressões de estilo controladas por dados. Consulte como usar essas expressões no SDK da Web do Azure Maps para ajustar os estilos em mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 41a117c9ea8b47afcedaa1714abc2031d3be6c21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680059"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo controladas por dados (SDK da Web)

As expressões permitem aplicar lógica de negócios a opções de estilo que observam as propriedades definidas em cada forma em uma fonte de dados. As expressões podem filtrar dados em uma fonte de dados ou em uma camada. As expressões podem consistir em lógica condicional, como If-Statements. E, eles podem ser usados para manipular dados usando: operadores de cadeia de caracteres, operadores lógicos e operadores matemáticos.

Os estilos controlados por dados reduzem a quantidade de código necessária para implementar a lógica de negócios em relação ao estilo. Quando usado com camadas, as expressões são avaliadas no momento da renderização em um thread separado. Essa funcionalidade fornece maior desempenho em comparação com a avaliação da lógica de negócios no thread da interface do usuário.

Este vídeo fornece uma visão geral do estilo controlado por dados no SDK da Web do Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

As expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia de caracteres que especifica o nome do operador de expressão. Por exemplo, "+" ou "Case". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma cadeia de caracteres, número, booliano ou `null` ) ou outra matriz de expressão. O pseudocódigo a seguir define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

O SDK da Web do Azure Maps dá suporte a muitos tipos de expressões. As expressões podem ser usadas sozinhas ou em combinação com outras expressões.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressão de agregação](#aggregate-expression) | Uma expressão que define um cálculo que é processado em um conjunto de dados e pode ser usado com a `clusterProperties` opção de a `DataSource` . |
| [Expressões boolianas](#boolean-expressions) | As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas. |
| [Expressões de cores](#color-expressions) | As expressões de cor facilitam a criação e a manipulação de valores de cores. |
| [Expressões condicionais](#conditional-expressions) | As expressões condicionais fornecem operações lógicas que são como instruções IF-. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados de propriedade em um recurso. |
| [Expressões de interpolação e etapas](#interpolate-and-step-expressions) | As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. |
| [Expressões específicas de camada](#layer-specific-expressions) | Expressões especiais que só se aplicam a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework. |
| [Expressões de operador de cadeia de caracteres](#string-operator-expressions) | Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso. |
| [Expressões de tipo](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos. |
| [Expressões de associação de variáveis](#variable-binding-expressions) | Expressões de associação de variáveis armazenam os resultados de um cálculo em uma variável e referenciadas em outro lugar em uma expressão várias vezes sem precisar recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento da renderização. |

Todos os exemplos neste documento usam o recurso a seguir para demonstrar diferentes maneiras em que os diferentes tipos de expressões podem ser usados. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados de propriedade em um recurso. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['at', number, array]` | value | Recupera um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria do recurso: ponto, MultiPoint, LineString, MultiLineString, polígono, MultiPolygon. |
| `['get', string]` | valor | Obtém o valor da propriedade das propriedades do recurso atual. Retornará NULL se a propriedade solicitada estiver ausente. |
| `['get', string, object]` | valor | Obtém o valor da propriedade das propriedades do objeto fornecido. Retornará NULL se a propriedade solicitada estiver ausente. |
| `['has', string]` | boolean | Determina se as propriedades de um recurso têm a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto têm a propriedade especificada. |
| `['id']` | valor | Obtém a ID do recurso se ele tiver um. |
| `['in', boolean | string | number, array]` | boolean | Determina se um item existe em uma matriz |
| `['in', substring, string]` | boolean | Determina se uma subcadeia de caracteres existe em uma cadeia de caracteres |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | número | Retorna a primeira posição na qual um item pode ser encontrado em uma matriz ou uma subcadeia de caracteres pode ser encontrada em uma cadeia de caracteres ou `-1` se a entrada não puder ser encontrada. Aceita um índice opcional de onde começar a pesquisa. |
| `['length', string | array]` | número | Obtém o comprimento de uma cadeia de caracteres ou de uma matriz. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | matriz de cadeia de caracteres \| | Retorna um item de uma matriz ou de uma subcadeia de caracteres a partir de um índice de início especificado ou entre um índice inicial e um índice final, se definido. O valor de retorno é inclusivo do índice inicial, mas não do índice final. |

**Exemplos**

As propriedades de um recurso podem ser acessadas diretamente em uma expressão usando uma `get` expressão. Este exemplo usa o `zoneColor` valor do recurso para especificar a Propriedade Color de uma camada Bubble. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem, se todos os recursos de ponto tiverem a `zoneColor` propriedade. Se não forem, a cor provavelmente retornará para "preto". Para modificar a cor de fallback, use uma `case` expressão em combinação com a `has` expressão para verificar se a propriedade existe. Se a propriedade não existir, retorne uma cor de fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

As camadas de bolhas e símbolos renderizarão as coordenadas de todas as formas em uma fonte de dados, por padrão. Esse comportamento pode realçar os vértices de um polígono ou de uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de geometria dos recursos que ele renderiza, usando uma `['geometry-type']` expressão dentro de uma expressão booliana. O exemplo a seguir limita uma camada de bolha para que apenas os `Point` recursos sejam renderizados.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo a seguir permite que os `Point` `MultiPoint` recursos e sejam renderizados. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, a estrutura de polígonos será renderizada em camadas de linha. Para desabilitar esse comportamento em uma camada de linha, adicione um filtro que permita `LineString` apenas `MultiLineString` recursos e.  

Aqui estão alguns exemplos adicionais de como usar expressões de dados:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['+', number, number, …]` | número | Calcula a soma dos números especificados. |
| `['-', number]` | número | Subtrai 0 pelo número especificado. |
| `['-', number, number]` | número | Subtrai os primeiros números pelo segundo número. |
| `['*', number, number, …]` | número | Multiplica os números especificados juntos. |
| `['/', number, number]` | número | Divide o primeiro número pelo segundo número. |
| `['%', number, number]` | número | Calcula o resto ao dividir o primeiro número pelo segundo número. |
| `['^', number, number]` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `['abs', number]` | número | Calcula o valor absoluto do número especificado. |
| `['acos', number]` | número | Calcula o arco cosseno do número especificado. |
| `['asin', number]` | número | Calcula o arco seno do número especificado. |
| `['atan', number]` | número | Calcula o arco tangente do número especificado. |
| `['ceil', number]` | número | Arredonda o número para cima até o próximo número inteiro. |
| `['cos', number]` | número | Calcula o cos do número especificado. |
| `['e']` | número | Retorna a constante matemática `e` . |
| `['floor', number]` | número | Arredonda o número para baixo até o inteiro completo anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Retorna a constante matemática `ln(2)` . |
| `['log10', number]` | número | Calcula o logaritmo de base 10 do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo de base dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto de números especificado. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto de números especificado. |
| `['pi']` | número | Retorna a constante matemática `PI` . |
| `['round', number]` | número | Arredonda o número para o inteiro mais próximo. Valores intermediários são arredondados para longe de zero. Por exemplo, `['round', -1.5]` é avaliado como `-2` . |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |

## <a name="aggregate-expression"></a>Expressão de agregação

Uma expressão de agregação define um cálculo que é processado em um conjunto de dados e pode ser usado com a `clusterProperties` opção de a `DataSource` . A saída dessas expressões deve ser um número ou um booliano. 

Uma expressão de agregação leva em três valores: um valor de operador e um valor inicial, e uma expressão para recuperar uma propriedade de cada recurso em um dado para aplicar a operação de agregação. Essa expressão tem o seguinte formato:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operador: uma função de expressão que é aplicada em relação a todos os valores calculados pelo `mapExpression` para cada ponto no cluster. Operadores com suporte: 
    - Para números: `+` , `*` , `max` , `min`
    - Para boolianos: `all` , `any`
- inicialvalue: um valor inicial no qual o primeiro valor calculado é agregado.
- mapExpression: uma expressão que é aplicada em cada ponto do conjunto de dados.

**Exemplos**

Se todos os recursos em um conjunto de dados tiverem uma `revenue` propriedade, que é um número. Em seguida, a receita total de todos os pontos em um cluster, que são criados a partir do conjunto de dados, pode ser calculada. Esse cálculo é feito usando a seguinte expressão de agregação: `['+', 0, ['get', 'revenue']]`

### <a name="accumulated-expression"></a>Expressão acumulada

A `accumulated` expressão Obtém o valor de uma propriedade de cluster acumulada até o momento. Isso só pode ser usado na `clusterProperties` opção de uma fonte clusterizada `DataSource` .

**Usage**

```javascript
["accumulated"]
```

## <a name="boolean-expressions"></a>Expressões boolianas

As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas.

Ao comparar valores, a comparação é estritamente digitada. Os valores de tipos diferentes são sempre considerados desiguais. Os casos em que os tipos são conhecidos como diferentes no momento da análise são considerados inválidos e produzirão um erro de análise. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['!', boolean]` | booleano | Negação lógica. Retorna `true` se a entrada é `false` e `false` se a entrada é `true` . |
| `['!=', value, value]` | boolean | Retorna `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `['<', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente menor do que a segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['<=', value, value]` | boolean | Retorna `true` se a primeira entrada for menor ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['==', value, value]` | boolean | Retorna `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['>', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente maior do que a segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['>=' value, value]` | boolean | Retorna `true` se a primeira entrada for maior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `['all', boolean, boolean, …]` | boolean | Retorna `true` se todas as entradas forem `true` , `false` caso contrário,. |
| `['any', boolean, boolean, …]` | boolean | Retorna `true` se alguma das entradas for `true` , `false` caso contrário,. |
| `['within', Polygon | MultiPolygon | Feature<Polygon | MultiPolygon>]` | boolean | Retorna `true` se o recurso avaliado estiver totalmente contido dentro de um limite da geometria de entrada; caso contrário, false. O valor de entrada pode ser um geojson válido do tipo `Polygon` , `MultiPolygon` , `Feature` ou `FeatureCollection` . Recursos com suporte para avaliação:<br/><br/>-Point: retorna `false` se um ponto está no limite ou fica fora do limite.<br/>-LineString: retorna `false` se qualquer parte de uma linha ficar fora do limite, a linha interceptará o limite ou o ponto de extremidade de uma linha estiver no limite. |

## <a name="conditional-expressions"></a>Expressões condicionais

As expressões condicionais fornecem operações lógicas que são como instruções IF-.

As expressões a seguir executam operações lógicas condicionais nos dados de entrada. Por exemplo, a `case` expressão fornece a lógica "if/then/else" enquanto a `match` expressão é como uma "switch-Statement". 

### <a name="case-expression"></a>Expressão Case

Uma `case` expressão é um tipo de expressão condicional que fornece a lógica "if/then/else". Esse tipo de expressão percorre uma lista de condições booleanas. Retorna o valor de saída da primeira condição booliana a ser avaliada como true.

O pseudocódigo a seguir define a estrutura da `case` expressão. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplo**

O exemplo a seguir percorre diferentes condições booleanas até encontrar uma que seja avaliada como `true` e, em seguida, retorna esse valor associado. Se nenhuma condição booliana for avaliada como `true` , um valor de fallback será retornado. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expressão de correspondência

Uma `match` expressão é um tipo de expressão condicional que fornece a instrução switch, como lógica. A entrada pode ser qualquer expressão como, `['get', 'entityType']` que retorna uma cadeia de caracteres ou um número. Cada rótulo deve ser um único valor literal ou uma matriz de valores literais, cujos valores devem ser todas as cadeias de caracteres ou todos os números. A entrada corresponde se qualquer um dos valores na matriz corresponder. Cada rótulo deve ser exclusivo. Se o tipo de entrada não corresponder ao tipo dos rótulos, o resultado será o valor de fallback.

O pseudocódigo a seguir define a estrutura da `match` expressão. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Exemplos**

O exemplo a seguir examina a `entityType` propriedade de um recurso de ponto em uma camada de bolha que procura uma correspondência. Se encontrar uma correspondência, esse valor especificado será retornado ou retornará o valor de fallback.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

O exemplo a seguir usa uma matriz para listar um conjunto de rótulos que devem retornar o mesmo valor. Essa abordagem é muito mais eficiente do que listar cada rótulo individualmente. Nesse caso, se a `entityType` propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será retornada.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Expressão de adesão

Uma `coalesce` expressão percorre um conjunto de expressões até que o primeiro valor não nulo seja obtido e retorne esse valor. 

O pseudocódigo a seguir define a estrutura da `coalesce` expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo a seguir usa uma `coalesce` expressão para definir a `textField` opção de uma camada de símbolo. Se a `title` propriedade estiver ausente no recurso ou definida como `null` , a expressão tentará procurar a `subTitle` propriedade, se ela estiver ausente ou `null` , em seguida, retornará a uma cadeia de caracteres vazia. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subTitle. 
            ['get', 'subTitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

O exemplo a seguir usa uma `coalesce` expressão para recuperar o primeiro ícone de imagem disponível disponível no mapa Sprite de uma lista de nomes de imagem especificados.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Expressões de tipo

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['array', value]` \| `['array', type: "string" | "number" | "boolean", value]` | Objeto [] | Declara que a entrada é uma matriz. |
| `['boolean', value]` \| `["boolean", value, fallback: value, fallback: value, ...]` | boolean | Declara que o valor de entrada é um booliano. Se vários valores forem fornecidos, cada um será avaliado na ordem até que um booliano seja obtido. Se nenhuma das entradas for booliana, a expressão será um erro. |
| `['collator', { 'case-sensitive': boolean, 'diacritic-sensitive': boolean, 'locale': string }]` | Agrupador | Retorna um Agrupador para uso em operações de comparação dependentes de localidade. As opções de diferenciação de maiúsculas e minúsculas e de sinais diacríticos são padronizadas como falso. O argumento locale especifica a marca de idioma IETF da localidade a ser usada. Se nenhum for fornecido, a localidade padrão será usada. Se a localidade solicitada não estiver disponível, o Agrupador usará uma localidade de fallback definida pelo sistema. Use resolvid-locale para testar os resultados do comportamento de fallback de localidade. |
| `['literal', array]`<br/><br/>`['literal', object]` | objeto de matriz \| | Retorna um valor de objeto ou matriz literal. Use esta expressão para impedir que uma matriz ou objeto seja avaliado como uma expressão. Isso é necessário quando uma matriz ou objeto precisa ser retornado por uma expressão. |
| `['image', string]` | string | Verifica se uma ID de imagem especificada é carregada na imagem de mapas Sprite. Se for, a ID será retornada; caso contrário, NULL será retornado. |
| `['number', value]` \| `["number", value, fallback: value, fallback: value, ...]` | número | Declara que o valor de entrada é um número. Se vários valores forem fornecidos, cada um será avaliado na ordem até que um número seja obtido. Se nenhuma das entradas for números, a expressão será um erro. |
| `['object', value]`  \| `["object", value, fallback: value, fallback: value, ...]` | Objeto | Declara que o valor de entrada é um objeto.  Se vários valores forem fornecidos, cada um será avaliado na ordem até que um objeto seja obtido. Se nenhuma das entradas for objetos, a expressão será um erro. |
| `['string', value]` \| `["string", value, fallback: value, fallback: value, ...]` | string | Declara que o valor de entrada é uma cadeia de caracteres. Se vários valores forem fornecidos, cada um será avaliado na ordem até que uma cadeia de caracteres seja obtida. Se nenhuma das entradas for cadeia de caracteres, a expressão será um erro. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada em um booliano. O resultado é `false` quando a entrada é uma cadeia de caracteres vazia,,, `0` `false` `null` ou `NaN` ; caso contrário, seu `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | cor | Converte o valor de entrada em uma cor. Se vários valores forem fornecidos, cada um será avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada em um número, se possível. Se a entrada for `null` ou `false` , o resultado será 0. Se a entrada for `true` , o resultado será 1. Se a entrada for uma cadeia de caracteres, ela será convertida em um número usando a função de cadeia de caracteres [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) da especificação da linguagem ECMAScript. Se vários valores forem fornecidos, cada um será avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas puder ser convertida, a expressão será um erro. |
| `['to-string', value]` | string | Converte o valor de entrada em uma cadeia de caracteres. Se a entrada for `null` , o resultado será `""` . Se a entrada for um booliano, o resultado será `"true"` ou `"false"` . Se a entrada for um número, ela será convertida em uma cadeia de caracteres usando a função de número [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) da especificação da linguagem ECMAScript. Se a entrada for uma cor, ela será convertida em cadeia de caracteres de cor de CSS RGBA `"rgba(r,g,b,a)"` . Caso contrário, a entrada é convertida em uma cadeia de caracteres usando a função [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) da especificação da linguagem ECMAScript. |
| `['typeof', value]` | string | Retorna uma cadeia de caracteres que descreve o tipo do valor especificado. |

> [!TIP]
> Se uma mensagem de erro semelhante a `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` aparecer no console do navegador, significa que há uma expressão em algum lugar em seu código que tem uma matriz que não tem uma cadeia de caracteres para seu primeiro valor. Se você quiser que a expressão retorne uma matriz, empacote a matriz com a `literal` expressão. O exemplo a seguir define a `offset` opção de ícone de uma camada de símbolo, que precisa ser uma matriz que contém dois números, usando uma `match` expressão para escolher entre dois valores de deslocamento com base no valor da  `entityType` Propriedade do recurso de ponto.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressões de cores

As expressões de cor facilitam a criação e a manipulação de valores de cores.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | cor | Cria um valor de cor de componentes *vermelhos*, *verdes* e *azuis* que devem variar entre `0` e e `255` um componente alfa do `1` . Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `['rgba', number, number, number, number]` | cor | Cria um valor de cor de componentes *vermelhos*, *verdes* e *azuis* que devem variar entre `0` e e `255` um componente alfa dentro de um intervalo de `0` e `1` . Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `['to-rgba']` | \[número, número, número, número\] | Retorna uma matriz de quatro elementos que contém os componentes *vermelho*, *verde*, *azul* e *alfa* da cor de entrada, nessa ordem. |

**Exemplo**

O exemplo a seguir cria um valor de cor RGB que tem um valor *vermelho* de `255` e valores *verdes* e *azuis* que são calculados multiplicando `2.5` pelo valor da `temperature` propriedade. À medida que a temperatura for alterada, a cor será alterada para diferentes tons de *vermelho*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Expressões de operador de cadeia de caracteres

Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso. 

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena várias cadeias de caracteres juntas. Cada valor deve ser uma cadeia de caracteres. Use a `to-string` expressão Type para converter outros tipos de valor em String, se necessário. |
| `['downcase', string]` | string | Converte a cadeia de caracteres especificada em minúsculas. |
| `['is-supported-script', string]` \| `['is-supported-script', Expression]`| boolean | Determina se a cadeia de caracteres de entrada usa um conjunto de caracteres suportado pela pilha de fontes atual. Por exemplo: `['is-supported-script', 'ಗೌರವಾರ್ಥವಾಗಿ']` |
| `['resolved-locale', string]` | string | Retorna a marca de idioma da IETF da localidade que está sendo usada pelo Agrupador fornecido. Isso pode ser usado para determinar a localidade padrão do sistema ou para determinar se uma localidade solicitada foi carregada com êxito. |
| `['upcase', string]` | string | Converte a cadeia de caracteres especificada em maiúsculas. |

**Exemplo**

O exemplo a seguir converte a `temperature` Propriedade do recurso Point em uma cadeia de caracteres e, em seguida, concatena "° f" ao final dela.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

A expressão acima renderiza um PIN no mapa com o texto "64 ° f" sobreposto sobre ele, conforme mostrado na imagem abaixo.

<center>

![Exemplo ](media/how-to-expressions/string-operator-expression.png) de expressão de operador de cadeia de caracteres </center>

## <a name="interpolate-and-step-expressions"></a>Expressões de interpolação e etapas

As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. Essas expressões assumem uma expressão que retorna um valor numérico como sua entrada, por exemplo `['get',  'temperature']` . O valor de entrada é avaliado em relação aos pares de valores de entrada e saída, para determinar o valor que melhor se adapta à função de curva ou etapa interpolada. Os valores de saída são chamados de "paradas". Os valores de entrada para cada parada devem ser um número e estar em ordem crescente. Os valores de saída devem ser um número e uma matriz de números ou uma cor.

### <a name="interpolate-expression"></a>Expressão Interpolate

Uma `interpolate` expressão pode ser usada para calcular um conjunto de valores suave e contínuo, interpolando entre os valores de parada. Uma `interpolate` expressão que retorna valores de cor produz um gradiente de cor no qual os valores de resultado são selecionados.

Há três tipos de métodos de interpolação que podem ser usados em uma `interpolate` expressão:
 
* `['linear']` – Interpola linearmente entre o par de paradas.
* `['exponential', base]` – Interpola exponencialmente entre as interrupções. O `base` valor controla a taxa na qual a saída aumenta. Valores mais altos fazem com que a saída aumente mais em direção ao alto fim do intervalo. Um `base` valor próximo de 1 produz uma saída que aumenta de forma linear.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpolações usando uma [curva de Bézier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controle fornecidos.

Veja um exemplo de como esses diferentes tipos de interpolações se parecem. 

| Linear  | Exponencial | Bézier cúbica |
|---------|-------------|--------------|
| ![Grafo de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Grafo de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Grafo de interpolação Bézier cúbica](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo a seguir define a estrutura da `interpolate` expressão. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Exemplo**

O exemplo a seguir usa uma `linear interpolate` expressão para definir a `color` propriedade de uma camada de bolha com base na `temperature` Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado. Se ele estiver entre 60 e menor que 70, amarelo será retornado. Se estiver entre 70 e menor que 80, "laranja" será retornado. Se for 80 ou superior, será retornado "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50, 'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo ](media/how-to-expressions/interpolate-expression-example.png) de expressão Interpolate </center>

### <a name="step-expression"></a>Expressão de etapa

Uma `step` expressão pode ser usada para calcular valores de resultado discretos, avaliando uma [função piecewise](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paradas. 

O pseudocódigo a seguir define a estrutura da `step` expressão. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

As expressões de etapa retornam o valor de saída da parada logo antes do valor de entrada ou o primeiro valor de entrada se a entrada for menor que a primeira parada. 

**Exemplo**

O exemplo a seguir usa uma `step` expressão para definir a `color` propriedade de uma camada de bolha com base na `temperature` Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado. Se estiver entre 60 e menor que 70, "amarelo" será retornado. Se estiver entre 70 e menor que 80, "laranja" será retornado. Se for 80 ou superior, será retornado "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60, 'yellow',
        70, 'orange',
        80, 'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de etapa](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões específicas de camada

Expressões especiais que se aplicam somente a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade do mapa de calor

Uma expressão de densidade do mapa de calor recupera o valor de densidade do mapa de calor para cada pixel em uma camada do mapa de calor e é definida como `['heatmap-density']` . Esse valor é um número entre `0` e `1` . Ele é usado em combinação com uma `interpolation` `step` expressão or para definir o gradiente de cor usado para colorir o mapa de calor. Essa expressão só pode ser usada na [opção de cor](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) da camada do mapa de calor.

> [!TIP]
> A cor no índice 0, em uma expressão de interpolação ou a cor padrão de uma cor da etapa, define a cor da área onde não há dados. A cor no índice 0 pode ser usada para definir uma cor de plano de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto.

**Exemplo**

Este exemplo usa uma expressão de interpolação de capa para criar um gradiente de cor suave para renderizar o mapa de calor. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Além de usar um gradiente suave para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de intervalos usando uma `step` expressão. Usar uma `step` expressão para colorir o mapa de calor visualmente divide a densidade em intervalos que se assemelham a um mapa de estilo de contorno ou de radar.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Para obter mais informações, consulte a documentação [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Expressão de progresso da linha

Uma expressão de progresso de linha recupera o progresso ao longo de uma linha de gradiente em uma camada de linha e é definida como `['line-progress']` . Esse valor é um número entre 0 e 1. Ele é usado em combinação com uma `interpolation` `step` expressão ou. Essa expressão só pode ser usada com a [opção strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) da camada de linha. 

> [!NOTE]
> A `strokeGradient` opção da camada de linha requer a `lineMetrics` opção da fonte de dados a ser definida como `true` .

**Exemplo**

Este exemplo usa a `['line-progress']` expressão para aplicar um gradiente de cor ao traço de uma linha.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Consulte o exemplo ao vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A expressão de formato de campo de texto pode ser usada com a `textField` opção da propriedade camadas de símbolo `textOptions` para fornecer formatação de texto misto. Essa expressão permite que um conjunto de cadeias de caracteres de entrada e opções de formatação sejam especificados. As opções a seguir podem ser especificadas para cada cadeia de caracteres de entrada nesta expressão.

 * `'font-scale'` -Especifica o fator de dimensionamento para o tamanho da fonte. Se especificado, esse valor substituirá a `size` Propriedade do `textOptions` para a cadeia de caracteres individual.
 * `'text-font'` -Especifica uma ou mais famílias de fontes que devem ser usadas para esta cadeia de caracteres. Se especificado, esse valor substituirá a `font` Propriedade do `textOptions` para a cadeia de caracteres individual.

O pseudocódigo a seguir define a estrutura da expressão de formato de campo de texto. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[]
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[]
    },
    …
]
```

**Exemplo**

O exemplo a seguir formata o campo de texto adicionando uma fonte em negrito e dimensionando o tamanho da fonte da `title` Propriedade do recurso. Este exemplo também adiciona a `subTitle` Propriedade do recurso em uma nova linha, com um tamanho de fonte reduzido.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subTitle property. 
            ['get', 'subTitle'],
            { 
                'font-scale': 0.75
            }
        ]
    }
});
```

Essa camada renderizará o recurso de ponto, conforme mostrado na imagem abaixo:
 
<center>

![Imagem do recurso de ponto com campo ](media/how-to-expressions/text-field-format-expression.png) de texto formatado </center>

### <a name="number-format-expression"></a>Expressão de formato de número

A `number-format` expressão só pode ser usada com a `textField` opção de uma camada de símbolo. Essa expressão converte o número fornecido em uma cadeia de caracteres formatada. Essa expressão encapsula a função [número. Tolocalstring](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) do JavaScript e dá suporte ao seguinte conjunto de opções.

 * `locale` -Especifique essa opção para converter números em cadeias de caracteres de uma maneira que se alinhe com o idioma especificado. Passe uma [marca de idioma BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) para essa opção.
 * `currency` -Para converter o número em uma cadeia de caracteres que representa uma moeda. Os valores possíveis são os [códigos de moeda ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), como "USD" para o dólar americano, "EUR" para o euro ou "CNY" para o RMB do chinês.
 * `'min-fraction-digits'` -Especifica o número mínimo de casas decimais a serem incluídas na versão da cadeia de caracteres do número.
 * `'max-fraction-digits'` -Especifica o número máximo de casas decimais a serem incluídas na versão da cadeia de caracteres do número.

O pseudocódigo a seguir define a estrutura da expressão de formato de campo de texto. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Exemplo**

O exemplo a seguir usa uma `number-format` expressão para modificar como a `revenue` Propriedade do recurso de ponto é renderizada na `textField` opção de uma camada de símbolo, de modo que ela pareça um valor de dólar americano.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { 'currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Essa camada renderizará o recurso de ponto, conforme mostrado na imagem abaixo:

<center>

![Exemplo ](media/how-to-expressions/number-format-expression.png) de expressão de formato de número </center>

### <a name="image-expression"></a>Expressão de imagem

Uma expressão de imagem pode ser usada com `image` as `textField` Opções e de uma camada de símbolo e a `fillPattern` opção da camada de polígono. Essa expressão verifica se a imagem solicitada existe no estilo e retorna o nome da imagem resolvido ou `null` , dependendo se a imagem está no estilo no momento. Esse processo de validação é síncrono e requer que a imagem tenha sido adicionada ao estilo antes de solicitá-la no argumento de imagem.

**Exemplo**

O exemplo a seguir usa uma `image` expressão para adicionar um ícone embutido com texto em uma camada de símbolo. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Essa camada renderizará o campo de texto na camada de símbolo, conforme mostrado na imagem abaixo:

<center>

![Exemplo ](media/how-to-expressions/image-expression.png) de expressão de imagem </center>

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom atual do mapa no momento da renderização e é definida como `['zoom']` . Essa expressão retorna um número entre o intervalo de nível de zoom mínimo e máximo do mapa. Os controles de mapa interativos do Azure Maps para Web e Android dão suporte a 25 níveis de zoom, numerados de 0 a 24. O uso da `zoom` expressão permite que os estilos sejam modificados dinamicamente, pois o nível de zoom do mapa é alterado. A `zoom` expressão só pode ser usada com `interpolate` `step` expressões e.

**Exemplo**

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados são agregados juntos e a camada do mapa de calor é diferente. Uma `zoom` expressão pode ser usada para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Isso fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. O dimensionamento do raio, de modo que ele se dobra em cada nível de zoom, criará um mapa de calor que se parece consistente em todos os níveis de zoom. Ele pode ser feito usando a `zoom` expressão com uma `base 2 exponential interpolation` expressão, com o conjunto de raio de pixel definido para o nível de zoom mínimo e um raio dimensionado para o nível máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)` mostrado abaixo.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        1, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Consulte o exemplo ao vivo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de associação de variáveis

Expressões de associação de variáveis armazenam os resultados de um cálculo em uma variável. Portanto, os resultados de cálculo podem ser referenciados em outro lugar em uma expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expression | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' Let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome1: cadeia de caracteres,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome2: cadeia de caracteres,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childID<br/>\] | | Armazena um ou mais valores como variáveis para uso pela `var` expressão na expressão filho que retorna o resultado. |
| `['var', name: string]` | any | Faz referência a uma variável que foi criada usando a `let` expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita relativa à taxa de temperatura e, em seguida, usa uma `case` expressão para avaliar diferentes operações boolianas nesse valor. A `let` expressão é usada para armazenar a receita relativa à taxa de temperatura, para que ela só precise ser calculada uma vez. A `var` expressão referencia essa variável com frequência, conforme necessário, sem precisar recalculá-la.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código que implementam expressões:

> [!div class="nextstepaction"] 
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que dão suporte a expressões:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)