---
title: NA cláusula no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL do Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342444"
---
# <a name="from-clause"></a>Cláusula FROM

FROM (`FROM <from_specification>`) cláusula é opcional, a menos que a fonte seja filtrada ou projetada mais adiante na consulta. Uma consulta como `SELECT * FROM Families` enumera em todo o `Families` contêiner. Você também pode usar o identificador especial ROOT para o contêiner em vez de usar o nome do contêiner.

A cláusula FROM impõe as regras por consulta a seguir:

* O contêiner pode ser um alias, como `SELECT f.id FROM Families AS f` ou simplesmente `SELECT f.id FROM Families f`. Aqui `f` é o alias `Families`. COMO é uma palavra-chave opcional para [alias](sql-query-aliasing.md) o identificador.  

* Após receber um alias, o nome original do código-fonte não pode ser associado. Por exemplo, `SELECT Families.id FROM Families f` é sintaticamente inválido porque o identificador `Families` tem sido um alias e não pode mais ser resolvido.  

* Todas as propriedades de referência devem ser totalmente qualificadas evitar associações ambíguas na ausência de adesão de esquema estrita. Por exemplo, `SELECT id FROM Families f` é sintaticamente inválido porque a propriedade `id` não está associada.

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
  
- `<container_expression> '.' property_`  
  
  Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Especifica que documento deve ser recuperado acessando a propriedade `property_name` ou o elemento de matriz array_index para todos os documentos recuperados pela expressão de contêiner especificada.  
  
## <a name="remarks"></a>Comentários
  
Todos os aliases fornecidos ou inferidos nos `<from_source>(`s) devem ser exclusivos. A sintaxe de `<container_expression>.`property_name é a mesma de `<container_expression>' ['"property_name"']'`. No entanto, a sintaxe desta última pode ser usada se um nome de propriedade contém um caractere não identificador.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Manipulação de propriedades ausentes, elementos de matriz ausentes e valores indefinidos
  
Se uma expressão de contêiner acessar propriedades ou elementos da matriz e o valor não existir, esse valor será ignorado e não processado.  
  
### <a name="container-expression-context-scoping"></a>Escopo do contexto da expressão do contêiner  
  
Uma expressão contêiner pode ter escopo no contêiner ou no escopo do documento:  
  
-   Uma expressão será contida no contêiner, se a origem subjacente da expressão do contêiner for ROOT ou `container_name`. Essa expressão representa um conjunto de documentos recuperados diretamente do contêiner e não depende do processamento de outras expressões de contêiner.  
  
-   Uma expressão terá escopo de documento, se a fonte subjacente da expressão de contêiner for `input_alias` introduzido anteriormente na consulta. Essa expressão representa um conjunto de documentos obtidos pela avaliação da expressão contêiner no escopo de cada documento pertencente ao conjunto associado ao contêiner de alias.  O conjunto resultante será uma união de conjuntos obtidos pela avaliação da expressão do contêiner para cada um dos documentos no conjunto subjacente. 

## <a name="examples"></a>Exemplos

### <a name="get-subitems-by-using-the-from-clause"></a>Obter subitens, usando a cláusula FROM

A cláusula FROM pode reduzir o código-fonte para um subconjunto menor. Para enumerar somente uma subárvore de cada item, a sub-raiz pode se tornar o código-fonte, conforme mostrado no exemplo a seguir:

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

A consulta anterior usado uma matriz como a origem, mas você também pode usar um objeto como a origem. A consulta considera qualquer valor JSON válido, definido na fonte para inclusão no resultado. O exemplo a seguir excluiria `Families` que não têm um `address.state` valor.

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