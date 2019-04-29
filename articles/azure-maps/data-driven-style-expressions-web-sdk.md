---
title: Controlado por dados expressões de estilo no SDK da Web de mapas do Azure | Microsoft Docs
description: Como usar expressões de estilo orientado a dados no SDK da Web de mapas do Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904870"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Expressões de estilo orientado a dados (SDK da Web)

Expressões que você possa aplicar lógica de negócios para as opções de estilo que observa as propriedades definidas em cada forma de uma fonte de dados. Expressões também podem ser usadas para filtrar dados em uma fonte de dados ou camada. Expressões podem consistir de lógica condicional, como instruções if e também podem ser usadas para manipular dados. cadeia de caracteres, operadores lógicos e matemáticos. 

Estilos orientado a dados podem reduzir a quantidade de código necessário para implementar a lógica de negócios em torno de estilo. Quando usado com camadas, as expressões são avaliadas em tempo de renderização em um thread separado que fornece maior desempenho quando comparado ao avaliar a lógica de negócios no thread da interface do usuário.

O vídeo a seguir fornece uma visão geral do estilo controlada por dados no SDK da Web de mapas do Azure.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Expressões são representadas como matrizes JSON. O primeiro elemento de uma expressão na matriz é uma cadeia de caracteres que especifica o nome do operador de expressão. Por exemplo, "+" ou "case". Os próximos elementos (se houver) são os argumentos para a expressão. Cada argumento é um valor literal (uma cadeia de caracteres, número, booliano, ou `null`), ou outra matriz de expressão. O pseudocódigo a seguir define a estrutura básica de uma expressão. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

SDK da Web de mapas do Azure dá suporte a muitos tipos de expressões que podem ser usados sozinhos ou em combinação com outras expressões.

| Tipo de expressões | DESCRIÇÃO |
|---------------------|-------------|
| [Expressões Boolianas](#boolean-expressions) | Expressões Boolianas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações booleanas. |
| [Expressões de cores](#color-expressions) | Expressões de cores facilitam criar e manipular valores de cor. |
| [Expressões condicionais](#conditional-expressions) | Expressões condicionais fornecem operações de lógica que são como instruções if. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados de propriedade em um recurso. |
| [Interpolar e expressões de etapa](#interpolate-and-step-expressions) | Interpolar e expressões de etapa podem ser usadas para calcular valores ao longo de uma função de curva ou etapa interpolada. |
| [Expressões de específico de camada](#layer-specific-expressions) | Expressões especiais que só são aplicáveis a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece operadores matemáticos para executar cálculos orientados a dados dentro da estrutura de expressão. |
| [Expressões do operador de cadeia de caracteres](#string-operator-expressions) | Expressões do operador de cadeia de caracteres executam operações de conversão em cadeias de caracteres, como concatenar e convertendo o caso. |
| [Expressões de tipo](#type-expressions) | Expressões de tipo fornecem ferramentas para teste e convertendo tipos de dados diferentes, como cadeias de caracteres, números e valores booleanos. |
| [Expressões de associação de variável](#variable-binding-expressions) | Expressões de associação de variável permitem que os resultados de um cálculo a ser armazenado em uma variável e referenciados em outro lugar em uma expressão várias vezes sem precisar recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa em tempo de renderização. |

Todos os exemplos neste documento usará o recurso a seguir para demonstrar as diferentes maneiras que os diferentes tipos de expressões podem ser usados. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Expressões de dados

Expressões de dados fornecem acesso aos dados de propriedade em um recurso. 

| Expression | Tipo de retorno | DESCRIÇÃO |
|------------|-------------|-------------|
| `['at', number, array]` | objeto | Recupera um item de uma matriz. |
| `['geometry-type']` | string | Obtém o tipo de geometria do recurso: Ponto, MultiPoint, LineString, MultiLineString, MultiPolygon, polígono. |
| `['get', string]` | value | Obtém o valor da propriedade das propriedades do recurso atual. Retorna nulo se a propriedade solicitada está ausente. |
| `['get', string, object]` | value | Obtém o valor da propriedade das propriedades do objeto fornecido. Retorna nulo se a propriedade solicitada está ausente. |
| `['has', string]` | boolean | Determina se as propriedades de um recurso têm a propriedade especificada. |
| `['has', string, object]` | boolean | Determina se as propriedades do objeto tem a propriedade especificada. |
| `['id']` | value | Obtém a ID do recurso se ele tiver um. |
| `['length', string | array]` | número | Obtém o comprimento de uma cadeia de caracteres ou matriz. |

**Exemplos**

Propriedades de um recurso podem ser acessadas diretamente em uma expressão, usando um `get` expressão. O exemplo a seguir usa o valor de "zoneColor" do recurso para especificar a propriedade de cor de uma camada de bolha. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

O exemplo acima funcionará bem se todos os recursos de ponto tem o `zoneColor` propriedade, mas se não, a cor provavelmente voltará à "preto". Para modificar a cor de fallback, um `case` expressão pode ser usada em combinação com o `has` expressão para verificar se a propriedade existe e se ele não retorna uma cor de fallback em vez disso.

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

Camadas de símbolo e de bolhas processará as coordenadas de todas as formas em uma fonte de dados por padrão. Isso pode ser feito para realçar os vértices de um polígono ou linha. O `filter` opção da camada pode ser usada para limitar o tipo de geometria dos recursos que ele renderiza usando um `['geometry-type']` expressão dentro de uma expressão booliana. O exemplo a seguir limita uma camada de bolha para que somente `Point` recursos são renderizados.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

O exemplo a seguir permitirá que os dois `Point` e `MultiPoint` recursos a serem renderizados. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Da mesma forma, a estrutura de tópicos de polígonos será renderizado em camadas da linha. Para desabilitar esse comportamento em uma camada de linha, adicione um filtro que permite apenas `LineString` e `MultiLineString` recursos.  

## <a name="math-expressions"></a>Expressões matemáticas

Expressões matemáticas fornecem operadores matemáticos para executar cálculos orientados a dados dentro da estrutura de expressão.

| Expression | Tipo de retorno | DESCRIÇÃO |
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
| `['ceil', number]` | número | Arredonda o número até o próximo número inteiro. |
| `['cos', number]` | número | Calcula o cos do número especificado. |
| `['e']` | número | Retorna a constante matemática `e`. |
| `['floor', number]` | número | Arredonda o número para baixo até o inteiro anterior. |
| `['ln', number]` | número | Calcula o logaritmo natural do número especificado. |
| `['ln2']` | número | Retorna a constante matemática `ln(2)`. |
| `['log10', number]` | número | Calcula o logaritmo de base 10 do número especificado. |
| `['log2', number]` | número | Calcula o logaritmo de base e dois do número especificado. |
| `['max', number, number, …]` | número | Calcula o número máximo no conjunto especificado de números. |
| `['min', number, number, …]` | número | Calcula o número mínimo no conjunto especificado de números. |
| `['pi']` | número | Retorna a constante matemática `PI`. |
| `['round', number]` | número | Arredonda o número para o inteiro mais próximo. Valores decimais são arredondados para cima. Por exemplo, `['round', -1.5]` é avaliada como -2. |
| `['sin', number]` | número | Calcula o seno do número especificado. |
| `['sqrt', number]` | número | Calcula a raiz quadrada do número especificado. |
| `['tan', number]` | número | Calcula a tangente do número especificado. |
## <a name="boolean-expressions"></a>Expressões boolianas

Expressões Boolianas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações booleanas.

Ao comparar valores, a comparação é digitada. Valores de tipos diferentes são sempre considerados desiguais. Casos em que os tipos são conhecidos seja diferente no momento da análise são considerados inválidos e produzirão um erro de análise. 

| Expression | Tipo de retorno | DESCRIÇÃO |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | Negação lógica. Retorna `true` se a entrada for `false`, e `false` se a entrada for `true`. |
| `['!= ', value, value]` | boolean | Retorna `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `['<', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente menor que o segundo, `false` caso contrário. Os argumentos são necessários para estar ambos cadeias de caracteres ou os dois números. |
| `['<=', value, value]` | boolean | Retorna `true` se a primeira entrada é menor ou igual ao segundo, `false` caso contrário. Os argumentos são necessários para estar ambos cadeias de caracteres ou os dois números. |
| `['==', value, value]` | boolean | Retorna `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos são necessários para estar ambos cadeias de caracteres ou os dois números. |
| `['>', value, value]` | boolean | Retorna `true` se a primeira entrada é estritamente maior que o segundo, `false` caso contrário. Os argumentos são necessários para estar ambos cadeias de caracteres ou os dois números. |
| `['>=' value, value]` | boolean | Retorna `true` se a primeira entrada é maior que ou igual ao segundo, `false` caso contrário. Os argumentos são necessários para estar ambos cadeias de caracteres ou os dois números. |
| `['all', boolean, boolean, …]` | boolean | Retorna `true` se todas as entradas forem `true`, `false` caso contrário. |
| `['any', boolean, boolean, …]` | boolean | Retorna `true` se qualquer uma das entradas forem `true`, `false` caso contrário. |

## <a name="conditional-expressions"></a>Expressões condicionais

Expressões condicionais fornecem operações de lógica que são como instruções if.

As expressões a seguir executam operações de lógica condicional nos dados de entrada. Por exemplo, o `case` expressão fornece lógica "if/then/else" enquanto o `match` expressão é como uma "-instrução switch". 

### <a name="case-expression"></a>Expressão case

Um `case` expressão é um tipo de expressão condicional que fornece a instrução if como lógica (if/then/else). Esse tipo de expressão percorre uma lista de condições Boolianas e retorna o valor de saída da primeira condição booliana for verdadeira.

O pseudocódigo a seguir define a estrutura do `case` expressão. 

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

O exemplo a seguir percorre diferentes condições Boolianas até encontrar uma que seja avaliada como `true`e, em seguida, retorna que o valor associado. Se nenhuma condição booleana for avaliada como `true`, será retornado um valor de fallback. 

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

Um `match` expressão é um tipo de expressão condicional que fornece a instrução switch como lógica. A entrada pode ser qualquer expressão, como `['get', 'entityType']` que retorna uma cadeia de caracteres ou um número. Cada rótulo deve ser um único valor literal ou uma matriz de valores literais, cujos valores devem ser todas as cadeias de caracteres ou todos os números. A entrada corresponde se qualquer um dos valores na correspondência de matriz. Cada rótulo deve ser exclusivo. Se o tipo de entrada não corresponde ao tipo dos rótulos, o resultado será o valor de fallback.

O pseudocódigo a seguir define a estrutura do `match` expressão. 

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

O exemplo a seguir examina o `entityType` propriedade de um recurso de ponto em uma camada de bolha procura uma correspondência. Se ele encontrar uma correspondência, o que especificou o valor é retornado ou retorna o valor de fallback.

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

O exemplo a seguir usa uma matriz para listar um conjunto de rótulos que devem todas retornar o mesmo valor. Isso é muito mais eficiente do que lista cada rótulo individualmente. Nesse caso, se o `entityType` propriedade é "restaurante" ou "grocery_store", a cor "vermelho" será retornada.

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

### <a name="coalesce-expression"></a>Expressão de união

Um `coalesce` expressão as etapas por meio de um conjunto de expressões até que o primeiro valor não nulo é obtido e retorna esse valor. 

O pseudocódigo a seguir define a estrutura do `coalesce` expressão. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Exemplo**

O exemplo a seguir usa uma `coalesce` expressão para definir o `textField` opção de uma camada de símbolo. Se o `title` propriedade está ausente do recurso ou definido como `null`, a expressão será, em seguida, tentar procurar o `subtitle` propriedade, se seu ausente ou `null`, ele fará o fallback para uma cadeia de caracteres vazia. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Expressões de tipo

Expressões de tipo fornecem ferramentas para teste e convertendo tipos de dados diferentes, como cadeias de caracteres, números e valores booleanos.

| Expression | Tipo de retorno | DESCRIÇÃO |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | matriz \| objeto | Retorna um valor literal de matriz ou objeto. Use essa expressão para impedir que uma matriz ou objeto que está sendo avaliada como uma expressão. Isso é necessário quando uma matriz ou objeto precisa ser retornado por uma expressão. |
| `['to-boolean', value]` | boolean | Converte o valor de entrada em um booliano. O resultado será `false` quando a entrada é uma cadeia de caracteres vazia `0`, `false`, `null`, ou `NaN`; caso contrário, seu `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | cor | Converte o valor de entrada para uma cor. Se vários valores são fornecidos, cada um deles é avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas podem ser convertidas, a expressão é um erro. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | número | Converte o valor de entrada para um número, se possível. Se a entrada for `null` ou `false`, o resultado é 0. Se a entrada for `true`, o resultado será 1. Se a entrada for uma cadeia de caracteres, ele será convertido em um número usando a [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) cadeia de caracteres a função da especificação da linguagem ECMAScript. Se vários valores são fornecidos, cada um deles é avaliado na ordem até que a primeira conversão bem-sucedida seja obtida. Se nenhuma das entradas podem ser convertidas, a expressão é um erro. |
| `['to-string', value]` | string | Converte o valor de entrada em uma cadeia de caracteres. Se a entrada for `null`, o resultado é `""`. Se a entrada for um valor booliano, o resultado será `"true"` ou `"false"`. Se a entrada for um número, ele será convertido em uma cadeia de caracteres usando o [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) número de função da especificação da linguagem ECMAScript. Se a entrada for uma cor, ele será convertido em cadeia de caracteres de cor CSS RGBA `"rgba(r,g,b,a)"`. Caso contrário, a entrada é convertida em uma cadeia de caracteres usando o [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) função da especificação da linguagem ECMAScript. |
| `['typeof', value]` | string | Retorna uma cadeia de caracteres que descreve o tipo do valor fornecido. |

> [!TIP]
> Se uma mensagem de erro semelhante à `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` aparecer no console do navegador-significa que há uma expressão em algum lugar no seu código que possui uma matriz que não tem uma cadeia de caracteres para o primeiro valor. Se você quiser que a expressão para retornar uma matriz, encapsule a matriz com o `literal` expressão. O exemplo a seguir define o ícone `offset` opção de uma camada de símbolo, o que precisa ser uma matriz que contém dois números, usando um `match` expressão para escolher entre dois valores de deslocamento com base no valor da `entityType` propriedade do ponto recurso.
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
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Expressões de cores

Expressões de cores facilitam criar e manipular valores de cor.

| Expression | Tipo de retorno | DESCRIÇÃO |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | cor | Cria um valor de cor de *vermelho*, *verde*, e *azul* componentes devem variar entre `0` e `255`e um componente alfa de `1`. Se qualquer componente está fora do intervalo, a expressão é um erro. |
| `['rgba', number, number, number, number]` | cor | Cria um valor de cor de *vermelho*, *verde*, *azul* componentes devem variar entre `0` e `255`e um componente alfa dentro de um intervalo de `0` e `1`. Se qualquer componente está fora do intervalo, a expressão é um erro. |
| `['to-rgba']` | \[number, number, number, number\] | Retorna uma matriz de quatro elementos que contém a cor de entrada *vermelho*, *verde*, *azul*, e *alfa* componentes, nessa ordem. |

**Exemplo**

O exemplo a seguir cria e valor de cor RGB que tem um *vermelho* valor de `255`, e *verde* e *azul* valores que são calculados multiplicando `2.5` pelo valor da `temperature` propriedade. Como as mudanças de temperatura alterará a cor com tons diferentes de *vermelho*.

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

## <a name="string-operator-expressions"></a>Expressões do operador de cadeia de caracteres

Expressões do operador de cadeia de caracteres executam operações de conversão em cadeias de caracteres, como concatenar e convertendo o caso. 

| Expression | Tipo de retorno | DESCRIÇÃO |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | Concatena várias cadeias de caracteres. Cada valor deve ser uma cadeia de caracteres. Use o `to-string` digite a expressão para converter outros tipos de valor para cadeia de caracteres, se necessário. |
| `['downcase', string]` | string | Converte a cadeia de caracteres especificada em minúsculas. |
| `['upcase', string]` | string | Converte a cadeia de caracteres especificada em maiusculas. |

**Exemplo**

O exemplo a seguir converte o `temperature` propriedade do ponto em uma cadeia de caracteres de recurso e, em seguida, concatena "° F" ao final dele.

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

A expressão acima renderiza um pin no mapa com o texto "64° F" sobreposta sobre ele, conforme mostrado na imagem abaixo.

<center>

![Exemplo de expressão de operador de cadeia de caracteres](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolar e expressões de etapa

Interpolar e expressões de etapa podem ser usadas para calcular valores ao longo de uma função de curva ou etapa interpolada. Essas expressões têm em uma expressão que retorna um valor numérico como sua entrada, por exemplo `['get',  'temperature']`. O valor de entrada é avaliado em relação a pares de valores de entrada e saídas, chamado de "parar", para determinar o valor que melhor se adapta a função de curva ou etapa interpolada. Os valores de entrada para cada parada devem ser um número e estar em ordem crescente. Os valores de saída devem ser um número e a matriz de números ou uma cor.

### <a name="interpolate-expression"></a>Expressão de interpolação

Um `interpolate` expressão pode ser usada para calcular um conjunto de valores contínuo, suave pela interpolação entre valores de palavras irrelevantes. Um `interpolate` expressão que retorna valores de cor produz no resultado de quais valores são selecionados de um gradiente de cores.

Há três tipos de métodos de interpolação que podem ser usados em um `interpolate` expressão:
 
* `['linear']` -Linearmente interpola entre o par de paradas.
* `['exponential', base]` -Interpola exponencialmente entre as paradas. O `base` valor controla a taxa na qual a saída aumenta. Valores mais altos Verifique a saída mais aumentar até o final de alto do intervalo. Um `base` valor próximo a 1 produz uma saída que aumenta linearmente mais.
* `['cubic-bezier', x1, y1, x2, y2]` -Interpola usando um [curva de Bézier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definido pelos pontos de controle fornecido.

Aqui está um exemplo da aparência esses diferentes tipos de interpolações. 

| Linear  | Exponencial | Cubic Bezier |
|---------|-------------|--------------|
| ![Gráfico de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Gráfico de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Gráfico de interpolação de Bézier cúbico](media/how-to-expressions/bezier-curve-interpolation.png) |

O pseudocódigo a seguir define a estrutura do `interpolate` expressão. 

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

O exemplo a seguir usa uma `linear interpolate` expressão para definir o `color` propriedade de uma camada de bolha com base no `temperature` propriedade do recurso de ponto. Se o `temperature` valor é menor que 60, "azul" será retornado, se entre 60 e menor que 70, amarelo será retornado, se entre 70 e menos de 80, "orange" será retornado, se 80 ou superior, "vermelho" será retornado.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de interpolação](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Expressão de etapa

Um `step` expressão pode ser usada para calcular valores discretos, nível resultados avaliando uma [constante em pedaços função](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definido pelo será interrompido. 

O pseudocódigo a seguir define a estrutura do `step` expressão. 

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

Expressões de etapa retornam o valor de saída de parada de logo antes do valor de entrada ou o primeiro valor de entrada se a entrada for menor que a primeira parada. 

**Exemplo**

O exemplo a seguir usa uma `step` expressão para definir o `color` propriedade de uma camada de bolha com base no `temperature` propriedade do recurso de ponto. Se o `temperature` valor é menor que 60, "azul" será retornado, se entre 60 e menor que 70, "yellow" será retornado, se entre 70 e menos de 80, "orange" será retornado, se 80 ou superior, "vermelho" será retornado.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.
 
<center>

![Exemplo de expressão de etapa](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Expressões de específico de camada

Expressões especiais que se aplicam somente a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade de mapa de calor

Uma expressão de densidade de mapa de calor recupera o valor de densidade de mapa de calor para cada pixel em uma camada do mapa de calor e é definida como `['heatmap-density']`. Esse valor é um número entre `0` e `1` e é usado em combinação com um `interpolation` ou `step` expressão para definir o gradiente de cores usado para colorir o mapa de calor. Esta expressão pode ser usada apenas na [opção de cor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) da camada do mapa de calor.

> [!TIP]
> A cor no índice 0 em uma expressão de interpolação ou a cor padrão de uma cor de etapa, define a cor da área em que não há nenhum dado e pode ser usado para definir uma cor de plano de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto. 

**Exemplo**

Este exemplo usa uma expressão de interpolação de linha de comando para criar um gradiente de cor suave para a renderização de mapa de calor. 

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

Além de usar um gradiente suave para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de intervalos usando uma `step` expressão. Usando um `step` expressão para colorir o mapa de calor divide a densidade visualmente em intervalos que mais ainda se parece com um mapa de estilo do contorno ou radar.  

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

Para obter mais informações, consulte o [adicionar uma camada do mapa de calor](map-add-heat-map-layer.md) documentação.

### <a name="line-progress-expression"></a>Expressão de progresso da linha

Recupera o progresso ao longo de uma linha em uma camada de linha de gradação de uma expressão de progresso de linha e é definida como `['line-progress']`. Esse valor é um número entre 0 e 1 e é usado em combinação com um `interpolation` ou `step` expressão. Esta expressão pode ser usada apenas com o [strokeGradient opção]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) da camada de linha. 

> [!NOTE]
> O `strokeGradient` opção da camada de linha requer o `lineMetrics` opção de fonte de dados a ser definida `true`.

**Exemplo**

O exemplo a seguir usa o `['line-progress']` expressão para aplicar um gradiente de cores ao traço de uma linha.

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

[Veja o exemplo em tempo real](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato do campo de texto

A expressão de formato do campo de texto pode ser usada com o `textField` opção das camadas de símbolo `textOptions` propriedade para fornecer a formatação de texto misto. Essa expressão permite que um conjunto de cadeias de caracteres de entrada e as opções de formatação a ser especificado. As opções a seguir podem ser especificadas para cada cadeia de caracteres de entrada nesta expressão.

 * `'font-scale'` -Especifica o fator de escala para o tamanho da fonte. Se for especificado, esse valor substituirá o `size` propriedade do `textOptions` para a cadeia de caracteres individual.
 * `'text-font'` -Especifica um ou mais famílias de fontes que devem ser usadas para essa cadeia de caracteres. Se for especificado, esse valor substituirá o `font` propriedade do `textOptions` para a cadeia de caracteres individual.

O pseudocódigo a seguir define a estrutura de expressão de formato do campo de texto. 

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

O exemplo a seguir formata o campo de texto, adicionando uma fonte em negrito e escalar verticalmente o tamanho da fonte a `title` propriedade do recurso. Este exemplo também adiciona o `subtitle` propriedade do recurso em uma nova linha, com uma escala de reduzir o tamanho da fonte.

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

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

Essa camada será renderizado o recurso de ponto, conforme mostrado na imagem abaixo:
 
<center>

![Imagem do recurso de ponto com o campo de texto formatado](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Expressão de formato de número

O `number-format` expressão pode ser usada apenas com o `textField` opção de uma camada de símbolo. Essa expressão converte o número fornecido em uma cadeia de caracteres formatada. Essa expressão encapsula do JavaScript [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) de função e suporta o seguinte conjunto de opções.

 * `locale` -Especifica esta opção para converter números em cadeias de caracteres de forma que se alinha com o idioma especificado. Passar uma [marca de idioma BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) para essa opção.
 * `currency` – Para converter o número em uma cadeia de caracteres que representa uma moeda. Os valores possíveis são os [códigos de moeda ISO 4217](https://en.wikipedia.org/wiki/ISO_4217), como "USD" para dólar americano, "EUR" para o euro ou "CNY" para o RMB chinês.
 * `'min-fraction-digits'` -Especifica o número mínimo de casas decimais a serem incluídos na versão de cadeia de caracteres do número.
 * `'max-fraction-digits'` -Especifica o número máximo de casas decimais a serem incluídos na versão de cadeia de caracteres do número.

O pseudocódigo a seguir define a estrutura de expressão de formato do campo de texto. 

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

O exemplo a seguir usa uma `number-format` expressão para modificar como o `revenue` propriedade do recurso de ponto é renderizada no `textField` opção de um símbolo de camada, de modo que ele seja exibido um valor em dólar americano.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Essa camada será renderizado o recurso de ponto, conforme mostrado na imagem abaixo:

<center>

![Exemplo de expressão de formato de número](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Expressão de zoom

Um `zoom` expressão é usada para recuperar o nível de zoom atual do mapa em tempo de renderização e é definida como `['zoom']`. Essa expressão retorna um número entre o intervalo de nível de zoom mínimo e máximo do mapa. Usando essa expressão permite que os estilos seja modificado dinamicamente conforme o nível de zoom do mapa é alterado. O `zoom` expressão pode ser usada somente com `interpolate` e `step` expressões.

**Exemplo**

Por padrão, os raios dos pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. Como o mapa é ampliado as agregações de dados juntas e a camada do mapa de calor parece diferente. Um `zoom` expressão pode ser usada para dimensionar o radius para cada nível de zoom, de modo que cada ponto de dados abrange a mesma área física do mapa. Isso tornará a camada do mapa de calor parecer mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels verticalmente e horizontalmente como o nível de zoom anterior. Dimensionar o radius, de modo que ela duplica a cada nível de zoom, você criará um mapa de calor parece consistente em todos os níveis de zoom. Isso pode ser feito usando o `zoom` expressão com um `base 2 exponential interpolation` expressão, conforme mostrado abaixo. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[Veja o exemplo em tempo real](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Expressões de associação de variável

Expressões de associação de variável armazenam os resultados de um cálculo em uma variável para que ele pode ser referenciado em outro lugar em uma expressão várias vezes sem precisar recalcular a ele. Isso é uma otimização útil para expressões que envolvem muitos cálculos

| Expression | Tipo de retorno | DESCRIÇÃO |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let'<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;Value2: qualquer,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Armazena um ou mais valores como variáveis para uso pelo `var` expressão na expressão de filho que retorna o resultado. |
| `['var', name: string]` | qualquer | Faz referência a uma variável que foi criada usando o `let` expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita em relação à taxa de temperatura e, em seguida, usa um `case` expressão para avaliar as diferentes operações Boolianas nesse valor. O `let` expressão é usada para armazenar a receita em relação à taxa de temperatura para que ele só precisa ser calculada uma vez e o `var` expressão fizer referência a essa variável sempre que necessário sem precisar recalcular a ele.

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

Consulte os seguintes artigos para obter mais exemplos de código que implementam expressões:

> [!div class="nextstepaction"] 
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [Adicionar formas](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)

Saiba mais sobre as opções de camada que dão suporte a expressões:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
