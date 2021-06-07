---
title: Azure AD Connect expressões de sincronização de nuvem e referência de função
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869d60d4ccb3bae58924fe4221f83b1b0125ae04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612969"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Escrever expressões para mapeamentos de atributo no Azure Active Directory
Quando você configura a sincronização de nuvem, um dos tipos de mapeamentos de atributo que você pode especificar é um mapeamento de expressão. 

O mapeamento de expressão permite que você personalize atributos usando uma expressão do tipo script.  Isso permite transformar os dados locais em um valor novo ou diferente.  Por exemplo, talvez você queira combinar dois atributos em um único atributo porque esse único atributo é usado por um dos seus aplicativos de nuvem.

O documento a seguir abordará as expressões do script que são usadas para transformar os dados.  Isso é apenas parte do processo.  Em seguida, você precisará usar essa expressão e colocá-la em uma solicitação da Web para seu locatário.  Para obter mais informações sobre isso, confira [Transformações](how-to-transformation.md)

## <a name="syntax-overview"></a>Visão geral da sintaxe
A sintaxe de expressões para mapeamentos de atributos é semelhante à das funções de VBA (Visual Basic for Applications).

* A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido pelos argumentos entre parênteses:  <br>
  *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* Você pode aninhar funções dentro umas das outras. Por exemplo: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Você pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"
  3. Outras funções. Por exemplo: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ela deve ser escapada com o símbolo de barra invertida (\). Por exemplo: "nome da empresa: \\ " Contoso \\ ""

## <a name="list-of-functions"></a>Lista de funções
| Lista de funções | Descrição |
|-----|----|
|[Append](#append)| seleciona um valor da cadeia de caracteres de source e acrescenta o sufixo ao final dela.|
|[BitAnd](#bitand)| a função BitAnd define os bits especificados em um valor.|
|[CBool](#cbool)| a função CBool retorna um valor booliano com base na expressão avaliada|
|[ConvertFromBase64](#convertfrombase64)| a função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.|
|[ConvertToBase64](#converttobase64)| a função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Unicode em base64. |
|[ConvertToUTF8Hex](#converttoutf8hex)| a função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado em UTF8 hexadecimal.|
|[Count](#count)| a função Count retorna o número de elementos em um atributo de valores múltiplos|
|[CStr](#cstr)| a função CStr converte em um tipo de dados da cadeia de caracteres.|
|[DateFromNum](#datefromnum)| a função DateFromNum converte um valor, no formato de data do AD, em um tipo DateTime.|
|[DNComponent](#dncomponent)| a função DNComponent retorna o valor de um componente DN especificado saindo da esquerda.|
|[Erro](#error)| a função Error é usada para retornar um erro personalizado.|
|[FormatDateTime](#formatdatetime) | obtém uma cadeia de caracteres de data de um formato e a converte em um formato diferente.| 
|[GUID](#guid)|A função GUID gera um novo GUID aleatório.|           
|[IIF](#iif)| a função IIF retorna um valor de um conjunto de valores possíveis com base em uma condição especificada.|
|[InStr](#instr)|A função InStr localiza a primeira ocorrência de uma substring em uma cadeia de caracteres.|
|[IsNull](#isnull)| se a expressão for avaliada como Null, a função IsNull retornará true.|
|[IsNullOrEmpty](#isnullorempty)| se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.|         
|[IsPresent](#ispresent)| se a expressão for avaliada como uma cadeia de caracteres que não é Null nem vazia, a função IsPresent retornará true.|    
|[IsString](#isstring)| se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função IsString será avaliada como True.|
|[Item](#item)| a função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.|
|[Join](#join) |Join() é semelhante a Append(), exceto que pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres de **separador**.| 
|[Mantida](#left)| a função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.|
|[Meio](#mid) | retorna uma subcadeia de caracteres do valor de source. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.|
|[NormalizeDiacritics](#normalizediacritics)|Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com nenhum dos caracteres diacríticos substituídos por caracteres não diacríticas equivalentes.|
|[Válido](#not) |Inverte o valor booliano da **origem**. Se o valor da **origem** for "*True*", retorna "*False*". Caso contrário, diferente, retorna "*True*".| 
|[RemoveDuplicates](#removeduplicates)| a função RemoveDuplicates obtém uma cadeia de caracteres de valores múltiplos e verifica se cada valor é exclusivo.| 
|[Substituir](#replace) | substitui valores dentro de uma cadeia de caracteres. | 
|[SelectUniqueValue](#selectuniquevalue)|Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no aplicativo/diretório de destino.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuída a um usuário para um determinado aplicativo.| 
|[Split](#split)|Divide uma cadeia de caracteres em uma matriz com vários valores, usando o caractere delimitador especificado.|
|[StringFromSID](#stringfromsid)| a função StringFromSid converte uma matriz de bytes, que contém um identificador de segurança, em uma cadeia de caracteres.| 
|[StripSpaces](#stripspaces) | remove todos os caracteres de espaço (" ") da caracteres da cadeia de source.| 
|[Switch](#switch)|Quando o valor da **origem** corresponde a uma **chave**, retorna o **valor** dessa **chave**. | 
|[ToLower](#tolower)|Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas.| 
|[ToUpper](#toupper)|Usa um valor de cadeia de caracteres de *origem* e o converte em letras maiúsculas usando as regras de cultura especificadas.|
|[Trim](#trim)| a função Trim remove os espaços em branco à esquerda e à direita de uma cadeia de caracteres.|
|[Word](#word)| a função Word retorna uma palavra contida em uma cadeia de caracteres com base nos parâmetros que descrevem os delimitadores a serem usados e o número de palavras a serem retornadas.|

---
### <a name="append"></a>Acrescentar
**Funcionamento**<br>  Append(source, suffix)

**Descrição:**<br>  seleciona um valor da cadeia de caracteres de source e acrescenta o sufixo ao final dela.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
   | **suffix** |Obrigatório |String |A cadeia de caracteres que você deseja acrescentar ao final do valor de source. |

---
### <a name="bitand"></a>BitAnd
**Descrição:**  
 a função BitAnd define os bits especificados em um valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* value1, value2: os valores numéricos que devem ser agrupados com AND

**Comentários**  
 esta função converte ambos os parâmetros na representação binária e define um bit para:

* 0 – se um ou ambos os bits correspondentes em *value1* e *value2* forem 0
* 1 - se ambos os bits correspondentes são 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
 
 `BitAnd(&HF, &HF7)`</br>
  Retorna 7, já que os hexadecimais "F" AND "F7" são avaliados como esse valor.

---

### <a name="cbool"></a>CBool
**Descrição:**  
 a função CBool retorna um valor booliano com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Comentários**  
Se a expressão é avaliada como um valor diferente de zero, CBool retorna True; caso contrário, retorna False.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Retorna True se ambos os atributos têm o mesmo valor.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrição:**  
 a função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)` - adota Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* source: cadeia de caracteres codificada em Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos retornam "*Hello world!*"

---
### <a name="converttobase64"></a>ConvertToBase64
**Descrição:**  
 a função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Unicode em base64.  
Converte o valor de uma matriz de inteiros em sua representação equivalente de cadeia de caracteres, que é codificada com dígitos em base 64.

**Sintaxe:**  
`str ConvertToBase64(str source)`

**Exemplo:**  
`ConvertToBase64("Hello world!")`  
 retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Descrição:**  
 a função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado em UTF8 hexadecimal.

**Sintaxe:**  
`str ConvertToUTF8Hex(str source)`

**Comentários**  
 o formato de saída dessa função é usado pelo Azure Active Directory como o formato do atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
 retorna 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Descrição:**  
 a função Count retorna o número de elementos em um atributo de valores múltiplos

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Descrição:**  
 a função CStr converte em um tipo de dados da cadeia de caracteres.

**Sintaxe:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* valor: pode ser um valor numérico, o atributo de referência ou booliano.

**Exemplo:**  
`CStr([dn])`  
 poderia retornar "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum
**Descrição:**  
 a função DateFromNum converte um valor, no formato de data do AD, em um tipo DateTime.

**Sintaxe:**  
`dt DateFromNum(num value)`

**Exemplo:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
 retorna um DateTime que representa 01/01/2012 23:00:00

---
### <a name="dncomponent"></a>DNComponent
**Descrição:**  
 a função DNComponent retorna o valor de um componente DN especificado saindo da esquerda.

**Sintaxe:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: o atributo de referência a interpretar
* ComponentNumber: o componente no DN a retornar

**Exemplo:**  
`DNComponent(CRef([dn]),1)`  
 se dn é “cn=Joe,ou=…,” ele retorna Joe

---
### <a name="error"></a>Erro
**Descrição:**  
 a função Error é usada para retornar um erro personalizado.

**Sintaxe:**  
`void Error(str ErrorMessage)`

**Exemplo:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
 se o atributo accountName não estiver presente, gere um erro no objeto.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funcionamento**<br>  FormatDateTime(source, inputFormat, outputFormat)

**Descrição:**<br>  obtém uma cadeia de caracteres de data de um formato e a converte em um formato diferente.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
   | **inputFormat** |Obrigatório |String |Formato esperado do valor de source. Para formatos com suporte, consulte [/dotnet/Standard/base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
   | **outputFormat** |Obrigatório |String |Formato da data de saída. |

---
### <a name="guid"></a>Guid
**Descrição:**  
A função GUID gera um novo GUID aleatório

**Sintaxe:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Descrição:**  
 a função IIF retorna um valor de um conjunto de valores possíveis com base em uma condição especificada.

**Sintaxe:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* condition: qualquer valor ou expressão que pode ser avaliada como true ou false.
* valueIfTrue: se a condição for avaliada como true, o valor será retornado.
* valueIfFalse: se a condição for avaliada como false, o valor será retornado.

**Exemplo:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
  se o usuário for um estagiário, retornará o alias de um usuário com "t-" adicionado ao início; caso contrário, retornará o alias do usuário como está.

---
### <a name="instr"></a>InStr
**Descrição:**  
 a função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia de caracteres a ser pesquisada
* stringmatch: cadeia de caracteres a ser localizada
* start: posição inicial para se localizar a subcadeia de caracteres
* compare: vbTextCompare ou vbBinaryCompare

**Comentários**  
 retorna a posição onde a subcadeia de caracteres foi encontrada ou 0, se não foi encontrada.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
 é avaliado como 5

`InStr("repEated","e",3,vbBinaryCompare)`  
 é avaliado como 7

---
### <a name="isnull"></a>IsNull
**Descrição:**  
 se a expressão for avaliada como Null, a função IsNull retornará true.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Comentários**  
 para um atributo, um valor Null é expresso pela ausência do atributo.

**Exemplo:**  
`IsNull([displayName])`  
 retornará True se o atributo não estiver presente no CS ou no MV.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Descrição:**  
 se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.

**Sintaxe:**  
`bool IsNullOrEmpty(var Expression)`

**Comentários**  
 para um atributo, isso seria avaliado como True se o atributo estivesse ausente ou presente, mas fosse uma cadeia de caracteres vazia.  
O inverso dessa função é chamado de IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
 retornará True se o atributo não estiver presente ou for uma cadeia de caracteres vazia no CS ou no MV.

---
### <a name="ispresent"></a>IsPresent
**Descrição:**  
 se a expressão for avaliada como uma cadeia de caracteres que não é Null nem vazia, a função IsPresent retornará true.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Comentários**  
 o inverso dessa função é chamado de IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Item
**Descrição:**  
 a função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* attribute: atributo com valores múltiplos
* index: índice para um item na cadeia de caracteres com vários valores.

**Comentários**  
 a função Item é útil com a função Contains, desde que a última função retorne o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice está fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
 retorna o endereço de email principal.

---
### <a name="isstring"></a>IsString
**Descrição:**  
 se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função IsString será avaliada como True.

**Sintaxe:**  
`bool IsString(var expression)`

**Comentários**  
 usada para determinar se CStr() pode ter êxito ao analisar a expressão.

---
### <a name="join"></a>Ingressar
**Funcionamento**<br>  Join(separator, source1, source2, …)

**Descrição:**<br> Join() é semelhante a Append(), exceto que pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres de **separador**.

Se um dos valores de source for um atributo com vários valores, todos os valores nesse atributo serão unidos, separados pelo valor do separador.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **separator** |Obrigatório |String |Cadeia de caracteres usada para separar os valores de source quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se não for necessário nenhum separador. |
   | **source1  … sourceN** |Obrigatório, número de vezes variável |String |Valores de cadeia de caracteres a serem unidos. |

---
### <a name="left"></a>Esquerda
**Descrição:**  
 a função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* string: a cadeia de caracteres da qual retornar caracteres
* NumChars: um número que identifica o número de caracteres a ser retroando do início (esquerda) da cadeia de caracteres

**Comentários**  
 uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retorne a cadeia de caracteres vazia.
* Se numChars < 0, retorne a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia (ou seja, que contém todos os caracteres no parâmetro 1) será retornada.

**Exemplo:**  
`Left("John Doe", 3)`  
Retorna `Joh`.

---
### <a name="mid"></a>Mid
**Funcionamento**<br>  Mid(source, start, length)

**Descrição:**<br>  retorna uma subcadeia de caracteres do valor de source. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Geralmente o nome do atributo. |
   | **start** |Obrigatório |inteiro |Índice na cadeia de caracteres de **origem** em que a subcadeia deve iniciar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
   | **length** |Obrigatório |inteiro |Comprimento da subcadeia de caracteres. Se o comprimento terminar fora da cadeia de caracteres **source**, a função retornará uma subcadeia de caracteres do índice **start** até o final da cadeia de caracteres **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Funcionamento**<br> NormalizeDiacritics(source)

**Descrição:**<br> Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com nenhum dos caracteres diacríticos substituídos por caracteres não diacríticas equivalentes. Normalmente usado para converter os nomes e sobrenomes que contêm caracteres diacríticos (acentos) em valores legais que podem ser usados em vários identificadores de usuário, como nomes de entidade de segurança do usuário, nomes de conta SAM e endereços de email.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Not
**Funcionamento**<br>  Not(source)

**Descrição:**<br> Inverte o valor booliano da **origem**. Se o valor da **origem** for "*True*", retorna "*False*". Caso contrário, diferente, retorna "*True*".

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |Cadeia de caracteres booliana |Valores da **origem** esperados são "True" ou "False". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Descrição:**  
 a função RemoveDuplicates obtém uma cadeia de caracteres de valores múltiplos e verifica se cada valor é exclusivo.

**Sintaxe:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Exemplo:**  
`RemoveDuplicates([proxyAddresses])`  
 retorna um atributo proxyAddress corrigido no qual todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substitua
**Funcionamento**<br> Substitua(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Descrição:**<br>
 substitui valores dentro de uma cadeia de caracteres. Ela funciona de maneira diferente dependendo dos parâmetros fornecidos:

* Quando **oldValue** e **replacementValue** são fornecidos:
  
  * Substitui todas as ocorrências de **OldValue** na **fonte**  por **replacevalue**
* Quando **oldValue** e **template** são fornecidos:
  
  * Substitui todas as ocorrências de **oldValue** no **modelo** pelo valor de **origem**
* Quando **regexPattern** e **replacementValue** são fornecidos:

  * A função aplica o **regexPattern** à cadeia de caracteres **source** e você pode usar os nomes de grupo Regex para construir a cadeia de caracteres para **replacementValue**
* Quando **regexPattern**, **regexGroupName** e **replacementValue** são fornecidos:
  
  * A função aplica o **regexPattern** à cadeia de caracteres **source** e substitui todos os valores correspondentes a **regexGroupName** com **replacementValue**
* Quando **regexPattern**, **regexGroupName** e **replacementAttributeName** são fornecidos:
  
  * Se **source** não tiver um valor, **source** será retornado
  * Se **source** tiver um valor, a função aplicará o **regexPattern** à cadeia de caracteres **source** e substituirá todos os valores correspondentes a **regexGroupName** pelo valor associado a **replacementAttributeName**

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Normalmente o nome do atributo do objeto de **origem** . |
   | **oldValue** |Opcional |Cadeia de caracteres |Valor a ser substituído em **source** ou **template**. |
   | **regexPattern** |Opcional |Cadeia de caracteres |Padrão Regex para o valor a ser substituído na **origem**. Ou, quando **replacementPropertyName** for usado, o padrão para extrair o valor de **replacementPropertyName**. |
   | **regexGroupName** |Opcional |Cadeia de caracteres |Nome do grupo dentro de **regexPattern**. Somente quando **replacementPropertyName** for usado, extrairemos o valor desse grupo como **replacementValue** de **replacementPropertyName**. |
   | **replacementValue** |Opcional |Cadeia de caracteres |Novo valor com o qual substituir um antigo. |
   | **replacementAttributeName** |Opcional |Cadeia de caracteres |Nome do atributo a ser usado para o valor de substituição |
   | **modelos** |Opcional |Cadeia de caracteres |Quando o valor do **modelo** for fornecido, procuraremos **OldValue** dentro do modelo e o substituíremos pelo valor de **origem** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funcionamento**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Descrição:**<br> Requer um mínimo de dois argumentos, que são definidas usando expressões de regras de geração de valor exclusivo. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no aplicativo/diretório de destino. O primeiro valor exclusivo encontrado será retornado o um. Se todos os valores já existem no destino, a entrada será obter mantida em garantia e o motivo pelo qual obtém registrado nos logs de auditoria. Não há nenhum limite superior para o número de argumentos que podem ser fornecidos.

> [!NOTE]
> - Essa é uma função de nível superior, ele não pode ser aninhado.
> - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.  
> - Essa função destina-se somente a ser usado para criações de entrada. Ao usá-lo com um atributo, defina a **Aplicar mapeamento** propriedade **somente durante a criação do objeto**.
> - Atualmente, essa função só é compatível com "Provisionamento de usuário do Workday para o Active Directory". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |Pelo menos 2 são necessários, sem limite superior |String | Lista de regras de geração de valor exclusivo para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funcionamento**<br> SingleAppRoleAssignment([appRoleAssignments])

**Descrição:**<br> Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuída a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma cadeia de caracteres de nome de função única. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído por usuário por vez, e se várias funções forem atribuídas a cadeia de caracteres de função retornada pode não ser previsível. 

**Parâmetro**<br> 

  | Nome | Obrigatório/repetição | Type | Observações |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |Obrigatório |String |Objeto **[appRoleAssignments]**. |

---
### <a name="split"></a>Divisão
**Funcionamento**<br> Split(source, delimiter)

**Descrição:**<br> Divide uma cadeia de caracteres em uma matriz com vários valores, usando o caractere delimitador especificado.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Valor da **origem** para atualizar. |
   | **delimitador** |Obrigatório |String |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
 a função StringFromSid converte uma matriz de bytes, que contém um identificador de segurança, em uma cadeia de caracteres.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Funcionamento**<br>  StripSpaces(source)

**Descrição:**<br>  remove todos os caracteres de espaço (" ") da caracteres da cadeia de source.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Valor da **origem** para atualizar. |

---
### <a name="switch"></a>Comutador
**Funcionamento**<br>  Switch(source, defaultValue, key1, value1, key2, value2, …)

**Descrição:**<br> Quando o valor da **origem** corresponde a uma **chave**, retorna o **valor** dessa **chave**. Se o valor da **origem** não corresponder a nenhuma chave, retorna **defaultValue**.  Os parâmetros **key** e **value** devem sempre ocorrer em pares. A função sempre espera um número par de parâmetros.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Valor de **origem** a ser verificado. |
   | **defaultValue** |Opcional |Cadeia de caracteres |Valor padrão a ser usado quando source não corresponde a nenhum parâmetro. Pode ser uma cadeia de caracteres vazia (""). |
   | **chave** |Obrigatório |String |Parâmetro **key** com o qual comparar o valor de **source**. |
   | **value** |Obrigatório |String |Valor de substituição para o **source** que corresponde ao parâmetro key. |

---
### <a name="tolower"></a>ToLower
**Funcionamento**<br> ToLower (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetro**<br> 

   | Nome | Obrigatório/repetição | Type | Observações |
   | --- | --- | --- | --- |
   | **source** |Obrigatório |String |Normalmente o nome do atributo do objeto de source |
   | **cultura** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---

### <a name="toupper"></a>ToUpper
**Funcionamento**<br> ToUpper (origem, cultura)

**Descrição:**<br> Usa um valor de cadeia de caracteres de *origem* e o converte em letras maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetro**<br> 

  | Nome | Obrigatório/repetição | Type | Observações |
  | --- | --- | --- | --- |
  | **source** |Obrigatório |String |Normalmente o nome do atributo do objeto de source. |
  | **cultura** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---

### <a name="trim"></a>Trim
**Descrição:**  
 a função Trim remove os espaços em branco à esquerda e à direita de uma cadeia de caracteres.

**Sintaxe:**  
`str Trim(str value)`  

**Exemplo:**  
`Trim(" Test ")`  
 retorna "test".

`Trim([proxyAddresses])`  
 remove espaços à direita e à esquerda para cada valor no atributo proxyAddress.

---
### <a name="word"></a>Word
**Descrição:**  
 a função Word retorna uma palavra contida em uma cadeia de caracteres com base nos parâmetros que descrevem os delimitadores a serem usados e o número de palavras a serem retornadas.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: a cadeia de caracteres da qual retornar uma palavra
* WordNumber: um número que identifica qual número de palavras deve retornar.
* delimitadores: uma cadeia de caracteres que representa o delimitador(es) que deve ser usado para identificar palavras

**Comentários**  
 cada cadeia de caracteres separada por um dos caracteres delimitadores na cadeia de caracteres é identificada como palavra:

* Se number < 1, retorna uma cadeia de caracteres vazia.
* Se a cadeia de caracteres for nula, retorna a cadeia de caracteres vazia.

Se a cadeia de caracteres for menor que o número de palavras ou a cadeia não contiver nenhuma palavra identificada por delimitadores, uma cadeia de caracteres vazia será retornada.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
 retorna "brown"

`Word("This,string!has&many separators",3,",!&#")`  
 retornaria "has"

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Retirar o nome de domínio conhecido
Você precisa retirar um nome de domínio conhecido do email de um usuário para obter um nome de usuário. <br>
 Por exemplo, se o domínio for "contoso.com", você pode usar a seguinte expressão:

**Expressão** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** <br>

* **INPUT** (mail): "john.doe@contoso.com"
* **SAÍDA**: "davi.barros"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixo constante ao nome de usuário
Se você estiver usando um Salesforce Sandbox, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.

**Expressão** <br>
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** <br>

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **Saída**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias de usuário concatenando partes do nome e do sobrenome
Você precisa gerar um alias de usuário selecionando as três primeiras letras do nome do usuário e as cinco primeiras letras do sobrenome do usuário.

**Expressão** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** <br>

* **ENTRADA** (givenName): "Davi"
* **ENTRADA** (sobrenome): "Barros"
* **Saída**: "davibarros"

### <a name="remove-diacritics-from-a-string"></a>Remover diacríticos de uma cadeia de caracteres
Você precisa substituir caracteres que contenham os acentos por caracteres equivalentes que não contenham acentos.

**Expressão** <br>
NormalizeDiacritics([givenName])

**Entrada/saída de exemplo:** <br>

* **INPUT** (givenName): "Zoë"
* **SAÍDA**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
É necessário usar uma lista de cadeias de caracteres delimitada por vírgula e dividi-las em uma matriz que possa ser conectada a um atributo com vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi preenchida em extensionAttribute5 no Azure AD.

**Expressão** <br>
Split([extensionAttribute5], ",")

**Entrada/saída de exemplo:** <br>

* **Entrada** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **SAÍDA**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Gerar data como uma cadeia de caracteres em um determinado formato
Você deseja enviar datas para um aplicativo SaaS em um determinado formato. <br>
 Por exemplo, você deseja formatar datas para o ServiceNow.

**Expressão** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **ENTRADA** (extensionAttribute1): "20150123105347.1Z"
* **Saída**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base em um conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no AD do Azure. <br>
 Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Australia/Sydney".

**Expressão** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **ENTRADA** (estado): "QLD"
* **SAÍDA**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
É necessário localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**Entrada/saída de exemplo:**

* **ENTRADA** (mailNickname: "john_doe72"
* **Saída**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Converter o valor userPrincipalName (UPN) gerado em letras minúsculas
No exemplo a seguir, o valor do UPN é gerado pela concatenação dos campos de origem PreferredFirstName e PreferredLastName, e a função ToLower opera na cadeia de caracteres gerada para converter todos os caracteres em letras minúsculas. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **Saída**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Gerar um valor exclusivo para o atributo userPrincipalName (UPN)
Com base no usuário primeiro nome, sobrenome e sobrenome, você precisa gerar um valor para o atributo UPN e procure sua exclusividade no diretório de destino AD antes de atribuir o valor para o atributo UPN.

**Expressão** <br>

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**Entrada/saída de exemplo:**

* **INPUT** (PreferredFirstName): "John"
* **INPUT** (PreferredLastName): "Smith"
* **Saída**: " John.Smith@contoso.com " se o valor UPN de John.Smith@contoso.com ainda não existir no diretório
* **Saída**: " J.Smith@contoso.com " se o valor UPN John.Smith@contoso.com já existir no diretório
* **Saída**: " Jo.Smith@contoso.com " se os dois valores UPN acima já existirem no diretório


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)