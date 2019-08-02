---
title: Funções do sistema
description: Saiba mais sobre as funções de sistema do SQL no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342926"
---
# <a name="system-functions"></a>Funções do sistema

 O Cosmos DB fornece muitas funções SQL internas. As categorias de funções internas estão listadas abaixo.  
  
|Função|DESCRIÇÃO|  
|--------------|-----------------|  
|[Funções matemáticas](#mathematical-functions)|As funções matemáticas executam um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos, e retornam um valor numérico.|  
|[Funções de verificação de tipo](#type-checking-functions)|As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL.|  
|[Funções de cadeia de caracteres](#string-functions)|As funções de cadeia de caracteres executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano.|  
|[Funções de matriz](#array-functions)|As funções de matriz executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz.|
|[Funções de data e hora](#date-time-functions)|As funções de data e hora permitem que você obtenha a data UTC atual e a hora em duas formas; um carimbo de data numérico cujo valor é a época do Unix em milissegundos, ou como uma cadeia de caracteres que segue o formato ISO 8601.|
|[Funções espaciais](#spatial-functions)|As funções espaciais executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou booliano.|  

Veja a seguir uma lista de funções dentro de cada categoria:

| Grupo de funções | Operações |
|---------|----------|
| Funções matemáticas | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funções de verificação de tipo | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funções de cadeia de caracteres | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funções de matriz | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH e ARRAY_SLICE |
| Funções de data e hora | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Funções espaciais | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Se você estiver usando uma função definida pelo usuário (UDF) para o qual uma função interna agora está disponível, a função interna correspondente será executado mais rápido e mais eficiente.

A principal diferença entre as funções de Cosmos DB e ANSI SQL é que funções do Cosmos DB foram projetadas para funcionar bem com dados sem esquema e de esquema misto. Por exemplo, se uma propriedade está ausente ou tem um valor não numérico como `unknown`, o item será ignorado em vez de retornar um erro.

##  <a name="mathematical-functions"></a>Funções matemáticas  

As funções matemáticas executam um cálculo, com base em valores de entrada fornecidos como argumentos e retornam um valor numérico.

Você pode executar consultas como o exemplo a seguir:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

Aqui está uma tabela de funções matemáticas internas com suporte.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Retorna o valor absoluto (positivo) da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra os resultados do uso da função ABS em três números diferentes.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno).  
  
 **Sintaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna ACOS de -1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado de arco seno.  
  
 **Sintaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna ASIN de -1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado de arco tangente.  
  
 **Sintaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna o ATAN do valor especificado.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Retorna o valor principal do arco tangente de y/x, expresso em radianos.  
  
 **Sintaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir calcula o ATN2 dos componentes x e y especificados.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Retorna o menor valor de número inteiro maior ou igual à expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra valores numéricos positivos, negativos e zero com a função CEILING.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Retorna o cosseno trigonométrico do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir calcula o COS do ângulo especificado.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir calcula o COT do ângulo especificado.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos.  
  
 **Sintaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna o número de graus em um ângulo de radiano PI/2.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Retorna o maior inteiro menor ou igual à expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra valores numéricos positivos, negativos e zero com a função FLOOR.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Retorna o valor exponencial da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Comentários**  
  
  A constante **e** (2,718281...) é a base dos logaritmos naturais.  
  
  O expoente de um número é a constante **e** elevado à potência do número. Por exemplo, EXP(1.0) = e^1.0 = 2.71828182845905 e EXP(10) = e^10 = 22026.4657948067.  
  
  O exponencial do logaritmo natural de um número é o número em si: EXP (LOG (n)) = n. E o logaritmo natural do exponencial de um número é o número em si: LOG (EXP (n)) = n.  
  
  **Exemplos**  
  
  O exemplo a seguir declara uma variável e retorna o valor exponencial da variável especificada (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 O exemplo a seguir retorna o valor exponencial do logaritmo natural de 20 e o logaritmo natural do exponencial de 20. Como essas funções são funções inversas uma da outra, o valor retornado com o arredondamento para a matemática do ponto flutuante em ambos os casos é 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Retorna o logaritmo natural de expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
- `base`  
  
   Argumento numérico opcional que define a base para o logaritmo.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Comentários**  
  
  Por padrão, LOG() retorna o logaritmo natural. Você pode alterar a base do logaritmo para outro valor usando o parâmetro opcional de base.  
  
  O logaritmo natural é o logaritmo de base **e**, em que **e** é uma constante irracional aproximadamente igual a 2,718281828.  
  
  O logaritmo natural do exponencial de um número é o número em si: LOG( EXP( n ) ) = n. E o exponencial do logaritmo natural de um número é o número em si: EXP( LOG( n ) ) = n.  
  
  **Exemplos**  
  
  O exemplo a seguir declara uma variável e retorna o valor logarítmico da variável especificada (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 O exemplo a seguir calcula o LOG para o expoente de um número.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Retorna o logaritmo de base 10 da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Comentários**  
  
  As funções LOG10 e POWER estão inversamente relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n.  
  
  **Exemplos**  
  
  O exemplo a seguir declara uma variável e retorna o valor LOG10 da variável especificada (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Retorna o valor constante de PI.  
  
 **Sintaxe**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna o valor de PI.  
  
```  
SELECT PI() AS pi 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Retorna o valor da expressão especificada para a potência indicada.  
  
 **Sintaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
- `y`  
  
   É a potência à qual elevar `numeric_expression`.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir demonstra como elevar um número ao cubo.  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Retorna radianos quando uma expressão numérica é inserida em graus.  
  
 **Sintaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir usa alguns ângulos como entrada e retorna seus valores radianos correspondentes.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Este é o conjunto de resultados.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Retorna um valor numérico, arredondado para o valor inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Comentários**
  
  A operação de arredondamento executada segue o ponto médio arredondamento para cima. Se a entrada for uma expressão numérica que esteja exatamente entre dois inteiros, em seguida, o resultado será o mais próximo do valor inteiro diferente de zero.  
  
  |<numeric_expression>|Arredondado|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **Exemplos**  
  
  O exemplo a seguir arredonda os números positivos e negativos para o inteiro mais próximo.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Este é o conjunto de resultados.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Retorna o sinal positivo (+1), zero (0) ou negativo (-1) da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna os valores SIGN de números de -2 a 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Retorna o seno trigonométrico do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir calcula o SIN do ângulo especificado.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Retorna a raiz quadrada do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna as raízes quadradas dos números de 1 a 3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Retorna o quadrado do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna os quadrados dos números de 1 a 3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Retorna a tangente do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir calcula a tangente de PI()/2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a>TRUNC  
 Retorna um valor numérico, truncado para o valor inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
- `numeric_expression`  
  
   É uma expressão numérica.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir trunca os números positivos e negativos para o valor inteiro mais próximo.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Funções de verificação de tipo

As funções de verificação de tipo permitem que você verifique o tipo de uma expressão dentro de uma consulta SQL. Você pode usar funções de verificação de tipo para determinar os tipos de propriedades dentro dos itens em um piscar de olhos, quando eles são desconhecido ou variável. Aqui está uma tabela de funções de verificação do tipo de internas com suporte:

As funções a seguir dão suporte a verificação de tipo em relação aos valores de entrada e retornam, cada um, um valor booliano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a>IS_ARRAY  
 Retorna um valor booliano que indica se o tipo da expressão especificada é uma matriz.  
  
 **Sintaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a>IS_BOOL  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um booliano.  
  
 **Sintaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a>IS_DEFINED  
 Retorna um valor booliano que indica se um valor foi atribuído à propriedade.  
  
 **Sintaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica a presença de uma propriedade no documento JSON especificado. O primeiro retorna true, já que "a" está presente, mas o segundo retorna false porque "b" está ausente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a>IS_NULL  
 Retorna um valor booliano que indica se o tipo da expressão especificada é nulo.  
  
 **Sintaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a>IS_NUMBER  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um número.  
  
 **Sintaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a>IS_OBJECT  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um objeto JSON.  
  
 **Sintaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a>IS_PRIMITIVE  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um primitivo (cadeia de caracteres, booliano, numérico ou nulo).  
  
 **Sintaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Retorna um valor booliano que indica se o tipo da expressão especificada é uma cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
- `expression`  
  
   É qualquer expressão válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Funções de cadeia de caracteres

As funções escalares a seguir executam uma operação em um valor de cadeia de caracteres de entrada e retornam um valor de cadeia de caracteres, numéricos ou booleanos:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[COMPRIMENTO](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna a cadeia de caracteres concatenada do valor especificado.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda.  
  
 **Sintaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica se "abc" contém "ab" e contém "d".  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda.  
  
 **Sintaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna "abc" terminando com "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a>INDEX_OF  
 Retorna a posição inicial da primeira ocorrência da segunda expressão de cadeia de caracteres dentro da primeira expressão de cadeia de caracteres especificada, ou -1 se a cadeia de caracteres não for encontrada.  
  
 **Sintaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna o índice de diversas subcadeias de caracteres dentro de "abc".  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> ESQUERDA  
 Retorna a parte esquerda de uma cadeia de caracteres com o número especificado de caracteres.  
  
 **Sintaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna a parte esquerda de "abc" para vários valores de comprimento.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> COMPRIMENTO  
 Retorna o número de caracteres da expressão de cadeia de caracteres especificada.  
  
 **Sintaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna o comprimento de uma cadeia de caracteres.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos.  
  
 **Sintaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar LOWER em uma consulta.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Retorna uma expressão de cadeia de caracteres após remover os espaços em branco iniciais.  
  
 **Sintaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar LTRIM em uma consulta.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> SUBSTITUIR  
 Substitui todas as ocorrências de um valor de cadeia de caracteres especificado por outro valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar REPLACE em uma consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Repete um valor de cadeia de caracteres por um número de vezes especificado.  
  
 **Sintaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida. Se num_expr for negativo ou não finito, o resultado será indefinido.

  > [!NOTE]
  > O comprimento máximo do resultado é 10.000 caracteres, ou seja, (length(str_expr) * num_expr) <= 10,000.
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar REPLICATE em uma consulta.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Retorna a ordem inversa de um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar REVERSE em uma consulta.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Retorna a parte direita de uma cadeia de caracteres com o número especificado de caracteres.  
  
 **Sintaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna a parte direita de "abc" para vários valores de comprimento.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Retorna uma expressão de cadeia de caracteres após remover os espaços em branco finais.  
  
 **Sintaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar RTRIM em uma consulta.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda.  
  
 **Sintaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir verifica se a cadeia de caracteres "abc" começa com "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Retorna a expressão convertida para uma matriz. Se a expressão não pode ser convertida, retorna indefinida.  
  
 **Sintaxe**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida a ser avaliada como uma expressão de matriz JSON. Observe que os valores de cadeia de caracteres aninhada devem ser escritos com aspas duplas para ser válido. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)
  
  **Tipos de retorno**  
  
  Retorna uma expressão de matriz ou indefinido.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como StringToArray se comporta em diferentes tipos. 
  
 A seguir estão exemplos com uma entrada válida.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Este é o conjunto de resultados.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

O exemplo a seguir é um exemplo de uma entrada inválida. 
   
 Aspas simples dentro da matriz não são um JSON válido.
Mesmo que eles sejam válidos dentro de uma consulta, elas não analisará a matrizes válidos. Cadeias de caracteres na cadeia de caracteres de matriz ou devem ser escapadas "[\\"\\"]" ou as aspas ao redor devem ser única ' [""]'.

```
SELECT
    StringToArray("['5','6','7']")
```

Este é o conjunto de resultados.

```
[{}]
```

A seguir estão exemplos de entrada inválido.
   
 A expressão transmitida será analisada como uma matriz JSON; o exemplo a seguir não é avaliadas para o tipo de matriz e, portanto, retornar indefinido.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Este é o conjunto de resultados.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Retorna a expressão convertida como um booliano. Se a expressão não pode ser convertida, retorna indefinida.  
  
 **Sintaxe**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida a ser avaliada como uma expressão booleana.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booleana ou indefinido.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como StringToBoolean se comporta em diferentes tipos. 
 
 A seguir estão exemplos com uma entrada válida.

Espaço em branco é permitido apenas antes ou depois de "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

A seguir estão exemplos com uma entrada inválida.

 Boolianos diferenciam maiusculas de minúsculas e devem ser escritos com todos os caracteres minúsculos ou seja, "true" e "falsos".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Este é o conjunto de resultados.  
  
```  
[{}]
``` 

A expressão transmitida será analisada como uma expressão booliana; Essas entradas não são avaliadas para tipo booliano e, portanto, retornar indefinido.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Este é o conjunto de resultados.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Retorna a expressão convertida como nulo. Se a expressão não pode ser convertida, retorna indefinida.  
  
 **Sintaxe**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida a ser avaliada como uma expressão nula.
  
  **Tipos de retorno**  
  
  Retorna uma expressão nula ou indefinido.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como StringToNull se comporta em diferentes tipos. 

A seguir estão exemplos com uma entrada válida.

 Espaço em branco é permitido apenas antes ou depois de "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

A seguir estão exemplos com uma entrada inválida.

NULL diferencia maiusculas de minúsculas e deve ser escrito com todos os caracteres minúsculos ou seja, "nulos".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Este é o conjunto de resultados.  
  
```  
[{}]
```  

A expressão transmitida será analisada como uma expressão nula; Essas entradas não são avaliadas para tipo nulo e, portanto, retornar indefinido.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Retorna a expressão convertida para um número. Se a expressão não pode ser convertida, retorna indefinida.  
  
 **Sintaxe**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida a ser avaliada como uma expressão de número de JSON. Números em JSON devem ser um inteiro ou um ponto flutuante. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica ou indefinido.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como StringToNumber se comporta em diferentes tipos. 

Espaço em branco é permitido apenas antes ou depois do número.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Este é o conjunto de resultados.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Em JSON, um número válido deve ser tanto ser um inteiro ou flutuante número de ponto.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Este é o conjunto de resultados.  
  
```  
{{}}
```  

A expressão transmitida será analisada como uma expressão numérica; Essas entradas não são avaliadas para o tipo de número e, portanto, retornar indefinido. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Este é o conjunto de resultados.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Retorna a expressão convertida para um objeto. Se a expressão não pode ser convertida, retorna indefinida.  
  
 **Sintaxe**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida a ser avaliada como uma expressão de objeto JSON. Observe que os valores de cadeia de caracteres aninhada devem ser escritos com aspas duplas para ser válido. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de objeto ou indefinido.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como StringToObject se comporta em diferentes tipos. 
  
 A seguir estão exemplos com uma entrada válida.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Este é o conjunto de resultados.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 A seguir estão exemplos com uma entrada inválida.
Mesmo que eles sejam válidos dentro de uma consulta, não será analisado para objetos válidos. Cadeias de caracteres na cadeia de caracteres do objeto ou devem ser escapadas "{\\" uma\\":\\" str\\"}" ou as aspas ao redor devem ser única ' {"a": "str"}'.

Aspas simples ao redor de nomes de propriedade não são um JSON válido.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Este é o conjunto de resultados.

```  
[{}]
```  

Nomes de propriedade sem aspas ao redor não são um JSON válido.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Este é o conjunto de resultados.

```  
[{}]
``` 

A seguir estão exemplos com uma entrada inválida.

 A expressão transmitida será analisada como um objeto JSON; Essas entradas não são avaliadas para o tipo de objeto e, portanto, retornar indefinido.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Este é o conjunto de resultados.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 Retorna parte de uma expressão de cadeia de caracteres começando na posição baseada em zero do caractere especificado e continua até o comprimento especificado ou até o final da cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
- `num_expr`  
  
   É qualquer expressão numérica válida para denotar o caractere de início e término.    
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir retorna a subcadeia de caracteres de "abc" começando em 1 e com o comprimento de 1 caractere.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Retorna uma representação de cadeia de caracteres de uma expressão escalar. 
  
 **Sintaxe**  
  
```  
ToString(<expr>)
```  
  
 **Argumentos**  
  
- `expr`  
  
   É qualquer expressão escalar válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como ToString se comporta com tipos diferentes.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Dada a seguinte entrada:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 O exemplo a seguir mostra como ToString pode ser usado com outras funções de cadeia de caracteres como CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Este é o conjunto de resultados.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Dada a seguinte entrada.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
O exemplo a seguir mostra como ToString pode ser usado com outras funções de cadeia de caracteres como REPLACE.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Este é o conjunto de resultados.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Retorna uma expressão de cadeia de caracteres após remover os espaços em branco iniciais e finais.  
  
 **Sintaxe**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como usar TRIM em uma consulta.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos.  
  
 **Sintaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
- `str_expr`  
  
   É qualquer expressão de cadeia de caracteres válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo abaixo mostra como usar UPPER em uma consulta  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Funções de matriz

As seguintes funções escalares executam uma operação em um valor de entrada de matriz e numérico de retorno, o valor booliano ou uma matriz:
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a>ARRAY_CONCAT  
 Retorna uma matriz que é o resultado da concatenação de dois ou mais valores de matriz.  
  
 **Sintaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão válida de matriz.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão de matriz.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como concatenar duas matrizes.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a>ARRAY_CONTAINS  
Retorna um valor booliano que indica se a matriz contém o valor especificado. Você pode verificar uma correspondência parcial ou completa de um objeto usando uma expressão booliana no comando. 

**Sintaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão válida de matriz.  
  
- `expr`  
  
   É qualquer expressão válida.  

- `bool_expr`  
  
   É qualquer expressão booliana. Se ele for definido como ' true' e se o valor de pesquisa especificado for um objeto, o comando procurará uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se ele for definido como 'false', o comando procurará uma correspondência completa de todos os objetos dentro da matriz. O valor padrão, se não especificado, é false. 
  
  **Tipos de retorno**  
  
  Retorna um valor booliano.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como verificar a associação em uma matriz usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"b1": true, "b2": false}]  
```  

O exemplo a seguir mostra como verificar em busca de correspondência parcial de um JSON em uma matriz usando ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a>ARRAY_LENGTH  
 Retorna o número de elementos da expressão de matriz especificada.  
  
 **Sintaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão válida de matriz.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como obter o comprimento de uma matriz usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a>ARRAY_SLICE  
 Retorna parte de uma expressão de matriz.
  
 **Sintaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
- `arr_expr`  
  
   É qualquer expressão válida de matriz.  
  
- `num_expr`  
  
   Índice numérico com base em zero no qual começar a matriz. Valores negativos podem ser usados para especificar o índice inicial relativo ao último elemento da matriz, ou seja, -1 faz referência ao último elemento na matriz.  

- `num_expr`  

   Número máximo de elementos na matriz resultante.    

  **Tipos de retorno**  
  
  Retorna uma expressão de matriz.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como obter diferentes fatias de uma matriz usando ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Data e a função de tempo

As funções escalares a seguir permitem que você obtenha a data UTC atual e a hora em duas formas; um carimbo de data numérico cujo valor é a época do Unix em milissegundos, ou como uma cadeia de caracteres que segue o formato ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Retorna a data UTC atual e a hora como uma cadeia de caracteres ISO 8601.
  
 **Sintaxe**
  
```
GETCURRENTDATETIME ()
```
  
  **Tipos de retorno**
  
  Retorna o UTC Data e hora ISO 8601 cadeia de caracteres valor atual. 

  Isso é expresso no formato AAAA-MM-DDThh:mm:ss.sssZ onde:
  
  |||
  |-|-|
  |AAAA|ano de quatro dígitos|
  |MM|mês de dois dígitos (01 = janeiro, etc.)|
  |DD|dia de dois dígitos do mês (01 a 31)|
  |T|signifier para início de elementos de tempo|
  |hh|hora de dois dígitos (de 00 a 23)|
  |MM|minutos com dois dígitos (de 00 a 59)|
  |ss|segundos com dois dígitos (de 00 a 59)|
  |.sss|três dígitos do decimal frações de segundo|
  |Z|Designador de UTC (Coordinated Universal Time)||
  
  Para obter mais detalhes sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Comentários**

  GETCURRENTDATETIME é uma função não determinística. 
  
  O resultado retornado é o UTC (Coordinated Universal Time).

  **Exemplos**  
  
  O exemplo a seguir mostra como obter a hora de data UTC atual usando a função interna de GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Aqui está um exemplo de conjunto de resultados.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 Retorna o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970. 
  
 **Sintaxe**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Tipos de retorno**  
  
  Retorna um valor numérico, o número atual de milissegundos decorridos desde a época Unix ou seja, o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

  **Comentários**

  GETCURRENTTIMESTAMP é uma função não determinística.
  
  O resultado retornado é o UTC (Coordinated Universal Time).

  **Exemplos**  
  
  O exemplo a seguir mostra como obter o carimbo de hora atual usando a função interna de GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Aqui está um exemplo de conjunto de resultados.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Funções espaciais

O Cosmos DB dá suporte às funções internas do OGC (Open Geospatial Consortium) a seguir em consultas geoespaciais. As funções escalares a seguir executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou um valor booliano.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.  
  
 **Sintaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão numérica que contém a distância. Isso é expresso em metros no sistema de referência padrão.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como retornar todos os documentos de família que estejam em um raio de 30 km do local especificado usando a função interna ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Retorna uma expressão booliana que indica se o objeto GeoJSON (Ponto, Polígono ou LineString) especificado no primeiro argumento está dentro do GeoJSON (Ponto, Polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
 
- `spatial_expr`  
  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
  **Tipos de retorno**  
  
  Retorna um valor booliano.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como localizar todos os documentos da família em um polígono usando ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a>ST_INTERSECTS  
 Retorna uma expressão booliana que indica se o objeto GeoJSON (ponto, polígono ou LineString) especificado no primeiro argumento intercepta o GeoJSON (ponto, polígono ou LineString) no segundo argumento.  
  
 **Sintaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
 
- `spatial_expr`  
  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
  **Tipos de retorno**  
  
  Retorna um valor booliano.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como localizar todas as áreas que interseccionam com o polígono especificado.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Retorna um valor Booliano que indica se a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**
  
- `spatial_expr`  
  
   É qualquer expressão válida de LineString, polígono ou ponto GeoJSON.  
  
  **Tipos de retorno**  
  
  Retorna uma expressão booliana.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como verificar se um ponto é válido usando ST_VALID.  
  
  Por exemplo, esse ponto tem um valor de latitude que não está no intervalo de valores válido [-90, 90] e, portanto, a consulta retornará false.  
  
  No caso dos polígonos, a especificação GeoJSON exige que o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada. Os pontos em um polígono devem ser especificados no sentido anti-horário. Um polígono especificado no sentido horário representa o inverso da região dentro dele.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argumentos**  
  
- `spatial_expr`  
  
   É qualquer expressão válida de ponto ou polígono GeoJSON.  
  
  **Tipos de retorno**  
  
  Retorna um valor JSON que contém um valor Booliano caso a expressão de ponto ou polígono GeoJSON especificada é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
  **Exemplos**  
  
  O exemplo a seguir mostra como verificar a validade (com detalhes) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [UDFs](sql-query-udfs.md)
- [agregações](sql-query-aggregates.md)