---
title: Expressões de estilo controladas por dados no Android Maps | Mapas do Microsoft Azure
description: Saiba mais sobre expressões de estilo controladas por dados. Veja como usar essas expressões no Azure Maps SDK do Android para ajustar os estilos em mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 1babf1feb550109486089c45469ab4ce32f72cb3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097407"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Expressões de estilo controladas por dados (SDK do Android)

As expressões permitem aplicar lógica de negócios a opções de estilo que observam as propriedades definidas em cada forma em uma fonte de dados. As expressões podem filtrar dados em uma fonte de dados ou em uma camada. As expressões podem consistir em lógica condicional, como If-Statements. E, eles podem ser usados para manipular dados usando: operadores de cadeia de caracteres, operadores lógicos e operadores matemáticos.

Os estilos controlados por dados reduzem a quantidade de código necessária para implementar a lógica de negócios em relação ao estilo. Quando usado com camadas, as expressões são avaliadas no momento da renderização em um thread separado. Essa funcionalidade fornece maior desempenho em comparação com a avaliação da lógica de negócios no thread da interface do usuário.

O Azure Maps SDK do Android dá suporte a quase todas as mesmas expressões de estilo que o SDK da Web do Azure Maps, de modo que todos os mesmos conceitos descritos nas [expressões de estilo controladas por dados (SDK da Web)](data-driven-style-expressions-web-sdk.md) podem ser transportadas em um aplicativo Android. Todas as expressões de estilo no Azure Maps SDK do Android estão disponíveis no `com.microsoft.azure.maps.mapcontrol.options.Expression` namespace. Há muitos tipos diferentes de expressões de estilo.

| Tipo de expressões | Descrição |
|---------------------|-------------|
| [Expressões boolianas](#boolean-expressions) | As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas. |
| [Expressões de cores](#color-expressions) | As expressões de cor facilitam a criação e a manipulação de valores de cores. |
| [Expressões condicionais](#conditional-expressions) | As expressões condicionais fornecem operações lógicas que são como instruções IF-. |
| [Expressões de dados](#data-expressions) | Fornece acesso aos dados de propriedade em um recurso. |
| [Expressões de interpolação e etapas](#interpolate-and-step-expressions) | As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. |
| [Expressões baseadas em JSON](#json-based-expressions) | Facilita a reutilização de expressões baseadas em JSON brutos de estilo criadas para o SDK da Web com o SDK do Android. |  
| [Expressões específicas de camada](#layer-specific-expressions) | Expressões especiais que só se aplicam a uma única camada. |
| [Expressões matemáticas](#math-expressions) | Fornece operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework. |
| [Expressões de operador de cadeia de caracteres](#string-operator-expressions) | Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso. |
| [Expressões de tipo](#type-expressions) | As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos. |
| [Expressões de associação de variáveis](#variable-binding-expressions) | Expressões de associação de variáveis armazenam os resultados de um cálculo em uma variável e referenciadas em outro lugar em uma expressão várias vezes sem precisar recalcular o valor armazenado. |
| [Expressão de zoom](#zoom-expression) | Recupera o nível de zoom atual do mapa no momento da renderização. |

> [!NOTE]
> A sintaxe para expressões é basicamente idêntica em Java e Kotlin. Se você tiver a documentação definida como Kotlin, mas ver blocos de código para Java, o código será idêntico em ambos os idiomas.

Todos os exemplos nesta seção do documento usam o recurso a seguir para demonstrar diferentes maneiras em que essas expressões podem ser usadas.

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

O código a seguir mostra como criar manualmente esse recurso geojson em um aplicativo.

::: zone pivot="programming-language-java-android"

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add properties to the feature.
feature.addNumberProperty("id", 123)
feature.addStringProperty("entityType", "restaurant")
feature.addNumberProperty("revenue", 12345)
feature.addStringProperty("subTitle", "Building 40")
feature.addNumberProperty("temperature", 64)
feature.addStringProperty("title", "Cafeteria")
feature.addStringProperty("zoneColor", "purple")

val abcArray = JsonArray()
abcArray.add("a")
abcArray.add("b")
abcArray.add("c")

feature.addProperty("abcArray", abcArray)

val array1 = JsonArray()
array1.add("a")
array1.add("b")

val array2 = JsonArray()
array1.add("x")
array1.add("y")

val array2d = JsonArray()
array2d.add(array1)
array2d.add(array2)

feature.addProperty("array2d", array2d)

val style = JsonObject()
style.addProperty("fillColor", "red")

feature.addProperty("_style", style)
```

::: zone-end

O código a seguir mostra como desserializar a versão em cadeias do objeto JSON em um recurso geojson em um aplicativo.

::: zone pivot="programming-language-java-android"

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}"

val feature = Feature.fromJson(featureString)
```

::: zone-end

## <a name="json-based-expressions"></a>Expressões baseadas em JSON

O SDK da Web do Azure Maps também oferece suporte a expressões de estilo controladas por dados que são representadas usando uma matriz JSON. Essas mesmas expressões podem ser recriadas usando a `Expression` classe nativa no SDK do Android. Como alternativa, essas expressões baseadas em JSON podem ser convertidas em uma cadeia de caracteres usando uma função Web, como `JSON.stringify` e transmitida para o `Expression.raw(String rawExpression)` método. Por exemplo, considere a seguinte expressão JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

A versão em cadeias da expressão acima seria `"['get','title']"` e pode ser lida na SDK do Android da seguinte maneira.

::: zone pivot="programming-language-java-android"

```java
Expression exp = Expression.raw("['get','title']")
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val exp = Expression.raw("['get','title']")
```

::: zone-end

Usar essa abordagem pode facilitar a reutilização de expressões de estilo entre aplicativos móveis e Web que usam o Azure Maps.

Este vídeo fornece uma visão geral do estilo controlado por dados no Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Expressões de dados

As expressões de dados fornecem acesso aos dados de propriedade em um recurso.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `accumulated()` | número | Obtém o valor de uma propriedade de cluster acumulado até o momento. |
| `at(number | Expression, Expression)` | value | Recupera um item de uma matriz. |
| `geometryType()` | string | Obtém o tipo de geometria do recurso: ponto, MultiPoint, LineString, MultiLineString, polígono, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | value | Obtém o valor da propriedade das propriedades do objeto fornecido. Retornará NULL se a propriedade solicitada estiver ausente. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | booleano | Determina se as propriedades de um recurso têm a propriedade especificada. |
| `id()` | value | Obtém a ID do recurso se ele tiver um. |
| `in(string | number | Expression, Expression)` | booleano | Determina se um item existe em uma matriz |
| `length(string | Expression)` | número | Obtém o comprimento de uma cadeia de caracteres ou de uma matriz. |
| `properties()`| value | Obtém o objeto de propriedades do recurso. |

As seguintes expressões de estilo do SDK Web não têm suporte no SDK do Android:

- índice-de
- fatia

**Exemplos**

As propriedades de um recurso podem ser acessadas diretamente em uma expressão usando uma `get` expressão. Este exemplo usa o `zoneColor` valor do recurso para especificar a Propriedade Color de uma camada Bubble.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
)
```

::: zone-end

O exemplo acima funcionará bem, se todos os recursos de ponto tiverem a `zoneColor` propriedade. Se não forem, a cor provavelmente retornará para "preto". Para modificar a cor de fallback, use uma `switchCase` expressão em combinação com a `has` expressão para verificar se a propriedade existe. Se a propriedade não existir, retorne uma cor de fallback.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
)
```

::: zone-end

As camadas de bolhas e símbolos renderizarão as coordenadas de todas as formas em uma fonte de dados, por padrão. Esse comportamento pode realçar os vértices de um polígono ou de uma linha. A `filter` opção da camada pode ser usada para limitar o tipo de geometria dos recursos que ele renderiza, usando uma `geometryType` expressão dentro de uma expressão booliana. O exemplo a seguir limita uma camada de bolha para que apenas os `Point` recursos sejam renderizados.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(eq(geometryType(), "Point"))
)
```

::: zone-end

O exemplo a seguir permite que os `Point` `MultiPoint` recursos e sejam renderizados.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
)
```

::: zone-end

Da mesma forma, a estrutura de polígonos será renderizada em camadas de linha. Para desabilitar esse comportamento em uma camada de linha, adicione um filtro que permita `LineString` apenas `MultiLineString` recursos e.  

Aqui estão alguns exemplos adicionais de como usar expressões de dados:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Expressões matemáticas

As expressões matemáticas fornecem operadores matemáticos para executar cálculos controlados por dados dentro do Expression Framework.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `abs(number | Expression)` | número | Calcula o valor absoluto do número especificado. |
| `acos(number | Expression)` | número | Calcula o arco cosseno do número especificado. |
| `asin(number | Expression)` | número | Calcula o arco seno do número especificado. |
| `atan(number | Expression)` | número | Calcula o arco tangente do número especificado. |
| `ceil(number | Expression)` | número | Arredonda o número para cima até o próximo número inteiro. |
| `cos(number | Expression)` | número | Calcula o cos do número especificado. |
| `division(number, number)` \| `division(Expression, Expression)` | número | Divide o primeiro número pelo segundo número. Expressão equivalente do SDK da Web: `/` |
| `e()` | número | Retorna a constante matemática `e` . |
| `floor(number | Expression)` | número | Arredonda o número para baixo até o inteiro completo anterior. |
| `log10(number | Expression)` | número | Calcula o logaritmo de base 10 do número especificado. |
| `log2(number | Expression)` | número | Calcula o logaritmo de base dois do número especificado. |
| `ln(number | Expression)` | número | Calcula o logaritmo natural do número especificado. |
| `ln2()` | número | Retorna a constante matemática `ln(2)` . |
| `max(numbers... | expressions...)` | número | Calcula o número máximo no conjunto de números especificado. |
| `min(numbers... | expressions...)` | número | Calcula o número mínimo no conjunto de números especificado. |
| `mod(number, number)` \| `mod(Expression, Expression)` | número | Calcula o resto ao dividir o primeiro número pelo segundo número. Expressão equivalente do SDK da Web: `%` |
| `pi()` | número | Retorna a constante matemática `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | número | Calcula o valor do primeiro valor elevado à potência do segundo número. |
| `product(numbers... | expressions...)` | número | Multiplica os números especificados juntos. Expressão equivalente do SDK da Web: `*` |
| `round(number | Expression)` | número | Arredonda o número para o inteiro mais próximo. Valores intermediários são arredondados para longe de zero. Por exemplo, `round(-1.5)` é avaliado como `-2` . |
| `sin(number | Expression)` | número | Calcula o seno do número especificado. |
| `sqrt(number | Expression)` | número | Calcula a raiz quadrada do número especificado. |
| `subtract(number | Expression` | número | Subtrai 0 pelo número especificado. |
| `subtract(number | Expression, number | Expression)` | número | Subtrai os primeiros números pelo segundo número. |
| `sum(numbers... | expressions...)` | número | Calcula a soma dos números especificados. |
| `tan(number | Expression)` | número | Calcula a tangente do número especificado. |

## <a name="boolean-expressions"></a>Expressões boolianas

As expressões booleanas fornecem um conjunto de expressões de operadores boolianos para avaliar comparações boolianas.

Ao comparar valores, a comparação é estritamente digitada. Os valores de tipos diferentes são sempre considerados desiguais. Os casos em que os tipos são conhecidos como diferentes no momento da análise são considerados inválidos e produzirão um erro de análise.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `all(Expression...)` | booleano | Retorna `true` se todas as entradas forem `true` , `false` caso contrário,. |
| `any(Expression...)` | booleano | Retorna `true` se alguma das entradas for `true` , `false` caso contrário,. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se os valores de entrada forem iguais, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se a primeira entrada é estritamente maior do que a segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se a primeira entrada for maior ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se a primeira entrada é estritamente menor do que a segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se a primeira entrada for menor ou igual à segunda, `false` caso contrário. Os argumentos são necessários para serem tanto cadeias de caracteres quanto números. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | booleano | Retorna `true` se os valores de entrada não forem iguais, `false` caso contrário. |
| `not(Expression | boolean)` | booleano | Negação lógica. Retorna `true` se a entrada é `false` e `false` se a entrada é `true` . |

## <a name="conditional-expressions"></a>Expressões condicionais

As expressões condicionais fornecem operações lógicas que são como instruções IF-.

As expressões a seguir executam operações lógicas condicionais nos dados de entrada. Por exemplo, a `switchCase` expressão fornece a lógica "if/then/else" enquanto a `match` expressão é como uma "switch-Statement".

### <a name="switch-case-expression"></a>Alternar expressão Case

Uma `switchCase` expressão é um tipo de expressão condicional que fornece a lógica "if/then/else". Esse tipo de expressão percorre uma lista de condições booleanas. Retorna o valor de saída da primeira condição booliana a ser avaliada como true.

O pseudocódigo a seguir define a estrutura da `switchCase` expressão.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Exemplo**

O exemplo a seguir percorre diferentes condições booleanas até encontrar uma que seja avaliada como `true` e, em seguida, retorna esse valor associado. Se nenhuma condição booliana for avaliada como `true` , um valor de fallback será retornado.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
)
```

::: zone-end

### <a name="match-expression"></a>Expressão de correspondência

Uma `match` expressão é um tipo de expressão condicional que fornece a instrução switch, como lógica. A entrada pode ser qualquer expressão como, `get( "entityType")` que retorna uma cadeia de caracteres ou um número. Cada parada deve ter um rótulo que seja um único valor literal ou uma matriz de valores literais, cujos valores devem ser todas as cadeias de caracteres ou todos os números. A entrada corresponde se qualquer um dos valores na matriz corresponder. Cada rótulo de parada deve ser exclusivo. Se o tipo de entrada não corresponder ao tipo dos rótulos, o resultado será o valor de fallback padrão.

O pseudocódigo a seguir define a estrutura da `match` expressão.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Exemplos**

O exemplo a seguir examina a `entityType` propriedade de um recurso de ponto em uma camada de bolha que procura uma correspondência. Se encontrar uma correspondência, esse valor especificado será retornado ou retornará o valor de fallback.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

O exemplo a seguir usa uma matriz para listar um conjunto de rótulos que devem retornar o mesmo valor. Essa abordagem é muito mais eficiente do que listar cada rótulo individualmente. Nesse caso, se a `entityType` propriedade for "restaurante" ou "grocery_store", a cor "vermelho" será retornada.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(arrayOf("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
)
```

::: zone-end

### <a name="coalesce-expression"></a>Expressão de adesão

Uma `coalesce` expressão percorre um conjunto de expressões até que o primeiro valor não nulo seja obtido e retorne esse valor.

O pseudocódigo a seguir define a estrutura da `coalesce` expressão.

```java
coalesce(Expression... input)
```

**Exemplo**

O exemplo a seguir usa uma `coalesce` expressão para definir a `textField` opção de uma camada de símbolo. Se a `title` propriedade estiver ausente no recurso ou definida como `null` , a expressão tentará procurar a `subTitle` propriedade, se ela estiver ausente ou `null` , em seguida, retornará a uma cadeia de caracteres vazia.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
)
```

::: zone-end

## <a name="type-expressions"></a>Expressões de tipo

As expressões de tipo fornecem ferramentas para testar e converter diferentes tipos de dados, como cadeias de caracteres, números e valores Boolianos.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `array(Expression)` | Objeto [] | Declara que a entrada é uma matriz. |
| `bool(Expression)` | booleano | Declara que o valor de entrada é um booliano. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | Agrupador | Retorna um Agrupador para uso em operações de comparação dependentes de localidade. As opções de diferenciação de maiúsculas e minúsculas e de sinais diacríticos são padronizadas como falso. O argumento locale especifica a marca de idioma IETF da localidade a ser usada. Se nenhum for fornecido, a localidade padrão será usada. Se a localidade solicitada não estiver disponível, o Agrupador usará uma localidade de fallback definida pelo sistema. Use resolvid-locale para testar os resultados do comportamento de fallback de localidade.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | objeto de objeto de cadeia de caracteres de número booliano \| \| \| \| [] | Retorna um valor de objeto ou matriz literal. Use esta expressão para impedir que uma matriz ou objeto seja avaliado como uma expressão. Isso é necessário quando uma matriz ou objeto precisa ser retornado por uma expressão. |
| `number(Expression)` | número | Declara que o valor de entrada é um número. |
| `object(Expression)` | Objeto | Declara que o valor de entrada é um objeto. |
| `string(Expression)` | string | Declara que o valor de entrada é uma cadeia de caracteres. |
| `toArray(Expression)` | Objeto [] | Converte a expressão em uma matriz de objetos JSON. |
| `toBool(Expression)` | booleano | Converte o valor de entrada em um booliano. |
| `toNumber(Expression)` | número | Converte o valor de entrada em um número, se possível. |
| `toString(Expression)` | string | Converte o valor de entrada em uma cadeia de caracteres. |
| `typeoOf(Expression)` | string | Retorna uma cadeia de caracteres que descreve o tipo do valor especificado. |

## <a name="color-expressions"></a>Expressões de cores

As expressões de cor facilitam a criação e a manipulação de valores de cores.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `color(int)` | cor | Converte um valor inteiro de cor em uma expressão de cor. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | cor | Cria um valor de cor de componentes *vermelhos*, *verdes* e *azuis* que devem variar entre `0` e e `255` um componente alfa do `1` . Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | cor | Cria um valor de cor de componentes *vermelhos*, *verdes* e *azuis* que devem variar entre `0` e e `255` um componente alfa dentro de um intervalo de `0` e `1` . Se algum componente estiver fora do intervalo, a expressão será um erro. |
| `toColor(Expression)` | cor  | Converte o valor de entrada em uma cor. |
| `toRgba(Expression)` | cor | Retorna uma matriz de quatro elementos que contém os componentes vermelho, verde, azul e alfa da cor de entrada, nessa ordem. |

**Exemplo**

O exemplo a seguir cria um valor de cor RGB que tem um valor *vermelho* de `255` e valores *verdes* e *azuis* que são calculados multiplicando `2.5` pelo valor da `temperature` propriedade. À medida que a temperatura for alterada, a cor será alterada para diferentes tons de *vermelho*.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
)
```

::: zone-end

Se todos os parâmetros de cor forem números, não será necessário encapsulá-los com a `literal` expressão. Por exemplo: 

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
)
```

::: zone-end

> [!TIP]
> Os valores de cor da cadeia de caracteres podem ser convertidos em uma cor usando o `android.graphics.Color.parseColor` método. O seguinte converte uma cadeia de caracteres de cor hexadecimal em uma expressão de cor que pode ser usada com uma camada.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Expressões de operador de cadeia de caracteres

Expressões do operador String executam operações de conversão em cadeias de caracteres como concatenar e converter o caso.

| Expression | Tipo de retorno | Descrição |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | string | Concatena várias cadeias de caracteres juntas. Cada valor deve ser uma cadeia de caracteres. Use a `toString` expressão Type para converter outros tipos de valor em String, se necessário. |
| `downcase(string)` \| `downcase(Expression)` | string | Converte a cadeia de caracteres especificada em minúsculas. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| booleano | Determina se a cadeia de caracteres de entrada usa um conjunto de caracteres suportado pela pilha de fontes atual. Por exemplo: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | string | Retorna a marca de idioma da IETF da localidade que está sendo usada pelo Agrupador fornecido. Isso pode ser usado para determinar a localidade padrão do sistema ou para determinar se uma localidade solicitada foi carregada com êxito. |
| `upcase(string)` \| `upcase(Expression)` | string | Converte a cadeia de caracteres especificada em maiúsculas. |

**Exemplo**

O exemplo a seguir converte a `temperature` Propriedade do recurso Point em uma cadeia de caracteres e, em seguida, concatena "° f" ao final dela.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
)
```

::: zone-end

A expressão acima renderiza um PIN no mapa com o texto "64 ° f" sobreposto sobre ele, conforme mostrado na imagem abaixo.

![Exemplo de expressão de operador de cadeia de caracteres](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Expressões de interpolação e etapas

As expressões Interpolate e Step podem ser usadas para calcular valores ao longo de uma curva interpolada ou função Step. Essas expressões assumem uma expressão que retorna um valor numérico como sua entrada, por exemplo `get("temperature")` . O valor de entrada é avaliado em relação aos pares de valores de entrada e saída, para determinar o valor que melhor se adapta à função de curva ou etapa interpolada. Os valores de saída são chamados de "paradas". Os valores de entrada para cada parada devem ser um número e estar em ordem crescente. Os valores de saída devem ser um número e uma matriz de números ou uma cor.

### <a name="interpolate-expression"></a>Expressão Interpolate

Uma `interpolate` expressão pode ser usada para calcular um conjunto de valores suave e contínuo, interpolando entre os valores de parada. Uma `interpolate` expressão que retorna valores de cor produz um gradiente de cor no qual os valores de resultado são selecionados. A `interpolate` expressão tem os seguintes formatos:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Há três tipos de métodos de interpolação que podem ser usados em uma `interpolate` expressão:

| Name | Descrição |
|------|-------------|
| `linear()` | Interpola linearmente entre o par de paradas.  |
| `exponential(number)` \| `exponential(Expression)` | Interpola exponencialmente entre as interrupções. Uma "base" é especificada e controla a taxa na qual a saída aumenta. Valores mais altos fazem com que a saída aumente mais em direção ao alto fim do intervalo. Um valor "base" próximo a 1 produz uma saída que aumenta de forma linear.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpola o uso de uma [curva de Bézier cúbica](https://developer.mozilla.org/docs/Web/CSS/timing-function) definida pelos pontos de controle fornecidos. |

A `stop` expressão tem o formato `stop(stop, value)` .

Veja um exemplo de como esses diferentes tipos de interpolações se parecem.

| Linear  | Exponencial | Bézier cúbica |
|---------|-------------|--------------|
| ![Grafo de interpolação linear](media/how-to-expressions/linear-interpolation.png) | ![Grafo de interpolação exponencial](media/how-to-expressions/exponential-interpolation.png) | ![Grafo de interpolação Bézier cúbica](media/how-to-expressions/bezier-curve-interpolation.png) |

**Exemplo**

O exemplo a seguir usa uma `linear interpolate` expressão para definir a `bubbleColor` propriedade de uma camada de bolha com base na `temperature` Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado. Se ele estiver entre 60 e menor que 70, amarelo será retornado. Se estiver entre 70 e menor que 80, "laranja" ( `#FFA500` ) será retornado. Se for 80 ou superior, será retornado "Red".

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.

![Exemplo de expressão Interpolate](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Expressão de etapa

Uma `step` expressão pode ser usada para calcular valores de resultado discretos, avaliando uma [função piecewise](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) definida por paradas.

A `interpolate` expressão tem os seguintes formatos:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

As expressões de etapa retornam o valor de saída da parada logo antes do valor de entrada ou o primeiro valor de entrada se a entrada for menor que a primeira parada.

**Exemplo**

O exemplo a seguir usa uma `step` expressão para definir a `bubbleColor` propriedade de uma camada de bolha com base na `temperature` Propriedade do recurso de ponto. Se o `temperature` valor for menor que 60, "Blue" será retornado. Se estiver entre 60 e menor que 70, "amarelo" será retornado. Se estiver entre 70 e menor que 80, "laranja" será retornado. Se for 80 ou superior, será retornado "Red".

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
)
```

::: zone-end

A imagem a seguir demonstra como as cores são escolhidas para a expressão acima.

![Exemplo de expressão de etapa](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Expressões específicas de camada

Expressões especiais que se aplicam somente a camadas específicas.

### <a name="heat-map-density-expression"></a>Expressão de densidade do mapa de calor

Uma expressão de densidade do mapa de calor recupera o valor de densidade do mapa de calor para cada pixel em uma camada do mapa de calor e é definida como `heatmapDensity` . Esse valor é um número entre `0` e `1` . Ele é usado em combinação com uma `interpolation` `step` expressão or para definir o gradiente de cor usado para colorir o mapa de calor. Essa expressão só pode ser usada na `heatmapColor` opção da camada do mapa de calor.

> [!TIP]
> A cor no índice 0, em uma expressão de interpolação ou a cor padrão de uma cor da etapa, define a cor da área onde não há dados. A cor no índice 0 pode ser usada para definir uma cor de plano de fundo. Muitos preferem definir esse valor como transparente ou semitransparente preto.

**Exemplo**

Este exemplo usa uma expressão de interpolação de capa para criar um gradiente de cor suave para renderizar o mapa de calor.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
)
```

::: zone-end

Além de usar um gradiente suave para colorir um mapa de calor, as cores podem ser especificadas dentro de um conjunto de intervalos usando uma `step` expressão. Usar uma `step` expressão para colorir o mapa de calor visualmente divide a densidade em intervalos que se assemelham a um mapa de estilo de contorno ou de radar.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

Para obter mais informações, consulte a documentação [Adicionar uma camada do mapa de calor](map-add-heat-map-layer-android.md) .

### <a name="line-progress-expression"></a>Expressão de progresso da linha

Uma expressão de progresso de linha recupera o progresso ao longo de uma linha de gradiente em uma camada de linha e é definida como `lineProgress()` . Esse valor é um número entre 0 e 1. Ele é usado em combinação com uma `interpolation` `step` expressão ou. Essa expressão só pode ser usada com a `strokeGradient` opção da camada de linha.

> [!NOTE]
> A `strokeGradient` opção da camada de linha requer a `lineMetrics` opção da fonte de dados a ser definida como `true` .

**Exemplo**

Este exemplo usa a `lineProgress()` expressão para aplicar um gradiente de cor ao traço de uma linha.

::: zone pivot="programming-language-java-android"

```java
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
)
```

::: zone-end

[Consulte o exemplo ao vivo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Expressão de formato de campo de texto

A `format` expressão pode ser usada com a `textField` opção da camada de símbolo para fornecer formatação de texto misto. Essa expressão usa uma ou mais `formatEntry` expressões que especificam uma cadeia de caracteres e um conjunto de `formatOptions` para acrescentar ao campo de texto.

| Expression | Descrição |
|------------|-------------|
| `format(Expression...)` | Retorna o texto formatado que contém anotações para uso em entradas de campo de texto de formato misto. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Retorna uma entrada de cadeia de caracteres formatada para uso na `format` expressão. |

As seguintes opções de formato disponíveis são:

| Expression | Descrição |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Especifica o fator de dimensionamento para o tamanho da fonte. Se especificado, esse valor substituirá a `textSize` propriedade da cadeia de caracteres individual. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Especifica uma cor a ser aplicada a um texto durante a renderização. |

**Exemplo**

O exemplo a seguir formata o campo de texto adicionando uma fonte em negrito e dimensionando o tamanho da fonte da `title` Propriedade do recurso. Este exemplo também adiciona a `subTitle` Propriedade do recurso em uma nova linha, com um tamanho de fonte reduzido.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(arrayOf("StandardFont-Bold")),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
)
```

::: zone-end

Essa camada renderizará o recurso de ponto, conforme mostrado na imagem abaixo:

![Imagem do recurso de ponto com campo de texto formatado](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Expressão de zoom

Uma `zoom` expressão é usada para recuperar o nível de zoom atual do mapa no momento da renderização e é definida como `zoom()` . Essa expressão retorna um número entre o intervalo de nível de zoom mínimo e máximo do mapa. Os controles de mapa interativos do Azure Maps para Web e Android dão suporte a 25 níveis de zoom, numerados de 0 a 24. O uso da `zoom` expressão permite que os estilos sejam modificados dinamicamente, pois o nível de zoom do mapa é alterado. A `zoom` expressão só pode ser usada com `interpolate` `step` expressões e.

**Exemplo**

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que o mapa é ampliado, os dados são agregados juntos e a camada do mapa de calor é diferente. Uma `zoom` expressão pode ser usada para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Isso fará com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior. O dimensionamento do raio, de modo que ele se dobra em cada nível de zoom, criará um mapa de calor que se parece consistente em todos os níveis de zoom. Ele pode ser feito usando a `zoom` expressão com uma `base 2 exponential interpolation` expressão, com o conjunto de raio de pixel definido para o nível de zoom mínimo e um raio dimensionado para o nível máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)` mostrado abaixo.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
)
```

::: zone-end

## <a name="variable-binding-expressions"></a>Expressões de associação de variáveis

Expressões de associação de variáveis armazenam os resultados de um cálculo em uma variável. Portanto, os resultados de cálculo podem ser referenciados em outro lugar em uma expressão várias vezes. É uma otimização útil para expressões que envolvem muitos cálculos.

| Expression | Tipo de retorno | Descrição |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Armazena um ou mais valores como variáveis para uso pela `var` expressão na expressão filho que retorna o resultado. |
| `var(Expression expression)` \| `var(string variableName)` | Objeto | Faz referência a uma variável que foi criada usando a `let` expressão. |

**Exemplo**

Este exemplo usa uma expressão que calcula a receita relativa à taxa de temperatura e, em seguida, usa uma `case` expressão para avaliar diferentes operações boolianas nesse valor. A `let` expressão é usada para armazenar a receita relativa à taxa de temperatura, para que ela só precise ser calculada uma vez. A `var` expressão referencia essa variável com frequência, conforme necessário, sem precisar recalculá-la.

::: zone pivot="programming-language-java-android"

```java
BubbleLayer layer = new BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = BubbleLayer(source,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
)
```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as camadas que dão suporte a expressões:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer-android.md)
