---
title: 'API DocumentDB do BD Cosmos do Azure: sintaxe SQL | Microsoft Docs'
description: "Documentação de referência para a linguagem de consulta SQL da API DocumentDB do BD Cosmos do Azure."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/13/2017
ms.author: mimig
ms.openlocfilehash: 0a05f4ee86d2dcca40dc0b5ec2c503ba0b8bfe8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-documentdb-api-sql-syntax-reference"></a>API DocumentDB do BD Cosmos do Azure: referência de sintaxe SQL

A API DocumentDB do BD Cosmos do Azure dá suporte a documentos de consulta usando um SQL (Structured Query Language) familiar, como a gramática, em documentos JSON hierárquicos, sem a necessidade de esquema explícito ou criação de índices secundários. Este tópico fornece a documentação de referência para a linguagem de consulta SQL da API DocumentDB.

Para obter uma explicação da linguagem de consulta SQL da API DocumentDB, confira [Consultas SQL para a API DocumentDB do BD Cosmos do Azure](documentdb-sql-query.md).  
  
Você também está convidado a visitar o [Query Playground](http://www.documentdb.com/sql/demo), onde pode experimentar o BD Cosmos do Azure e executar consultas SQL em relação a nosso conjunto de dados.  
  
## <a name="select-query"></a>Consulta SELECT  
Recupera documentos JSON do banco de dados. Dá suporte a avaliação de expressão, projeções, filtragem e junções.  As convenções usadas para descrever as instruções SELECT são tabuladas na seção Convenções de sintaxe.  
  
**Sintaxe**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Comentários**  
  
 Consulte as seguintes seções para obter detalhes sobre cada cláusula:  
  
-   [Cláusula SELECT](#bk_select_query)  
  
-   [Cláusula FROM](#bk_from_clause)  
  
-   [Cláusula WHERE](#bk_where_clause)  
  
-   [Cláusula ORDER BY](#bk_orderby_clause)  
  
As cláusulas na instrução SELECT devem ser ordenadas conforme mostrado acima. Qualquer uma das cláusulas opcionais pode ser omitida. Mas quando são usadas, elas devem aparecer na ordem correta.  
  
**Ordem de processamento lógico da instrução SELECT**  
  
A ordem na qual as cláusulas são processadas é:  

1.  [Cláusula FROM](#bk_from_clause)  
2.  [Cláusula WHERE](#bk_where_clause)  
3.  [Cláusula ORDER BY](#bk_orderby_clause)  
4.  [Cláusula SELECT](#bk_select_query)  

Observe que isso é diferente da ordem em que aparecem na sintaxe. A ordenação é tal que todos os símbolos novos introduzidos por uma cláusula processada são visíveis e podem ser usados em cláusulas processadas posteriormente. Por exemplo, os aliases declarados em uma cláusula FROM podem ser acessados nas cláusulas SELECT e WHERE.  

**Comentários e caracteres de espaço em branco**  

Todos os caracteres de espaço em branco que não fazem parte de uma cadeia de caracteres entre aspas ou identificador entre aspas não fazem parte da gramática da linguagem e são ignorados durante a análise.  

A linguagem de consulta dá suporte a comentários de estilo T-SQL, como  

-   Instrução SQL`-- comment text [newline]`  

Embora os comentários e caracteres de espaço em branco não tenham nenhum significado na gramática, eles devem ser usados para separar os tokens. Por exemplo: `-1e5` é um token de número único, ao passo que `: – 1 e5` é um token de sinal de subtração seguido pelo número 1 e identificador e5.  

##  <a name="bk_select_query"></a> Cláusula SELECT  
As cláusulas na instrução SELECT devem ser ordenadas conforme mostrado acima. Qualquer uma das cláusulas opcionais pode ser omitida. Mas quando são usadas, elas devem aparecer na ordem correta.  

**Sintaxe**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentos**  
  
 `<select_specification>`  
  
 Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
 `'*'`  
  
Especifica que o valor deve ser recuperado sem fazer alterações. Especificamente, se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
 `<object_property_list>`  
  
Especifica a lista de propriedades a serem recuperadas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
`VALUE`  
  
Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isso, ao contrário de `<property_list>`, não encapsula o valor projetado em um objeto.  
  
`<scalar_expression>`  
  
Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
**Comentários**  
  
A sintaxe `SELECT *` só será válida se a cláusula FROM tiver declarado exatamente um alias. `SELECT *` fornece uma projeção de identidade, que pode ser útil se nenhuma projeção é necessária. SELECT * só é válida se a cláusula FROM é especificada e introduzida uma única fonte de entrada.  
  
Observe que `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e podem ser expressos, como alternativa, usando instruções SELECT simples, conforme mostrado abaixo.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     é equivalente a:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     é equivalente a:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Consulte também**  
  
[Expressões escalares](#bk_scalar_expressions)  
[Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Cláusula FROM  
Especifica a fonte ou as fontes unidas. A cláusula FROM é opcional. Se não for especificado, outras cláusulas ainda serão executadas como se a cláusula FROM fornecesse um único documento.  
  
**Sintaxe**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentos**  
  
`<from_source>`  
  
Especifica uma fonte de dados, com ou sem um alias. Se o alias não for especificado, ele será inferido de `<collection_expression>` usando as seguinte regras:  
  
-   Se a expressão for um collection_name, collection_name será usado como um alias.  
  
-   Se a expressão for `<collection_expression>`, property_name e, em seguida, property_name serão usados como alias. Se a expressão for um collection_name, collection_name será usado como um alias.  
  
AS `input_alias`  
  
Especifica que `input_alias` é um conjunto de valores retornados pela expressão de coleção subjacente.  
 
`input_alias` IN  
  
Especifica que o `input_alias` deve representar o conjunto de valores obtidos pela iteração em todos os elementos da matriz de cada matriz retornada pela expressão de coleção subjacente. Qualquer valor retornado pela expressão de coleção subjacente que não seja uma matriz é ignorado.  
  
`<collection_expression>`  
  
Especifica a expressão de coleção a ser usada para recuperar os documentos.  
  
`ROOT`  
  
Especifica que documento deve ser recuperado da coleção padrão conectada no momento.  
  
`collection_name`  
  
Especifica que documento deve ser recuperado da coleção fornecida. O nome da coleção deve corresponder ao nome da coleção conectada no momento.  
  
`input_alias`  
  
Especifica que documento deve ser recuperado de outra fonte definida pelo alias fornecido.  
  
`<collection_expression> '.' property_`  
  
Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de coleção especificada.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de coleção especificada.  
  
**Comentários**  
  
Todos os aliases fornecidos ou inferidos nos `<from_source>(`s) devem ser exclusivos. A sintaxe de `<collection_expression>.`property_name é a mesma de `<collection_expression>' ['"property_name"']'`. No entanto, a sintaxe desta última pode ser usada se um nome de propriedade contém um caractere não identificador.  
  
**Propriedades ausentes, elementos de matriz ausentes, tratamento de valores indefinidos**  
  
Se uma expressão de coleção acessar propriedades ou elementos da matriz e o valor não existir, esse valor será ignorado e não processado.  
  
**Escopo de contexto de expressão de coleção**  
  
Uma expressão de coleção pode ser coleção com escopo ou documento com escopo:  
  
-   Uma expressão é coleção com escopo se a fonte subjacente da expressão de coleção é ROOT ou `collection_name`. Essa expressão representa um conjunto de documentos recuperados diretamente da coleção e não depende de processamento de outras expressões de coleção.  
  
-   Uma expressão tem escopo de documento se a fonte subjacente da expressão de coleção é um `input_alias` introduzido anteriormente na consulta. Essa expressão representa um conjunto de documentos obtidos avaliando a expressão de coleção no escopo de cada documento pertencente ao conjunto associado à coleção de alias.  O conjunto resultante será uma união de conjuntos obtidos pela avaliação da expressão de coleção para cada um dos documentos no conjunto subjacente.  
  
**Junções**  
  
Na versão atual, o BD Cosmos do Azure dá suporte a junções internas. Recursos adicionais de junção serão disponibilizados em breve.

Junções internas resultam em um produto completo cruzando os conjuntos associados à junção. O resultado de uma junção de N maneiras é um conjunto de tuplas com N elementos, em que cada valor na tupla é associado ao alias do conjunto membro da junção e pode ser acessado pela referência desse alias em outras cláusulas.  
  
A avaliação da junção depende do escopo de contexto dos conjuntos participantes:  
  
-  Uma junção entre um conjunto de coleção A e o conjunto de coleção com escopo definido B resulta em um produto cruzado de todos os elementos nos conjuntos A e B.
  
-   Uma associação entre o conjunto A e o conjunto com escopo de documento B resulta em uma união de todos os conjuntos obtidos pela avaliação do conjunto com escopo de documento B para cada documento do conjunto A.  
  
 Na versão atual, há suporte para, no máximo, uma expressão com escopo de coleção pelo processador de consulta.  
  
**Exemplos de junções:**  
  
Vamos começar com a seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permite que cada fonte defina `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM retorna um conjunto de tuplas N (tupla com valores N). Cada tupla terá os valores produzidos pela iteração de todos os alias da coleção em seus respectivos conjuntos.  
  
*JUNÇÃO exemplo 1, com 2 fontes:*  
  
- Permite que `<from_source1>` tenha escopo de coleção e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*JUNÇÃO exemplo 2, com 3 fontes:*  
  
- Permite que `<from_source1>` tenha escopo de coleção e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando `input_alias1` e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo de documento, referenciando `input_alias2` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300} para`input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Falta de tuplas para outros valores de `input_alias1`, `input_alias2`, para os quais `<from_source3>` não retornou nenhum valor.  
  
*JUNÇÃO exemplo 3, com 3 fontes:*  
  
- Permite que <from_source1> tenha escopo de coleção e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source1>` tenha escopo de coleção e represente o conjunto {A, B, C}.  
  
- Permite que <from_source2> tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para`input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo `input_alias1` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300} para`input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) , (C, 5, 300)  
  
> [!NOTE]
> Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` porque ambos têm o escopo para a mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) tuplas com valor A, 0 tuplas com valor B (1 x 0) e 2 (2 x 1) tuplas com valor C.  
  
**Consulte também**  
  
 [Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Cláusula WHERE  
 Especifica o critério de pesquisa para os documentos retornados pela consulta.  
  
 **Sintaxe**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentos**  
  
-   `<filter_condition>`  
  
     Especifica a condição a ser atendida para que os documentos sejam retornados.  
  
-   `<scalar_expression>`  
  
     Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
 **Comentários**  
  
 Para que o documento seja retornado, uma expressão especificada como condição de filtro deve ser avaliada como verdadeira. Somente o valor booliano verdadeiro atenderá à condição. Nenhum outro valor, seja indefinido, nulo, falso, número, matriz ou objeto, atenderá à condição.  
  
##  <a name="bk_orderby_clause"></a> Cláusula ORDER BY  
 Especifica a ordem de classificação para resultados retornados pela consulta.  
  
 **Sintaxe**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentos**  
  
-   `<sort_specification>`  
  
     Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um alias de nome ou coluna.  
  
     Várias colunas de classificação podem ser especificadas. Os nomes de coluna devem ser exclusivos. A sequência das colunas de classificação na cláusula ORDER BY define a organização do conjunto de resultados classificado. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
     Os nomes de coluna referenciados na cláusula ORDER BY devem corresponder a uma coluna na lista de seleção ou a uma coluna definida em uma tabela especificada na cláusula FROM sem nenhuma ambiguidade.  
  
-   `<sort_expression>`  
  
     Especifica uma única propriedade ou expressão na qual classificar o conjunto de resultados da consulta.  
  
-   `<scalar_expression>`  
  
     Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
-   `ASC | DESC`  
  
     Especifica que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. ASC classifica do valor mais baixo para o valor mais alto. DESC classifica do valor mais alto para o valor mais baixo. ASC é a ordem de classificação padrão. Os valores nulos são tratados como os menores valores possíveis.  
  
 **Comentários**  
  
 Enquanto a gramática de consulta dá suporte a vários pedidos por propriedades, o tempo de execução de consulta do BD Cosmos do Azure dá suporte à classificação apenas em relação a uma única propriedade e somente em relação a nomes de propriedade, ou seja, não em relação a propriedades calculadas. A classificação também requer que a política de indexação inclua um índice de intervalo para a propriedade e o tipo especificado, com precisão máxima. Consulte a documentação de política de indexação para obter mais detalhes.  
  
##  <a name="bk_scalar_expressions"></a> Expressões escalares  
 Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados para se obter um único valor. Expressões simples podem ser constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. As expressões simples podem ser combinadas em expressões complexas usando operadores.  
  
 Para obter detalhes sobre os valores que a expressão escalar pode ter, consulte a seção [Constantes](#bk_constants).  
  
 **Sintaxe**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumentos**  
  
-   `<constant>`  
  
     Representa um valor constante. Consulte a seção [Constantes](#bk_constants) para obter detalhes.  
  
-   `input_alias`  
  
     Representa um valor definido pelo `input_alias` introduzido na cláusula `FROM`.  
    Esse valor é garantidamente diferente de **indefinido**; os valores **indefinidos** na entrada são ignorados.  
  
-   `<scalar_expression>.property_name`  
  
     Representa um valor da propriedade de um objeto. Se a propriedade não existe ou é referenciada em um valor que não é um objeto, a expressão é avaliada como valor **indefinido**.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Representa um valor da propriedade com nome `property_name` ou elemento de matriz com índice `array_index` de um objeto ou uma matriz. Se o índice de matriz/propriedade não existe ou é referenciado em um valor que não é um objeto ou uma matriz, a expressão é avaliada como valor indefinido.  
  
-   `unary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado a um único valor. Consulte a seção [Operadores](#bk_operators) para obter detalhes.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Representa um operador que é aplicado a dois valores. Consulte a seção [Operadores](#bk_operators) para obter detalhes.  
  
-   `<scalar_function_expression>`  
  
     Representa um valor definido por um resultado de uma chamada de função.  
  
-   `udf_scalar_function`  
  
     Nome da função escalar definida pelo usuário.  
  
-   `builtin_scalar_function`  
  
     Nome da função escalar interna.  
  
-   `<create_object_expression>`  
  
     Representa um valor obtido pela criação de um novo objeto com propriedades especificadas e seus valores.  
  
-   `<create_array_expression>`  
  
     Representa um valor obtido pela criação de uma nova matriz com valores especificados como elementos  
  
-   `parameter_name`  
  
     Representa um valor do nome de parâmetro especificado. Os nomes de parâmetro devem ter uma única @ como primeiro caractere.  
  
 **Comentários**  
  
 Ao chamar uma função escalar interna ou definida pelo usuário, todos os argumentos deverão ser definidos. Se um dos argumentos for indefinido, a função não será chamada e o resultado será indefinido.  
  
 Ao criar um objeto, as propriedades a que forem atribuídas um valor indefinido serão ignoradas e não serão incluídas no objeto criado.  
  
 Ao criar uma matriz, os valores de elemento a que forem atribuídos um valor **indefinido** serão ignorados e não serão incluídos no objeto criado. Isso fará com que o próximo elemento definido assuma o seu lugar, de forma que a matriz criada não tenha índices ignorados.  
  
##  <a name="bk_operators"></a> Operadores  
 Esta seção descreve os operadores com suporte. Cada operador pode ser atribuído a exatamente uma categoria.  
  
 Consulte a tabela **Categorias de operador** abaixo para obter detalhes sobre o tratamento de valores **indefinidos**, requisitos de tipo para valores de entrada e tratamento de valores com tipos não correspondentes.  
  
 **Categorias de operador:**  
  
|**Categoria**|**Detalhes**|  
|-|-|  
|**aritmético**|O operador espera que as entradas sejam números. A saída também é um número. Se qualquer uma das entradas for **indefinido** ou um tipo que não seja número, o resultado será **indefinido**.|  
|**bit a bit**|O operador espera que as entradas sejam números inteiros com sinal de 32 bits. A saída também é um número inteiro com sinal de 32 bits.<br /><br /> Os valores não inteiros serão arredondados. Os valores positivos serão arredondados para baixo; os valores negativos, arredondados para cima.<br /><br /> Qualquer valor fora do intervalo inteiro de 32 bits será convertido usando-se os últimos 32 bits de notação de complemento do dois.<br /><br /> Se qualquer uma das entradas for **indefinido** ou um tipo que não seja número, o resultado será **indefinido**.<br /><br /> **Observação:** o comportamento acima é compatível com o comportamento do operador bit a bit de JavaScript.|  
|**lógico**|O operador espera que as entradas sejam boolianos. A saída também é um valor booliano.<br />Se qualquer uma das entradas for **indefinido** ou um tipo que não seja booliano, o resultado será **indefinido**.|  
|**comparação**|O operador espera que as entradas tenham o mesmo tipo e não sejam indefinidas. A saída é um valor booliano.<br /><br /> Se qualquer uma das entradas for **indefinido** ou tiver tipos diferentes, o resultado será **indefinido**.<br /><br /> Consulte a tabela **Ordenação dos valores para comparação** para ver detalhes da ordem dos valores.|  
|**string**|O operador espera que as entradas sejam cadeias de caracteres. A saída também é uma cadeia de caracteres.<br />Se qualquer uma das entradas for **indefinido** ou um tipo que não seja cadeia de caracteres, o resultado será **indefinido**.|  
  
 **Operadores unários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**aritmético**|+<br /><br /> -|Retorna o valor do número.<br /><br /> Negação bit a bit. Retorna o valor do número negado.|  
|**bit a bit**|~|Complemento de um. Retorna um complemento de um valor numérico.|  
|**Lógico**|**NOT**|Negação. Retorna o valor booliano negado.|  
  
 **Operadores binários:**  
  
|**Nome**|**Operador**|**Detalhes**|  
|-|-|-|  
|**aritmético**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Adição.<br /><br /> Subtração.<br /><br /> Multiplicação.<br /><br /> Divisão.<br /><br /> Modulação.|  
|**bit a bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|OR bit a bit.<br /><br /> AND bit a bit.<br /><br /> XOR bit a bit.<br /><br /> Deslocamento para a esquerda.<br /><br /> Deslocamento para a direita.<br /><br /> Deslocamento à direita com preenchimento com zero.|  
|**lógico**|**AND**<br /><br /> **OR**|Conjunção lógica. Retorna **true** se os dois argumentos forem **true**; do contrário, retorna **false**.<br /><br /> Conjunção lógica. Retorna **true** se os dois argumentos forem **true**; do contrário, retorna **false**.|  
|**comparação**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Igual a. Retorna **true** se os argumentos forem iguais; do contrário, retorna **false**.<br /><br /> Não igual a. Retorna **true** se os argumentos não forem iguais; do contrário, retorna **false**.<br /><br /> Maior que. Retorna **true** se o primeiro argumento for maior do que o segundo; do contrário, retorna **false**.<br /><br /> Maior ou igual a. Retorna **true** se o primeiro argumento for maior ou igual ao segundo; do contrário, retorna **false**.<br /><br /> Menor que. Retorna **true** se o primeiro argumento for menor do que o segundo; do contrário, retorna **false**.<br /><br /> Menor ou igual a. Retorna **true** se o primeiro argumento for menor ou igual ao segundo; do contrário, retorna **false**.<br /><br /> Coalesce. Retorna o segundo argumento se o primeiro argumento tem valor **indefinido**.|  
|**Cadeia de caracteres**|**&#124;&#124;**|Concatenação. Retorna uma concatenação dos dois argumentos.|  
  
 **Operadores ternários:**  
  
|Operador ternário|?|Retorna o segundo argumento se o primeiro argumento é avaliado como **true**; do contrário, retorna o terceiro argumento.|  
|-|-|-|  
  
 **Ordenação dos valores para comparação**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Não é comparável.|  
|**Nulo**|Valor único: **nulo**|  
|**Número**|Número real natural.<br /><br /> O valor infinito negativo é menor do que qualquer outro valor numérico.<br /><br /> Um valor infinito positivo é maior do que qualquer outro valor numérico. O valor **NaN** não é comparável. A comparação com **NaN** resultará em um valor **indefinido**.|  
|**Cadeia de caracteres**|Ordem lexicográfica.|  
|**Matriz**|Nenhuma ordem, mas justa.|  
|**Objeto**|Nenhuma ordem, mas justa.|  
  
 **Comentários**  
  
 No BD Cosmos do Azure, os tipos de valores geralmente não são conhecidos até que sejam realmente recuperados do banco de dados. Para dar suporte à execução de consultas com eficiência, a maioria dos operadores tem requisitos restritos de tipo. Além disso, os operadores por si só não executam conversões implícitas.  
  
 Isso significa que uma consulta como: SELECT * FROM ROOT r WHERE r.Age = 21 retornará apenas documentos com propriedade Age igual ao número 21. Documentos com propriedade Age igual à cadeia de caracteres "21" ou à cadeia de caracteres "0021" não corresponderão, já que a expressão "21" = 21 é avaliada como indefinida. Isso permite um melhor uso dos índices, pois a pesquisa de um valor específico (por exemplo, número 21) é mais rápida que a pesquisa de um número indefinido de possíveis correspondências (ou seja, número 21 ou cadeias de caracteres "21", "021", "21,0"...). Isso é diferente de como o JavaScript avalia os operadores em relação a valores de tipos diferentes.  
  
 **Comparação e igualdade de objetos e matrizes**  
  
 A comparação de valores de objeto ou matriz usando os operadores de intervalo (>, >=, <, <=) resulta em indefinido, pois não tem ordem definida nos valores de objeto ou matriz. No entanto, o uso de operadores de igualdade/desigualdade (=,! =, <>) tem suporte e os valores são comparados estruturalmente.  
  
 As matrizes são iguais se as duas matrizes têm o mesmo número de elementos e os elementos em posições de correspondência também são iguais. Se a comparação de qualquer par de elementos resulta em indefinido, o resultado da comparação de matriz é indefinido.  
  
 Os objetos são iguais se os dois objetos têm as mesmas propriedades definidas e se os valores das propriedades correspondentes também são iguais. Se a comparação de qualquer par de valores de propriedade resulta em indefinido, o resultado da comparação de objetos é indefinido.  
  
##  <a name="bk_constants"></a> Constantes  
 Uma constante, também conhecida como valor literal ou escalar, é um símbolo que representa um valor de dados específico. O formato de uma constante depende do valor que representa o tipo de dados.  
  
 **Suporte para tipos de dados escalares:**  
  
|**Tipo**|**Ordem de valores**|  
|-|-|  
|**Indefinido**|Valor único: **indefinido**|  
|**Nulo**|Valor único: **nulo**|  
|**Booliano**|Valores: **falso**, **verdadeiro**.|  
|**Número**|Um número de ponto flutuante de precisão dupla, padrão IEEE 754.|  
|**Cadeia de caracteres**|Uma sequência de zero ou mais caracteres Unicode. As cadeias de caracteres devem ser colocadas entre aspas simples ou duplas.|  
|**Matriz**|Uma sequência de zero ou mais elementos. Cada elemento pode ser um valor de qualquer tipo de dados escalares, exceto Indefinido.|  
|**Objeto**|Um conjunto ordenado de zero ou mais pares de nome/valor. Nome é uma cadeia de caracteres Unicode; o valor pode ser de qualquer tipo de dados escalares, exceto **Indefinido**.|  
  
 **Sintaxe**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumentos**  
  
1.  `<undefined_constant>; undefined`  
  
     Representa o valor indefinido do tipo Indefinido.  
  
2.  `<null_constant>; null`  
  
     Representa o valor **null** do tipo **Nulo**.  
  
3.  `<boolean_constant>`  
  
     Representa a constante do tipo booliano.  
  
4.  `false`  
  
     Representa o valor **false** do tipo booliano.  
  
5.  `true`  
  
     Representa o valor **true** do tipo booliano.  
  
6.  `<number_constant>`  
  
     Representa uma constante.  
  
7.  `decimal_literal`  
  
     Os literais decimais são números representados usando notação decimal ou notação científica.  
  
8.  `hexadecimal_literal`  
  
     Os literais hexadecimais são números representados usando o prefixo '0x' seguido por um ou mais dígitos hexadecimais.  
  
9. `<string_constant>`  
  
     Representa uma constante do tipo Cadeia de caracteres.  
  
10. `string _literal`  
  
     Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. As literais de cadeia de caracteres são colocadas entre aspas simples (apóstrofe: ') ou aspas duplas (aspas: ").  
  
 As seguintes sequências de escape são permitidas:  
  
|**Sequência de escape**|**Descrição**|**Caractere Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U+0027|  
|\\"|aspas (")|U+0022|  
|\\\|barra invertida (\\)|U+005C|  
|\\/|barra (/)|U+002F|  
|\b|backspace|U+0008|  
|\f|avanço de página|U+000C|  
|\n|alimentação de linha|U+000A|  
|\r|retorno de carro|U+000D|  
|\t|tab|U+0009|  
|\uXXXX|Um caractere Unicode definido por 4 dígitos hexadecimais.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a>Diretrizes de desempenho de consulta  
 Para que uma consulta seja executada com eficiência em uma grande coleção, ela deve usar filtros que podem ser oferecidos por um ou mais índices.  
  
 Os seguintes filtros serão considerados para a pesquisa de índice:  
  
-   Use o operador de igualdade (=) com uma expressão de caminho de documento e uma constante.  
  
-   Use o operador de intervalo (<, \<=, >, >=) com uma expressão de caminho de documento e constantes numéricas.  
  
-   Expressão de caminho de documento significa qualquer expressão que identifica um caminho constante nos documentos da coleção de banco de dados referenciada.  
  
 **Expressão de caminho de documento**  
  
 Expressões de caminho de documento são expressões que um caminho de propriedade ou um indexador de matriz avalia em um documento proveniente dos documentos da coleção do banco de dados. Esse caminho pode ser usado para identificar o local dos valores referenciados em um filtro diretamente nos documentos da coleção do banco de dados.  
  
 Para uma expressão ser considerada uma expressão de caminho de documento, ela deve:  
  
1.  Fazer referência diretamente à raiz da coleção.  
  
2.  Fazer referência ao indexador de matriz da constante ou à propriedade de alguma expressão de caminho de documento  
  
3.  Fazer referência a um alias que represente alguma expressão de caminho de documento.  
  
     **Convenções de sintaxe**  
  
     A tabela a seguir descreve as convenções usadas para descrever a sintaxe na referência da linguagem de consulta da API DocumentDB.  
  
    |**Convenção**|**Usadas para**|  
    |-|-|    
    |LETRAS MAIÚSCULAS|Palavras-chave não diferenciam maiúsculas de minúsculas.|  
    |letras minúsculas|Palavras-chave diferenciam maiúsculas de minúsculas.|  
    |\<não terminal>|Não terminal, definido separadamente.|  
    |\<não terminal> ::=|Definição de sintaxe do não terminal.|  
    |other_terminal|Terminal (token), descritos em detalhes em palavras.|  
    |identificador|Identificador. Permite apenas os seguintes caracteres: a-z A-Z 0-9 _O primeiro caractere não pode ser um dígito.|  
    |“cadeia de caracteres”|Cadeia de caracteres entre aspas. Permite qualquer cadeia de caracteres válida. Consulte a descrição de um string_literal.|  
    |'símbolo'|Símbolo de literal que faz parte da sintaxe.|  
    |&#124; (barra vertical)|Alternativas para itens de sintaxe. Você pode usar apenas um dos itens especificados.|  
    |[ ] /(colchetes)|Os colchetes colocam um ou mais itens opcionais.|  
    |[ ,...n ]|Indica que o item precedente pode ser repetido n vezes. As ocorrências são separadas por vírgulas.|  
    |[ ...n ]|Indica que o item precedente pode ser repetido n vezes. As ocorrências são separadas por espaços em branco.|  
  
##  <a name="bk_built_in_functions"></a> Funções internas  
 O BD Cosmos do Azure fornece muitas funções SQL internas. As categorias de funções internas estão listadas abaixo.  
  
|Função|Descrição|  
|--------------|-----------------|  
|[Funções matemáticas](#bk_mathematical_functions)|As funções matemáticas executam um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos, e retornam um valor numérico.|  
|[Funções de verificação de tipo](#bk_type_checking_functions)|As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL.|  
|[Funções de cadeia de caracteres](#bk_string_functions)|As funções de cadeia de caracteres executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano.|  
|[Funções de matriz](#bk_array_functions)|As funções de matriz executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz.|  
|[Funções espaciais](#bk_spatial_functions)|As funções espaciais a seguir executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou um valor booliano.|  
  
###  <a name="bk_mathematical_functions"></a>Funções matemáticas  
 As funções a seguir executam um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos, e retornam um valor numérico.  
  
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
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra os resultados do uso da função ABS em três números diferentes.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno).  
  
 **Sintaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna ACOS de -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado de arco seno.  
  
 **Sintaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna ASIN de -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada. Isso também é chamado de arco tangente.  
  
 **Sintaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna o ATAN do valor especificado.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Retorna o valor principal do arco tangente de y/x, expresso em radianos.  
  
 **Sintaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir calcula o ATN2 dos componentes x e y especificados.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Retorna o menor valor de número inteiro maior ou igual à expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra valores numéricos positivos, negativos e zero com a função CEILING.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Retorna o cosseno trigonométrico do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir calcula o COS do ângulo especificado.  
  
```  
SELECT COS(14.78)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir calcula o COT do ângulo especificado.  
  
```  
SELECT COT(124.1332)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos.  
  
 **Sintaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna o número de graus em um ângulo de radiano PI/2.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Retorna o maior inteiro menor ou igual à expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra valores numéricos positivos, negativos e zero com a função FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Retorna o valor exponencial da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
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
SELECT EXP(10)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 O exemplo a seguir retorna o valor exponencial do logaritmo natural de 20 e o logaritmo natural do exponencial de 20. Como essas funções são funções inversas uma da outra, o valor retornado com o arredondamento para a matemática do ponto flutuante em ambos os casos é 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Retorna o logaritmo natural de expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `base`  
  
     Argumento numérico opcional que define a base para o logaritmo.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Comentários**  
  
 Por padrão, LOG() retorna o logaritmo natural. Você pode alterar a base do logaritmo para outro valor usando o parâmetro opcional de base.  
  
 O logaritmo natural é o logaritmo de base **e**, em que **e** é uma constante irracional aproximadamente igual a 2,718281828.  
  
 O logaritmo natural do exponencial de um número é o número em si: LOG(EXP(n)) = n. E o exponencial do logaritmo natural de um número é o número em si: EXP(LOG(n)) = n.  
  
 **Exemplos**  
  
 O exemplo a seguir declara uma variável e retorna o valor logarítmico da variável especificada (10).  
  
```  
SELECT LOG(10)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 O exemplo a seguir calcula o LOG para o expoente de um número.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Retorna o logaritmo de base 10 da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Comentários**  
  
 As funções LOG10 e POWER estão inversamente relacionadas entre si. Por exemplo, 10 ^ LOG10(n) = n.  
  
 **Exemplos**  
  
 O exemplo a seguir declara uma variável e retorna o valor LOG10 da variável especificada (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Retorna o valor constante de PI.  
  
 **Sintaxe**  
  
```  
PI ()  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna o valor de PI.  
  
```  
SELECT PI()  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Retorna o valor da expressão especificada para a potência indicada.  
  
 **Sintaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
-   `y`  
  
     É a potência à qual elevar `numeric_expression`.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir demonstra como elevar um número ao cubo.  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Retorna radianos quando uma expressão numérica é inserida em graus.  
  
 **Sintaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir usa alguns ângulos como entrada e retorna seus valores radianos correspondentes.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Retorna um valor numérico, arredondado para o valor inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir arredonda os números positivos e negativos para o inteiro mais próximo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Retorna o sinal positivo (+1), zero (0) ou negativo (-1) da expressão numérica especificada.  
  
 **Sintaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna os valores SIGN de números de -2 a 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Retorna o seno trigonométrico do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir calcula o SIN do ângulo especificado.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Retorna a raiz quadrada do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna as raízes quadradas dos números de 1 a 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Retorna o quadrado do valor numérico especificado.  
  
 **Sintaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna os quadrados dos números de 1 a 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Retorna a tangente do ângulo especificado, em radianos, na expressão especificada.  
  
 **Sintaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir calcula a tangente de PI()/2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a>TRUNC  
 Retorna um valor numérico, truncado para o valor inteiro mais próximo.  
  
 **Sintaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentos**  
  
-   `numeric_expression`  
  
     É uma expressão numérica.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir trunca os números positivos e negativos para o valor inteiro mais próximo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Funções de verificação de tipo  
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
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a>IS_BOOL  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um booliano.  
  
 **Sintaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a>IS_DEFINED  
 Retorna um valor booliano que indica se um valor foi atribuído à propriedade.  
  
 **Sintaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica a presença de uma propriedade no documento JSON especificado. O primeiro retorna true, já que "a" está presente, mas o segundo retorna false porque "b" está ausente.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a>IS_NULL  
 Retorna um valor booliano que indica se o tipo da expressão especificada é nulo.  
  
 **Sintaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a>IS_NUMBER  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um número.  
  
 **Sintaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a>IS_OBJECT  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um objeto JSON.  
  
 **Sintaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a>IS_PRIMITIVE  
 Retorna um valor booliano que indica se o tipo da expressão especificada é um primitivo (cadeia de caracteres, booliano, numérico ou nulo).  
  
 **Sintaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Retorna um valor booliano que indica se o tipo da expressão especificada é uma cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentos**  
  
-   `expression`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica objetos JSON booliano, número, cadeia de caracteres, nulo, objeto, matriz e tipos indefinidos usando a função IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Funções de cadeia de caracteres  
 As funções escalares a seguir executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[COMPRIMENTO](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna a cadeia de caracteres concatenada do valor especificado.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres contém a segunda.  
  
 **Sintaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica se "abc" contém "ab" e contém "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda.  
  
 **Sintaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna "abc" terminando com "b" e "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a>INDEX_OF  
 Retorna a posição inicial da primeira ocorrência da segunda expressão de cadeia de caracteres dentro da primeira expressão de cadeia de caracteres especificada, ou -1 se a cadeia de caracteres não for encontrada.  
  
 **Sintaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna o índice de diversas subcadeias de caracteres dentro de "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> ESQUERDA  
 Retorna a parte esquerda de uma cadeia de caracteres com o número especificado de caracteres.  
  
 **Sintaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna a parte esquerda de "abc" para vários valores de comprimento.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> COMPRIMENTO  
 Retorna o número de caracteres da expressão de cadeia de caracteres especificada.  
  
 **Sintaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna o comprimento de uma cadeia de caracteres.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos.  
  
 **Sintaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar LOWER em uma consulta.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Retorna uma expressão de cadeia de caracteres após remover os espaços em branco iniciais.  
  
 **Sintaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar LTRIM em uma consulta.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> SUBSTITUIR  
 Substitui todas as ocorrências de um valor de cadeia de caracteres especificado por outro valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar REPLACE em uma consulta.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Repete um valor de cadeia de caracteres por um número de vezes especificado.  
  
 **Sintaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar REPLICATE em uma consulta.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Retorna a ordem inversa de um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar REVERSE em uma consulta.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Retorna a parte direita de uma cadeia de caracteres com o número especificado de caracteres.  
  
 **Sintaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna a parte direita de "abc" para vários valores de comprimento.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Retorna uma expressão de cadeia de caracteres após remover os espaços em branco finais.  
  
 **Sintaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como usar RTRIM em uma consulta.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda.  
  
 **Sintaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir verifica se a cadeia de caracteres "abc" começa com "b" e "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 Retorna parte de uma expressão de cadeia de caracteres começando na posição baseada em zero do caractere especificado e continua até o comprimento especificado ou até o final da cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir retorna a subcadeia de caracteres de "abc" começando em 1 e com o comprimento de 1 caractere.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos.  
  
 **Sintaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentos**  
  
-   `str_expr`  
  
     É qualquer expressão de cadeia de caracteres válida.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo abaixo mostra como usar UPPER em uma consulta  
  
```  
SELECT UPPER("Abc")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funções de matriz  
 As funções escalares a seguir executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz  
  
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
  
-   `arr_expr`  
  
     É qualquer expressão válida de matriz.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão de matriz.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como concatenar duas matrizes.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a>ARRAY_CONTAINS  
 Retorna um valor booliano que indica se a matriz contém o valor especificado.  
  
 **Sintaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr>)  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão válida de matriz.  
  
-   `expr`  
  
     É qualquer expressão válida.  
  
 **Tipos de retorno**  
  
 Retorna um valor booliano.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como verificar a associação em uma matriz usando ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_array_length"></a>ARRAY_LENGTH  
 Retorna o número de elementos da expressão de matriz especificada.  
  
 **Sintaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão válida de matriz.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão numérica.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como obter o comprimento de uma matriz usando ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a>ARRAY_SLICE  
 Retorna parte de uma expressão de matriz.
  
 **Sintaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentos**  
  
-   `arr_expr`  
  
     É qualquer expressão válida de matriz.  
  
-   `num_expr`  
  
     É qualquer expressão numérica válida.  
  
 **Tipos de retorno**  
  
 Retorna um valor booliano.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como obter uma parte de uma matriz usando ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Funções espaciais  
 As funções escalares a seguir executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou um valor booliano.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.  
  
 **Sintaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
 
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
 
-   `spatial_expr`  
  
     É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
 **Tipos de retorno**  
  
 Retorna um valor booliano.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como localizar todas as áreas que fazem interseção com o polígono determinado.  
  
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
  
-   `spatial_expr`  
  
     É qualquer expressão válida de LineString, polígono ou ponto GeoJSON.  
  
 **Tipos de retorno**  
  
 Retorna uma expressão booliana.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como verificar se um ponto é válido usando ST_VALID.  
  
 Por exemplo, esse ponto tem um valor de latitude que não está no intervalo de valores válido [-90, 90] e, portanto, a consulta retornará false.  
  
 No caso dos polígonos, a especificação GeoJSON exige que o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada. Os pontos em um polígono devem ser especificados no sentido anti-horário. Um polígono especificado no sentido horário representa o inverso da região dentro dele.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
 **Sintaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentos**  
  
-   `spatial_expr`  
  
     É qualquer expressão válida de ponto ou polígono GeoJSON.  
  
 **Tipos de retorno**  
  
 Retorna um valor JSON que contém um valor Booliano caso a expressão de ponto ou polígono GeoJSON especificada é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
 **Exemplos**  
  
 O exemplo a seguir mostra como verificar a validade (com detalhes) usando ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Próximas etapas  
 [Sintaxe SQL e consulta SQL para BD Cosmos do Azure](documentdb-sql-query.md)   
 [Documentação do BD Cosmos do Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/)  
  
  
