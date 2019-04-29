---
title: Sintaxe da linguagem SQL no Azure Cosmos DB
description: Este artigo explica a sintaxe da linguagem de consulta SQL usada no Azure Cosmos DB, diferentes operadores e palavras-chave disponíveis nessa linguagem.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 22b03417495625ef70650a015530d6f56b32fd4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626868"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Referência de linguagem SQL para o Azure Cosmos DB 

Azure Cosmos DB suporta documentos de consulta usando um SQL (Structured Query Language) familiar, como a gramática, em documentos JSON hierárquicos, sem a necessidade de esquema explícito ou criação de índices secundários. Este artigo fornece documentação para obter a sintaxe de linguagem de consulta SQL usada nas contas de API do SQL. Para obter uma explicação das consultas SQL de exemplo, consulte [exemplos de consulta SQL no Cosmos DB](how-to-sql-query.md).  
  
Visite o [espaço de consulta](https://www.documentdb.com/sql/demo), onde você pode experimentar o Cosmos DB e executar consultas SQL em um conjunto de dados de exemplo.  
  
## <a name="select-query"></a>Consulta SELECT  
Toda consulta consiste em uma cláusula SELECT e cláusulas FROM e WHERE opcionais de acordo com os padrões ANSI-SQL. Normalmente, para cada consulta, a origem na cláusula FROM é enumerada e o filtro na cláusula WHERE é aplicado na origem para recuperar um subconjunto de documentos JSON. Por fim, a cláusula SELECT é usada para projetar os valores JSON solicitados na lista selecionada. Para obter exemplos, consulte [exemplos de consulta SELECT](how-to-sql-query.md#SelectClause)
  
**Sintaxe**  
  
```sql
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
  
### <a name="logical-processing-order-of-the-select-statement"></a>Ordem de processamento lógico da instrução SELECT  
  
A ordem na qual as cláusulas são processadas é:  

1.  [Cláusula FROM](#bk_from_clause)  
2.  [Cláusula WHERE](#bk_where_clause)  
3.  [Cláusula ORDER BY](#bk_orderby_clause)  
4.  [Cláusula SELECT](#bk_select_query)  

Observe que isso é diferente da ordem em que aparecem na sintaxe. A ordenação é tal que todos os símbolos novos introduzidos por uma cláusula processada são visíveis e podem ser usados em cláusulas processadas posteriormente. Por exemplo, os aliases declarados em uma cláusula FROM podem ser acessados nas cláusulas SELECT e WHERE.  

### <a name="whitespace-characters-and-comments"></a>Comentários e caracteres de espaço em branco  

Os caracteres em branco que não fazem parte de uma cadeia de caracteres entre aspas ou identificador entre aspas não fazem parte da gramática do idioma e serão ignorados durante a análise.  

A linguagem de consulta dá suporte a comentários de estilo T-SQL, como  

-   Instrução SQL`-- comment text [newline]`  

Embora os comentários e caracteres de espaço em branco não tenham nenhum significado na gramática, eles devem ser usados para separar os tokens. Por exemplo: `-1e5` é um token de número único, ao passo que `: – 1 e5` é um token de sinal de subtração seguido pelo número 1 e identificador e5.  

##  <a name="bk_select_query"></a> Cláusula SELECT  
As cláusulas na instrução SELECT devem ser ordenadas conforme mostrado acima. Qualquer uma das cláusulas opcionais pode ser omitida. Mas quando são usadas, elas devem aparecer na ordem correta. Para obter exemplos, consulte [exemplos de consulta SELECT](how-to-sql-query.md#SelectClause).

**Sintaxe**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentos**  
  
- `<select_specification>`  

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem fazer alterações. Especificamente, se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem recuperadas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isso, ao contrário de `<property_list>`, não encapsula o valor projetado em um objeto.  
  
- `<scalar_expression>`  

  Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
**Comentários**  
  
A sintaxe `SELECT *` só será válida se a cláusula FROM tiver declarado exatamente um alias. `SELECT *` fornece uma projeção de identidade, que pode ser útil se nenhuma projeção é necessária. SELECT * só é válida se a cláusula FROM é especificada e introduzida uma única fonte de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e podem ser expressos, como alternativa, usando instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Consulte também**  
  
[Expressões escalares](#bk_scalar_expressions)  
[Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Cláusula FROM  
Especifica a fonte ou as fontes unidas. A cláusula FROM é opcional, a menos que a fonte seja filtrada ou projetada mais adiante na consulta. O objetivo desta cláusula é especificar a fonte de dados na qual a consulta deve operar. Geralmente, o contêiner inteiro é a origem, mas é possível especificar um subconjunto do contêiner. Se esta cláusula não for especificada, outras cláusulas ainda serão executadas como se a cláusula FROM fornecesse um único documento. Para obter exemplos, consulte [exemplos da cláusula FROM](how-to-sql-query.md#FromClause)
  
**Sintaxe**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentos**  
  
- `<from_source>`  
  
  Especifica uma fonte de dados, com ou sem um alias. Se o alias não for especificado, ele será inferido de `<container_expression>` usando as seguinte regras:  
  
  -  Se a expressão for um container_name, o container_name será usado como um alias.  
  
  -  Se a expressão for `<container_expression>`, property_name e, em seguida, property_name serão usados como alias. Se a expressão for um container_name, o container_name será usado como um alias.  
  
- AS `input_alias`  
  
  Especifica que `input_alias` é um conjunto de valores retornados pela expressão do contêiner subjacente.  
 
- `input_alias` IN  
  
  Especifica que `input_alias` deve representar o conjunto de valores obtidos pela iteração em todos os elementos de matriz de cada matriz retornada pela expressão de contêiner subjacente. Qualquer valor retornado pela expressão de contêiner subjacente que não seja uma matriz será ignorado.  
  
- `<container_expression>`  
  
  Especifica a expressão do contêiner a ser usada para recuperar os documentos.  
  
- `ROOT`  
  
  Especifica que o documento deve ser recuperado do padrão, contêiner atualmente conectado.  
  
- `container_name`  
  
  Especifica que o documento deve ser recuperado do contêiner fornecido. O nome do contêiner deve corresponder ao nome do contêiner atualmente conectado.  
  
- `input_alias`  
  
  Especifica que documento deve ser recuperado de outra fonte definida pelo alias fornecido.  
  
- `<container_expression> '.' property_`  
  
  Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
**Comentários**  
  
Todos os aliases fornecidos ou inferidos nos `<from_source>(`s) devem ser exclusivos. A sintaxe de `<container_expression>.`property_name é a mesma de `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe desta última pode ser usada se um nome de propriedade contém um caractere não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manipulação de propriedades ausentes, elementos de matriz ausentes e valores indefinidos
  
Se uma expressão de contêiner acessar propriedades ou elementos da matriz e o valor não existir, esse valor será ignorado e não processado.  
  
### <a name="container-expression-context-scoping"></a>Escopo do contexto da expressão do contêiner  
  
Uma expressão contêiner pode ter escopo no contêiner ou no escopo do documento:  
  
-   Uma expressão será contida no contêiner, se a origem subjacente da expressão do contêiner for ROOT ou `container_name`. Essa expressão representa um conjunto de documentos recuperados diretamente do contêiner e não depende do processamento de outras expressões de contêiner.  
  
-   Uma expressão terá escopo de documento, se a fonte subjacente da expressão de contêiner for `input_alias` introduzido anteriormente na consulta. Essa expressão representa um conjunto de documentos obtidos pela avaliação da expressão contêiner no escopo de cada documento pertencente ao conjunto associado ao contêiner de alias.  O conjunto resultante será uma união de conjuntos obtidos pela avaliação da expressão do contêiner para cada um dos documentos no conjunto subjacente.  
  
### <a name="joins"></a>Junções 
  
Na versão atual, o Cosmos DB dá suporte a junções internas. Recursos adicionais de junção serão disponibilizados em breve. 

Junções internas resultam em um produto completo cruzando os conjuntos associados à junção. O resultado de uma junção de N maneiras é um conjunto de tuplas com N elementos, em que cada valor na tupla é associado ao alias do conjunto membro da junção e pode ser acessado pela referência desse alias em outras cláusulas. Para obter exemplos, consulte [exemplos de palavra-chave JOIN](how-to-sql-query.md#Joins)
  
A avaliação da junção depende do escopo de contexto dos conjuntos participantes:  
  
- Uma junção entre o conjunto de contêineres A e o conjunto de escopo no contêiner B, resulta em um produto cruzado de todos os elementos nos conjuntos A e B.
  
- Uma associação entre o conjunto A e o conjunto com escopo de documento B resulta em uma união de todos os conjuntos obtidos pela avaliação do conjunto com escopo de documento B para cada documento do conjunto A.  
  
  Na versão atual, há suporte para, no máximo, uma expressão com escopo de contêiner pelo processador de consulta.  
  
### <a name="examples-of-joins"></a>Exemplos de junções  
  
Vamos começar com a seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permite que cada fonte defina `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM retorna um conjunto de tuplas N (tupla com valores N). Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos.  
  
**Exemplo 1** - 2 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** - 3 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando `input_alias1` e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo de documento, referenciando `input_alias2` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Falta de tuplas para outros valores de `input_alias1`, `input_alias2`, para os quais `<from_source3>` não retornou nenhum valor.  
  
**Exemplo 3** - 3 fontes  
  
- Permite que <from_source1> tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que <from_source2> tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo `input_alias1` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` porque ambos têm o escopo para a mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) tuplas com valor A, 0 tuplas com valor B (1 x 0) e 2 (2 x 1) tuplas com valor C.  
  
**Consulte também**  
  
 [Cláusula SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Cláusula WHERE  
 Especifica o critério de pesquisa para os documentos retornados pela consulta. Para obter exemplos, consulte [exemplos da cláusula WHERE](how-to-sql-query.md#WhereClause)
  
 **Sintaxe**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentos**  
  
- `<filter_condition>`  
  
   Especifica a condição a ser atendida para que os documentos sejam retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
  **Comentários**  
  
  Para que o documento seja retornado, uma expressão especificada como condição de filtro deve ser avaliada como verdadeira. Somente o valor booliano verdadeiro satisfará a condição, qualquer outro valor: indefinido, nulo, falso, Número, Matriz ou Objeto não satisfará a condição.  
  
##  <a name="bk_orderby_clause"></a> Cláusula ORDER BY  
 Especifica a ordem de classificação para resultados retornados pela consulta. Para obter exemplos, consulte [exemplos da cláusula ORDER BY](how-to-sql-query.md#OrderByClause)
  
 **Sintaxe**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentos**  
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um alias de nome ou coluna.  
  
   Várias colunas de classificação podem ser especificadas. Os nomes de coluna devem ser exclusivos. A sequência das colunas de classificação na cláusula ORDER BY define a organização do conjunto de resultados classificado. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
   Os nomes de coluna referenciados na cláusula ORDER BY devem corresponder a uma coluna na lista de seleção ou a uma coluna definida em uma tabela especificada na cláusula FROM sem nenhuma ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica uma única propriedade ou expressão na qual classificar o conjunto de resultados da consulta.  
  
- `<scalar_expression>`  
  
   Consulte a seção [Expressões escalares](#bk_scalar_expressions) para obter detalhes.  
  
- `ASC | DESC`  
  
   Especifica que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. ASC classifica do valor mais baixo para o valor mais alto. DESC classifica do valor mais alto para o valor mais baixo. ASC é a ordem de classificação padrão. Os valores nulos são tratados como os menores valores possíveis.  
  
  **Comentários**  
  
  Enquanto a gramática de consulta der suporte a vários pedidos por propriedades, o tempo de execução de consulta do Cosmos DB dará suporte à classificação apenas em relação a uma única propriedade e somente em relação a nomes de propriedades (não em relação a propriedades calculadas). A classificação também requer que a política de indexação inclua um índice de intervalo para a propriedade e o tipo especificado, com precisão máxima. Consulte a documentação de política de indexação para obter mais detalhes.  
  
##  <a name="bk_scalar_expressions"></a> Expressões escalares  
 Uma expressão escalar é uma combinação de símbolos e operadores que podem ser avaliados para se obter um único valor. Expressões simples podem ser constantes, referências de propriedade, referências de elemento de matriz, referências de alias ou chamadas de função. As expressões simples podem ser combinadas em expressões complexas usando operadores. Para obter exemplos, consulte [exemplos de expressões escalares](how-to-sql-query.md#scalar-expressions)
  
 Para obter detalhes sobre os valores que a expressão escalar pode ter, consulte a seção [Constantes](#bk_constants).  
  
 **Sintaxe**  
  
```sql  
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
  
- `<constant>`  
  
   Representa um valor constante. Consulte a seção [Constantes](#bk_constants) para obter detalhes.  
  
- `input_alias`  
  
   Representa um valor definido pelo `input_alias` introduzido na cláusula `FROM`.  
  Esse valor é garantidamente diferente de **indefinido**; os valores **indefinidos** na entrada são ignorados.  
  
- `<scalar_expression>.property_name`  
  
   Representa um valor da propriedade de um objeto. Se a propriedade não existe ou é referenciada em um valor que não é um objeto, a expressão é avaliada como valor **indefinido**.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representa um valor da propriedade com nome `property_name` ou elemento de matriz com índice `array_index` de um objeto ou uma matriz. Se o índice de propriedade/matriz não existir ou o índice de propriedade/matriz for referenciado em um valor que não seja um objeto/matriz, a expressão será avaliada como um valor indefinido.  
  
- `unary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a um único valor. Consulte a seção [Operadores](#bk_operators) para obter detalhes.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representa um operador que é aplicado a dois valores. Consulte a seção [Operadores](#bk_operators) para obter detalhes.  
  
- `<scalar_function_expression>`  
  
   Representa um valor definido por um resultado de uma chamada de função.  
  
- `udf_scalar_function`  
  
   Nome da função escalar definida pelo usuário.  
  
- `builtin_scalar_function`  
  
   Nome da função escalar interna.  
  
- `<create_object_expression>`  
  
   Representa um valor obtido pela criação de um novo objeto com propriedades especificadas e seus valores.  
  
- `<create_array_expression>`  
  
   Representa um valor obtido pela criação de uma nova matriz com valores especificados como elementos  
  
- `parameter_name`  
  
   Representa um valor do nome de parâmetro especificado. Os nomes de parâmetro devem ter uma única \@ como primeiro caractere.  
  
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
|**bit a bit**|O operador espera que as entradas sejam números inteiros com sinal de 32 bits. A saída também é um número inteiro com sinal de 32 bits.<br /><br /> Os valores não inteiros serão arredondados. Os valores positivos serão arredondados para baixo; os valores negativos, arredondados para cima.<br /><br /> Qualquer valor fora do intervalo inteiro de 32 bits será convertido usando-se os últimos 32 bits de notação de complemento do dois.<br /><br /> Se qualquer uma das entradas for **indefinido** ou um tipo que não seja número, o resultado será **indefinido**.<br /><br /> **Observação:** O comportamento acima é compatível com o comportamento do operador bit a bit do JavaScript.|  
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
|**lógico**|**AND**<br /><br /> **OR**|Conjunção lógica. Retorna **true** se os dois argumentos forem **true**; do contrário, retorna **false**.<br /><br /> Disjunção lógica. Retorna **true** se algum argumento for **true**; do contrário, retorna **false**.|  
|**comparação**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Igual a. Retorna **true** se os argumentos forem iguais; do contrário, retorna **false**.<br /><br /> Não igual a. Retorna **true** se os argumentos não forem iguais; do contrário, retorna **false**.<br /><br /> Maior que. Retorna **true** se o primeiro argumento for maior do que o segundo; do contrário, retorna **false**.<br /><br /> Maior ou igual a. Retorna **true** se o primeiro argumento for maior ou igual ao segundo; do contrário, retorna **false**.<br /><br /> Menor que. Retorna **true** se o primeiro argumento for menor do que o segundo; do contrário, retorna **false**.<br /><br /> Menor ou igual a. Retorna **true** se o primeiro argumento for menor ou igual ao segundo; do contrário, retorna **false**.<br /><br /> Coalesce. Retorna o segundo argumento se o primeiro argumento tem valor **indefinido**.|  
|**Cadeia de caracteres**|**&#124;&#124;**|Concatenação. Retorna uma concatenação dos dois argumentos.|  
  
 **Operadores ternários:**  

|**Nome**|**Operador**|**Detalhes**| 
|-|-|-|  
|Operador ternário|?|Retorna o segundo argumento se o primeiro argumento é avaliado como **true**; do contrário, retorna o terceiro argumento.|  

  
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
  
 No Cosmos DB, os tipos de valores geralmente não são conhecidos até serem recuperados do banco de dados. Para dar suporte à execução de consultas com eficiência, a maioria dos operadores tem requisitos restritos de tipo. Além disso, os operadores por si só não executam conversões implícitas.  
  
 Isso significa que uma consulta semelhante a: SELECT * FROM ROOT r WHERE r.Age = 21 somente retornará documentos com a propriedade Idade igual ao número 21. Documentos com propriedade Age igual à cadeia de caracteres "21" ou à cadeia de caracteres "0021" não corresponderão, já que a expressão "21" = 21 é avaliada como indefinida. Isso permite um melhor uso dos índices porque a pesquisa de um valor específico (como o número 21) é mais rápida do que a pesquisa por um número indefinido de correspondências em potencial (o número 21 ou cadeia de caracteres "21", "021", "21.0" …). Isso é diferente de como o JavaScript avalia os operadores em relação a valores de tipos diferentes.  
  
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
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Representa o valor indefinido do tipo Indefinido.  
  
* `<null_constant>; null`  
  
  Representa o valor **null** do tipo **Nulo**.  
  
* `<boolean_constant>`  
  
  Representa a constante do tipo booliano.  
  
* `false`  
  
  Representa o valor **false** do tipo booliano.  
  
* `true`  
  
  Representa o valor **true** do tipo booliano.  
  
* `<number_constant>`  
  
  Representa uma constante.  
  
* `decimal_literal`  
  
  Os literais decimais são números representados usando notação decimal ou notação científica.  
  
* `hexadecimal_literal`  
  
  Os literais hexadecimais são números representados usando o prefixo '0x' seguido por um ou mais dígitos hexadecimais.  
  
* `<string_constant>`  
  
  Representa uma constante do tipo Cadeia de caracteres.  
  
* `string _literal`  
  
  Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. As literais de cadeia de caracteres são colocadas entre aspas simples (apóstrofe: ') ou aspas duplas (aspas: ").  
  
  As seguintes sequências de escape são permitidas:  
  
|**Sequência de escape**|**Descrição**|**Caractere Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U+0027|  
|\\"|aspas (")|U+0022|  
|\\\ |barra invertida (\\)|U+005C|  
|\\/|barra (/)|U+002F|  
|\b|backspace|U+0008|  
|\f|avanço de página|U+000C|  
|\n|alimentação de linha|U+000A|  
|\r|retorno de carro|U+000D|  
|\t|tab|U+0009|  
|\uXXXX|Um caractere Unicode definido por 4 dígitos hexadecimais.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a>Diretrizes de desempenho de consulta  
 Para que uma consulta seja executada de forma eficiente para um contêiner grande, ela deve usar filtros que podem ser exibidos por meio de um ou mais índices.  
  
 Os seguintes filtros serão considerados para a pesquisa de índice:  
  
- Use o operador de igualdade (=) com uma expressão de caminho de documento e uma constante.  
  
- Use o operador de intervalo (<, \<=, >, >=) com uma expressão de caminho de documento e constantes numéricas.  
  
- Expressão de caminho de documento significa qualquer expressão que identifique um caminho constante nos documentos do contêiner de banco de dados referenciado.  
  
  **Expressão de caminho de documento**  
  
  Expressões de caminho de documento são expressões que um caminho de propriedade ou um indexador de matriz avalia em um documento proveniente dos documentos do contêiner do banco de dados. Esse caminho pode ser usado para identificar o local dos valores referenciados em um filtro diretamente nos documentos do contêiner do banco de dados.  
  
  Para uma expressão ser considerada uma expressão de caminho de documento, ela deve:  
  
1.  Fazer referência diretamente à raiz do contêiner.  
  
2.  Fazer referência ao indexador de matriz da constante ou à propriedade de alguma expressão de caminho de documento  
  
3.  Fazer referência a um alias que represente alguma expressão de caminho de documento.  
  
     **Convenções de sintaxe**  
  
     A tabela a seguir descreve as convenções usadas para descrever a sintaxe na referência do SQL a seguir.  
  
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
 O Cosmos DB fornece muitas funções SQL internas. As categorias de funções internas estão listadas abaixo.  
  
|Função|DESCRIÇÃO|  
|--------------|-----------------|  
|[Funções matemáticas](#bk_mathematical_functions)|As funções matemáticas executam um cálculo, normalmente com base em valores de entrada que são fornecidos como argumentos, e retornam um valor numérico.|  
|[Funções de verificação de tipo](#bk_type_checking_functions)|As funções de verificação de tipo permitem que você verifique o tipo de uma expressão em consultas SQL.|  
|[Funções de cadeia de caracteres](#bk_string_functions)|As funções de cadeia de caracteres executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano.|  
|[Funções de matriz](#bk_array_functions)|As funções de matriz executam uma operação em um valor de matriz de entrada e retornam um valor numérico, booliano ou um valor de matriz.|  
|[Funções espaciais](#bk_spatial_functions)|As funções espaciais executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou booliano.|  
  
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
  
###  <a name="bk_string_functions"></a> Funções de cadeia de caracteres  
 As funções escalares a seguir executam uma operação em um valor de cadeia de caracteres de entrada e retornam uma cadeia de caracteres, um valor numérico ou um valor booliano.  
  
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
 
###  <a name="bk_spatial_functions"></a> Funções espaciais  
 As funções escalares a seguir executam uma operação em um valor de entrada de objeto espacial e retornam um valor numérico ou um valor booliano.  
  
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

- [Sintaxe SQL e consulta SQL para Cosmos DB](how-to-sql-query.md)

- [Documentação do Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
