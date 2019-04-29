---
title: Referência da sintaxe SQLFilter do Barramento de Serviços do Azure | Microsoft Docs
description: Detalhes sobre a gramática de SQLFilter.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: e490c7c24ed38e2988c1f097b09b508746f08178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591801"
---
# <a name="sqlfilter-syntax"></a>Sintaxe SQLFilter

Um objeto *SqlFilter* é uma instância da [classe SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) e representa uma expressão de filtro baseada na linguagem SQL avaliada em uma [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Um SqlFilter dá suporte a um subconjunto do padrão SQL-92.  
  
 Este tópico lista detalhes sobre a gramática de SqlFilter.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argumentos  
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O valor `sys` indica o escopo do sistema, em que `<property_name>` é um nome de propriedade pública da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o escopo do usuário, em que `<property_name>` é uma chave de dicionário da [classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). O escopo `user` será o escopo padrão se `<scope>` não for especificado.  
  
## <a name="remarks"></a>Comentários

Uma tentativa de acessar uma propriedade inexistente do sistema é um erro, ao passo que uma tentativa de acessar uma propriedade de usuário inexistente não é um erro. Em vez disso, uma propriedade de usuário inexistente é internamente avaliada como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação de operador.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumentos  

 `<regular_identifier>` é uma cadeia de caracteres representada pela seguinte expressão regular:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Essa gramática significa qualquer cadeia de caracteres que começa com uma letra e é seguida por um ou mais sublinhados/letras/dígitos.  
  
`[:IsLetter:]` significa qualquer caractere Unicode categorizado como uma letra do Unicode. `System.Char.IsLetter(c)` retorna `true` se `c` é uma letra do Unicode.  
  
`[:IsDigit:]` significa qualquer caractere Unicode categorizado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se `c` é um dígito do Unicode.  
  
Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
`<delimited_identifier>` é qualquer cadeia de caracteres incluída em colchetes esquerdo/direito ([]). Um colchete direito é representado como dois colchetes direitos. Estes são exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` é qualquer cadeia de caracteres entre aspas duplas. Aspas duplas no identificador são representadas como duas aspas duplas. Não é recomendável usar identificadores entre aspas, porque pode ser confundido facilmente por uma constante de cadeia de caracteres. Use um identificador delimitado, se possível. Este é um exemplo de `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários
  
`<pattern>` deve ser uma expressão avaliada como uma cadeia de caracteres. Usado como um padrão para o operador LIKE.      Pode conter os seguintes caracteres curinga:  
  
-   `%`:  Qualquer cadeia de caracteres com zero ou mais caracteres.  
  
-   `_`: Qualquer caractere único.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Comentários  

`<escape_char>` deve ser uma expressão avaliada como uma cadeia de caracteres de comprimento 1. Usado como um caractere de escape para o operador LIKE.  
  
 Por exemplo, `property LIKE 'ABC\%' ESCAPE '\'` corresponde a `ABC%`, em vez de a uma cadeia de caracteres que começa com `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumentos  
  
-   `<integer_constant>` é uma cadeia de números que não são incluídos em aspas e que não contêm pontos decimais. Os valores são armazenados como `System.Int64` internamente e seguem o mesmo intervalo.  
  
     Estes são exemplos de constantes longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma cadeia de números que não são incluídos em aspas e que contêm um ponto decimal. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão.  
  
     Em uma versão futura, esse número poderá ser armazenado em outro tipo de dados para dar suporte à semântica de número exato; portanto, você não deve se basear no fato de que o tipo de dados subjacente é `System.Double` para `<decimal_constant>`.  
  
     Estes são exemplos de constantes decimais:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` é um número escrito em notação científica. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão. Estes são exemplos de constantes de número aproximado:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Comentários  

Constantes boolianas são representadas pelas palavras-chave **TRUE** ou **FALSE**. Os valores são armazenados como `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Comentários  

Constantes de cadeia de caracteres são incluídas em aspas simples e incluem caracteres Unicode válidos. Uma aspa simples inserida em uma constante de cadeia de caracteres é representada como duas aspas simples.  
  
## <a name="function"></a>função  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Comentários
  
A função `newid()` retorna um **System.Guid** gerado pelo método `System.Guid.NewGuid()`.  
  
A função `property(name)` retorna o valor da propriedade referenciada por `name`. O valor `name` pode ser qualquer expressão válida que retorna um valor de cadeia de caracteres.  
  
## <a name="considerations"></a>Considerações
  
Considere a seguinte semântica de [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter):  
  
-   Os nomes de propriedade não diferenciam maiúsculas de minúsculas.  
  
-   Os operadores seguem a semântica de conversão implícita do C# sempre que possível.  
  
-   As propriedades do sistema são propriedades públicas expostas em instâncias de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).  
  
    Considere a seguinte semântica de `IS [NOT] NULL`:  
  
    -   `property IS NULL` é avaliado como `true` se a propriedade não existe ou se o valor da propriedade é `null`.  
  
### <a name="property-evaluation-semantics"></a>Semântica de avaliação da propriedade  
  
- Uma tentativa de avaliar uma propriedade do sistema inexistente gerará uma exceção [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception).  
  
- Uma propriedade que não existe internamente é avaliada como **desconhecida**.  
  
  Avaliação desconhecida em operadores aritméticos:  
  
- Em operadores binários, se o lado esquerdo e/ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
- Em operadores unários, se um operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em operadores de comparação binária:  
  
- Se o lado esquerdo e/ou direito dos operandos for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] LIKE`:  
  
- Se um operando for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida em `[NOT] IN`:  
  
- Se o operando esquerdo for avaliado como **desconhecido**, o resultado será **desconhecido**.  
  
  Avaliação desconhecida no operador **AND**:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Avaliação desconhecida no operador **OR**:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Semântica de associação do operador
  
-   Operadores de comparação, como `>`, `>=`, `<`, `<=`, `!=` e `=` seguem a mesma semântica que a associação do operador do C# em promoções de tipo de dados e conversões implícitas.  
  
-   Operadores aritméticos, como `+`, `-`, `*`, `/` e `%` seguem a mesma semântica que a associação do operador do C# em promoções de tipo de dados e conversões implícitas.

## <a name="next-steps"></a>Próximas etapas

- [Classe SQLFilter (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [Classe SQLFilter (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Classe SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
