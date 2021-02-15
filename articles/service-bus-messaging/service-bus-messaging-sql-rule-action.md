---
title: Sintaxe da ação SQL da regra de assinatura do barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece uma referência para a sintaxe de ação de regra SQL. As ações são gravadas na sintaxe baseada em linguagem SQL executada em uma mensagem.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: f7b8cdfcccc22508b98a42391d2a0ef9955232d0
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742668"
---
# <a name="subscription-rule-sql-action-syntax"></a>Sintaxe da ação SQL da regra de assinatura

Uma *ação SQL* é usada para manipular metadados de mensagem depois que uma mensagem é selecionada por um filtro de uma regra de assinatura. É uma expressão de texto que se baseia em um subconjunto do padrão SQL-92. As expressões de ação são usadas com o `sqlExpression` elemento da propriedade ' action ' de um barramento de serviço `Rule` em um [modelo de Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md), ou o argumento de CLI do Azure `az servicebus topic subscription rule create` comando [`--action-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) e várias funções de SDK que permitem o gerenciamento de regras de assinatura.
  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
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
  
-   `<scope>` é uma cadeia de caracteres opcional que indica o escopo do `<property_name>`. Os valores válidos são `sys` ou `user`. O valor `sys` indica o escopo do sistema, em que `<property_name>` é um nome de propriedade pública da [Classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` indica o escopo do usuário, em que `<property_name>` é uma chave de dicionário da [Classe BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` escopo será o escopo padrão se `<scope>` não for especificado.  
  
### <a name="remarks"></a>Comentários  

Uma tentativa de acessar uma propriedade de sistema inexistente é um erro, enquanto uma tentativa de acessar uma propriedade de usuário não existente não é um erro. Em vez disso, uma propriedade de usuário inexistente é internamente avaliada como um valor desconhecido. Um valor desconhecido é tratado especialmente durante a avaliação de operador.  
  
## <a name="property_name"></a>property_name  
  
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
  
 Isso significa qualquer cadeia de caracteres que começa com uma letra e é seguida por um ou mais sublinhados/letras/dígitos.  
  
 `[:IsLetter:]` significa qualquer caractere Unicode categorizado como uma letra do Unicode. `System.Char.IsLetter(c)` retorna `true` se `c` é uma letra do Unicode.  
  
 `[:IsDigit:]` significa qualquer caractere Unicode categorizado como um dígito decimal. `System.Char.IsDigit(c)` retorna `true` se `c` é um dígito do Unicode.  
  
 Um `<regular_identifier>` não pode ser uma palavra-chave reservada.  
  
 `<delimited_identifier>` é qualquer cadeia de caracteres incluída em colchetes esquerdo/direito ([]). Um colchete direito é representado como dois colchetes direitos. Estes são exemplos de `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` é qualquer cadeia de caracteres entre aspas duplas. Aspas duplas no identificador são representadas como duas aspas duplas. Não é recomendável usar identificadores entre aspas porque ele pode ser facilmente confundido com uma constante de cadeia de caracteres. Use um identificador delimitado, se possível. Este é um exemplo de `<quoted_identifier>`:  
  
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
  
-   `%`: qualquer cadeia de caracteres com zero ou mais caracteres.  
  
-   `_`: qualquer caractere único.  
  
## <a name="escape_char"></a>escape_char  
  
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
  
-   `<integer_constant>` é uma cadeia de números que não está entre aspas e não contém pontos decimais. Os valores são armazenados como `System.Int64` internamente e seguem o mesmo intervalo.  
  
     Estes são exemplos de constantes longas:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` é uma cadeia de números que não está entre aspas e que contém um ponto decimal. Os valores são armazenados como `System.Double` internamente e seguem o mesmo intervalo e a mesma precisão.  
  
     Em uma versão futura, esse número pode ser armazenado em um tipo de dados diferente para dar suporte à semântica numérica exata, portanto, você não deve contar com o fato de que o tipo de dados subjacente é `System.Double` para `<decimal_constant>` .  
  
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
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Comentários
  
Constantes boolianas são representadas pelas palavras-chave `TRUE` ou `FALSE`. Os valores são armazenados como `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
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

A `newid()` função retorna um `System.Guid` gerado pelo `System.Guid.NewGuid()` método.  
  
A função `property(name)` retorna o valor da propriedade referenciada por `name`. O valor `name` pode ser qualquer expressão válida que retorna um valor de cadeia de caracteres.  

[!INCLUDE [service-bus-filter-examples](../../includes/service-bus-filter-examples.md)]
  
## <a name="considerations"></a>Considerações

- SET é usado para criar uma nova propriedade ou atualizar o valor de uma propriedade existente.
- REMOVE é usado para remover uma propriedade.
- SET executa uma conversão implícita, se possível, quando o tipo de expressão e o tipo de propriedade existente são diferentes.
- A ação falhará se propriedades do sistema inexistentes forem referenciadas.
- A ação não falhará se Propriedades de usuário inexistentes forem referenciadas.
- Uma propriedade de usuário inexistente é avaliada como “Desconhecida” internamente, seguindo a mesma semântica de [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) ao avaliar operadores.

## <a name="next-steps"></a>Próximas etapas

- [Classe sqlruleaction (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Classe sqlruleaction (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlruleaction)
- [Classe sqlruleaction (Java)](/java/api/com.microsoft.azure.servicebus.rules.sqlruleaction)
- [Sqlruleaction (JavaScript)](/javascript/api/@azure/service-bus/sqlruleaction)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)