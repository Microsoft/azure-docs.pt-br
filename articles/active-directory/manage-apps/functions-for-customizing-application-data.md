---
title: Escrevendo expressões para mapeamentos de atributo no Azure Active Directory | Microsoft Docs
description: Aprenda a usar o mapeamentos de expressão para transformar valores de atributo em um formato aceitável durante o provisionamento automatizado de objetos de aplicativo SaaS no Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12b75c2df7d11b0e90c5dccc3bc2aae4e0fb0c1e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204478"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrevendo expressões para mapeamentos de atributo no Active Directory do Azure
Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Nesses casos, você deve escrever uma expressão semelhante a script que permite transformar os dados de usuários em formatos que são mais aceitáveis para o aplicativo SaaS.

## <a name="syntax-overview"></a>Visão geral da sintaxe
A sintaxe de expressões para mapeamentos de atributos é semelhante à das funções de VBA (Visual Basic for Applications).

* A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido pelos argumentos entre parênteses: <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* Você pode aninhar funções dentro umas das outras. Por exemplo: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* Você pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"
  3. Outras funções. Por exemplo:  FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ela deve ser escapada com o símbolo de barra invertida (\). Por exemplo: "Nome da empresa: \\"Contoso\\""

## <a name="list-of-functions"></a>Lista de funções
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Acrescentar
**Função:**<br> Append(source, suffix)

**Descrição:**<br> seleciona um valor da cadeia de caracteres de source e acrescenta o sufixo ao final dela.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **suffix** |Obrigatório |Cadeia de caracteres |A cadeia de caracteres que você deseja acrescentar ao final do valor de source. |

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:**<br> FormatDateTime(source, inputFormat, outputFormat)

**Descrição:**<br> obtém uma cadeia de caracteres de data de um formato e a converte em um formato diferente.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **inputFormat** |Obrigatório |Cadeia de caracteres |Formato esperado do valor de source. Para formatos com suporte, consulte [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Obrigatório |Cadeia de caracteres |Formato da data de saída. |

---
### <a name="join"></a>Ingressar
**Função:**<br> Join(separator, source1, source2, …)

**Descrição:**<br> Join() é semelhante a Append(), exceto por poder combinar diversos valores de cadeia de caracteres **source** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres de **separator**.

Se um dos valores de source for um atributo com vários valores, todos os valores nesse atributo serão unidos, separados pelo valor do separador.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **separator** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres usada para separar os valores de source quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se não for necessário nenhum separador. |
| **source1  … sourceN** |Obrigatório, número de vezes variável |Cadeia de caracteres |Valores de cadeia de caracteres a serem unidos. |

---
### <a name="mid"></a>Mid
**Função:**<br> Mid(source, start, length)

**Descrição:**<br> retorna uma subcadeia de caracteres do valor de source. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Geralmente o nome do atributo. |
| **iniciar** |Obrigatório |inteiro |Índice na cadeia de caracteres de **source** em que a subcadeia de caracteres deve começar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
| **length** |Obrigatório |inteiro |Comprimento da subcadeia de caracteres. Se o comprimento terminar fora da cadeia de caracteres **source**, a função retornará uma subcadeia de caracteres do índice **start** até o final da cadeia de caracteres **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com nenhum dos caracteres diacríticos substituídos por caracteres não diacríticas equivalentes. Normalmente usado para converter os nomes e sobrenomes que contêm caracteres diacríticos (acentos) em valores legais que podem ser usados em vários identificadores de usuário, como nomes de entidade de segurança do usuário, nomes de conta SAM e endereços de email.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>não
**Função:**<br> Not(source)

**Descrição:**<br> Inverte o valor booliano de **source**. Se o valor de **source** for "*True*", retorna "*False*". Caso contrário, retorna "*True*".

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres booliana |Os valores de **source** esperados são "True" ou "False". |

---
### <a name="replace"></a>Substitua
**Função:**<br> Substitua(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrição:**<br>
substitui valores dentro de uma cadeia de caracteres. Ela funciona de maneira diferente dependendo dos parâmetros fornecidos:

* Quando **oldValue** e **replacementValue** são fornecidos:
  
  * Substitui todas as ocorrências de oldValue em source por replacementValue
* Quando **oldValue** e **template** são fornecidos:
  
  * Substitui todas as ocorrências de **oldValue** em **template** com o valor de **source**
* Quando **regexPattern**, **regexGroupName** e **replacementValue** são fornecidos:
  
  * Substitui todos os valores que correspondem a oldValueRegexPattern na cadeia de caracteres de source por replacementValue
* Quando **regexPattern**, **regexGroupName** e **replacementPropertyName** são fornecidos:
  
  * Se **source** não tiver um valor, **source** será retornado
  * Se **source** tiver um valor, usa **oldValueRegexPattern** e **oldValueRegexGroupName** para extrair o valor de substituição da propriedade com **replacementPropertyName**. O valor de substituição é retornado como o resultado

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **oldValue** |Opcional |Cadeia de caracteres |Valor a ser substituído em **source** ou **template**. |
| **regexPattern** |Opcional |Cadeia de caracteres |Padrão de Regex para o valor a ser substituído em **source**. Ou, quando replacementPropertyName for usado, o padrão para extrair o valor da propriedade de substituição. |
| **regexGroupName** |Opcional |Cadeia de caracteres |Nome do grupo dentro de **regexPattern**. Somente quando replacementPropertyName for usado, extrairemos o valor desse grupo como replacementValue da propriedade de substituição. |
| **replacementValue** |Opcional |Cadeia de caracteres |Novo valor com o qual substituir um antigo. |
| **replacementAttributeName** |Opcional |Cadeia de caracteres |Nome do atributo a ser usado para o valor de substituição quando source não tiver nenhum valor. |
| **template** |Opcional |Cadeia de caracteres |Quando o valor de **template** for fornecido, procuraremos **oldValue** dentro de template e o substituiremos pelo valor de source. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Função:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Descrição:**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no aplicativo/diretório de destino. O primeiro valor exclusivo encontrado será retornado o um. Se todos os valores já existem no destino, a entrada será obter mantida em garantia e o motivo pelo qual obtém registrado nos logs de auditoria. Não há nenhum limite superior para o número de argumentos que podem ser fornecidos.

> [!NOTE]
>1. Essa é uma função de nível superior, ele não pode ser aninhado.
>2. Essa função destina-se somente a ser usado para criações de entrada. Ao usá-lo com um atributo, defina a **Aplicar mapeamento** propriedade **somente durante a criação do objeto**.


**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Pelo menos 2 são necessários, sem limite superior |Cadeia de caracteres | Lista de regras de geração de valor exclusivo para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuída a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma cadeia de caracteres de nome de função única. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído por usuário por vez, e se várias funções forem atribuídas a cadeia de caracteres de função retornada pode não ser previsível. 

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obrigatório |Cadeia de caracteres |Objeto **[appRoleAssignments]** . |

---
### <a name="split"></a>Divisão
**Função:**<br> Split(source, delimiter)

**Descrição:**<br> Divide uma cadeia de caracteres em uma matriz com vários valores, usando o caractere delimitador especificado.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |**fonte** a atualizar. |
| **delimiter** |Obrigatório |Cadeia de caracteres |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:**<br> StripSpaces(source)

**Descrição:**<br> remove todos os caracteres de espaço (" ") da caracteres da cadeia de source.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |**fonte** a atualizar. |

---
### <a name="switch"></a>Switch
**Função:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrição:**<br> Quando o valor de **source** corresponde a um parâmetro **key**, retorna **value** para esse parâmetro **key**. Se o valor de **source** não corresponder a nenhum parâmetro key, **defaultValue** será retornado.  Os parâmetros **key** e **value** devem sempre ocorrer em pares. A função sempre espera um número par de parâmetros.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |**Source** a atualizar. |
| **defaultValue** |Opcional |Cadeia de caracteres |Valor padrão a ser usado quando source não corresponde a nenhum parâmetro. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Obrigatório |Cadeia de caracteres |Parâmetro **key** com o qual comparar o valor de **source**. |
| **valor** |Obrigatório |Cadeia de caracteres |Valor de substituição para o **source** que corresponde ao parâmetro key. |

---
### <a name="tolower"></a>ToLower
**Função:**<br> ToLower (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source |
| **cultura** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---
### <a name="toupper"></a>ToUpper
**Função:**<br> ToUpper (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetros:**<br> 

| NOME | Obrigatório/repetição | Tipo | Observações |
| --- | --- | --- | --- |
| **fonte** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **culture** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Retirar o nome de domínio conhecido
Você precisa retirar um nome de domínio conhecido do email de um usuário para obter um nome de usuário. <br>
Por exemplo, se o domínio for "contoso.com", você pode usar a seguinte expressão:

**Expressão:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** <br>

* **INPUT** (mail): "john.doe@contoso.com"
* **SAÍDA**: "davi.barros"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixo constante ao nome de usuário
Se você estiver usando um Salesforce Sandbox, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.

**Expressão:** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** <br>

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **OUTPUT**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias de usuário concatenando partes do nome e do sobrenome
Você precisa gerar um alias de usuário selecionando as três primeiras letras do nome do usuário e as cinco primeiras letras do sobrenome do usuário.

**Expressão:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **INPUT** (givenName): "John"
* **INPUT** (sobrenome): "Doe"
* **OUTPUT**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Remover diacríticos de uma cadeia de caracteres
Você precisa substituir caracteres que contenham os acentos por caracteres equivalentes que não contenham acentos.

**Expressão:** <br>
NormalizeDiacritics([givenName])

**Entrada/saída de exemplo:** <br>

* **INPUT** (givenName): "Zoë"
* **OUTPUT**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
É necessário usar uma lista de cadeias de caracteres delimitada por vírgula e dividi-las em uma matriz que possa ser conectada a um atributo com vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi preenchida em extensionAttribute5 no Azure AD.

**Expressão:** <br>
Split([extensionAttribute5], ",")

**Entrada/saída de exemplo:** <br>

* **ENTRADA** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **SAÍDA**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Gerar data como uma cadeia de caracteres em um determinado formato
Você deseja enviar datas para um aplicativo SaaS em um determinado formato. <br>
Por exemplo, você deseja formatar datas para o ServiceNow.

**Expressão:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **INPUT** (extensionAttribute1): "20150123105347.1Z"
* **OUTPUT**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base em um conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no AD do Azure. <br>
Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Australia/Sydney".

**Expressão:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **INPUT** (state): "QLD"
* **OUTPUT**: "Austrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
É necessário localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Entrada/saída de exemplo:**

* **ENTRADA** (mailNickname: "john_doe72"
* **OUTPUT**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor userPrincipalName (UPN) gerado em letras minúsculas
No exemplo a seguir, o valor do UPN é gerado pela concatenação dos campos de origem PreferredFirstName e PreferredLastName, e a função ToLower opera na cadeia de caracteres gerada para converter todos os caracteres em letras minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **OUTPUT**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar um valor exclusivo para o atributo userPrincipalName (UPN)
Com base no usuário primeiro nome, sobrenome e sobrenome, você precisa gerar um valor para o atributo UPN e procure sua exclusividade no diretório de destino AD antes de atribuir o valor para o atributo UPN.

**Expressão:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **SAÍDA**: "John.Smith@contoso.com" se o valor de UPN do John.Smith@contoso.com ainda não existir no diretório
* **SAÍDA**: "J.Smith@contoso.com" se o valor UPN de John.Smith@contoso.com já existir no diretório
* **SAÍDA**: "Jo.Smith@contoso.com" se os dois valores UPN acima já existirem no diretório

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](user-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Notificações de provisionamento de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
