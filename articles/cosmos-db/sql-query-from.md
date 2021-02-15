---
title: Cláusula FROM em Azure Cosmos DB
description: Saiba mais sobre a sintaxe SQL e o exemplo da cláusula FROM para Azure Cosmos DB. Este artigo também mostra exemplos de escopo de resultados e obter subitens usando a cláusula FROM.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 3844a628a8e3773821736324bfdaab3f64ff9da5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336240"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Cláusula FROM em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A cláusula FROM ( `FROM <from_specification>` ) é opcional, a menos que a fonte seja filtrada ou projetada posteriormente na consulta. Uma consulta como `SELECT * FROM Families` enumera em todo o `Families` contêiner. Você também pode usar a raiz do identificador especial para o contêiner em vez de usar o nome do contêiner.

A `FROM` cláusula impõe as seguintes regras por consulta:

* O contêiner pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` está o alias para `Families` . Como é uma palavra-chave opcional para [alias](sql-query-working-with-json.md#aliasing) do identificador.  

* Depois de alias, o nome de origem original não pode ser associado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` tem um alias e não pode mais ser resolvido.  

* Todas as propriedades referenciadas devem ser totalmente qualificadas para evitar associações ambíguas na ausência da adesão estrita ao esquema. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválido porque a propriedade `id` não está associada.

## <a name="syntax"></a>Sintaxe
  
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
  
## <a name="arguments"></a>Argumentos
  
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
  
- `<container_expression> '.' property_name`  
  
  Especifica que o documento deve ser recuperado acessando a `property_name` propriedade.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
## <a name="remarks"></a>Comentários
  
Todos os aliases fornecidos ou inferidos nos `<from_source>(`s) devem ser exclusivos. A sintaxe de `<container_expression>.`property_name é a mesma de `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe desta última pode ser usada se um nome de propriedade contém um caractere não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manipulação de propriedades ausentes, elementos de matriz ausentes e valores indefinidos
  
Se uma expressão de contêiner acessar propriedades ou elementos da matriz e o valor não existir, esse valor será ignorado e não processado.  
  
### <a name="container-expression-context-scoping"></a>Escopo do contexto da expressão do contêiner  
  
Uma expressão contêiner pode ter escopo no contêiner ou no escopo do documento:  
  
- Uma expressão será contida no contêiner, se a origem subjacente da expressão do contêiner for ROOT ou `container_name`. Essa expressão representa um conjunto de documentos recuperados diretamente do contêiner e não depende do processamento de outras expressões de contêiner.  
  
- Uma expressão terá escopo de documento, se a fonte subjacente da expressão de contêiner for `input_alias` introduzido anteriormente na consulta. Essa expressão representa um conjunto de documentos obtidos pela avaliação da expressão contêiner no escopo de cada documento pertencente ao conjunto associado ao contêiner de alias. O conjunto resultante será uma união de conjuntos obtidos pela avaliação da expressão do contêiner para cada um dos documentos no conjunto subjacente.

## <a name="examples"></a>Exemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obter subitens usando a cláusula FROM

A cláusula FROM pode reduzir a origem para um subconjunto menor. Para enumerar apenas uma subárvore em cada item, a subraiz pode se tornar a origem, conforme mostrado no exemplo a seguir:

```sql
    SELECT *
    FROM Families.children
```

Os resultados são:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

A consulta anterior usou uma matriz como a origem, mas você também pode usar um objeto como a origem. A consulta considera qualquer valor JSON válido e definido na fonte para inclusão no resultado. O exemplo a seguir excluiria `Families` que não tem um `address.state` valor.

```sql
    SELECT *
    FROM Families.address.state
```

Os resultados são:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula WHERE](sql-query-where.md)
