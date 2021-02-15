---
title: Referência para escrever expressões para mapeamentos de atributo no Azure Active Directory
description: Aprenda a usar o mapeamentos de expressão para transformar valores de atributo em um formato aceitável durante o provisionamento automatizado de objetos de aplicativo SaaS no Active Directory do Azure. Inclui uma lista de referência de funções.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8f5a4d3695722aae14b73bf6bba5f2e38593e08d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255790"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>Referência para escrever expressões para mapeamentos de atributo no Azure AD

Quando você configura o provisionamento de um aplicativo SaaS, um dos tipos de mapeamentos de atributos que você pode especificar é o mapeamento de expressão. Nesses casos, você deve escrever uma expressão semelhante a script que permite transformar os dados de usuários em formatos que são mais aceitáveis para o aplicativo SaaS.

## <a name="syntax-overview"></a>Visão geral da sintaxe

A sintaxe de expressões para mapeamentos de atributos é semelhante à das funções de VBA (Visual Basic for Applications).

* A expressão inteira deve ser definida em termos de funções, que consistem em um nome seguido por argumentos entre parênteses: *FunctionName ( `<<argument 1>>` , `<<argument N>>` )*
* Você pode aninhar funções dentro umas das outras. Por exemplo:  *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Você pode passar três tipos diferentes de argumentos em funções:
  
  1. Atributos, que devem ser colocados entre colchetes. Por exemplo: [attributeName]
  2. Constantes de cadeia de caracteres, que devem ser colocadas entre aspas duplas. Por exemplo: "Estados Unidos"
  3. Outras funções. Por exemplo: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Para constantes de cadeia de caracteres, se você precisar de uma barra invertida (\) ou aspas (") na cadeia de caracteres, ela deve ser escapada com o símbolo de barra invertida (\). Por exemplo: "nome da empresa: \\ " Contoso \\ ""
* A sintaxe diferencia maiúsculas de minúsculas, que deve ser considerada ao digitá-las como cadeias de caracteres em uma função vs copiar colando-as diretamente aqui. 

## <a name="list-of-functions"></a>Lista de funções

[](#append) &nbsp; &nbsp; Acrescentar &nbsp; &nbsp; [](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [](#coalesce) &nbsp; &nbsp; União &nbsp; &nbsp; [](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [Contagem](#count) &nbsp; &nbsp; de &nbsp; &nbsp; [](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [GUID](#guid) &nbsp; &nbsp; do &nbsp; &nbsp; [](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp; [](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; [](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp; [](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp; [Item](#item) &nbsp; &nbsp; de &nbsp; &nbsp; [](#join) &nbsp; &nbsp; Unir &nbsp; &nbsp; [À](#left) &nbsp; &nbsp; esquerda &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [não](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [substitua](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [switch](#switch) ToLower &nbsp; &nbsp; &nbsp; &nbsp; [](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Acrescentar

**Função:** Append (origem, sufixo)

**Descrição:** Usa um valor de cadeia de caracteres de origem e acrescenta o sufixo ao final dele.

**Parâmetro**

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **suffix** |Obrigatório |Cadeia de caracteres |A cadeia de caracteres que você deseja acrescentar ao final do valor de source. |

---
### <a name="bitand"></a>BitAnd
**Função:** BitAnd (value1, value2)

**Descrição:** Essa função converte ambos os parâmetros na representação binária e define um bit para:

- 0 – se um ou ambos os bits correspondentes em value1 e value2 forem 0
- 1 - se ambos os bits correspondentes são 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **value1** |Obrigatório |num |Valor numérico que deve ser AND'eddo com value2|
| **value2** |Obrigatório |num |Valor numérico que deve ser AND'ed com value1|

**Exemplo**
`BitAnd(&HF, &HF7)`

11110111 e 00000111 = 00000111, portanto `BitAnd` retorna 7, o valor binário de 00000111.

---
### <a name="cbool"></a>CBool
**Funcionamento** 
`CBool(Expression)`

**Descrição:**  
 `CBool` Retorna um booliano com base na expressão avaliada. Se a expressão for avaliada como um valor diferente de zero, `CBool` retornará *true*, caso contrário retornará *false*.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório | expressão | Qualquer expressão válida |

**Exemplo:** 
`CBool([attribute1] = [attribute2])`                                                                    
Retorna True se ambos os atributos têm o mesmo valor.

---
### <a name="coalesce"></a>Coalesce
**Função:** Adesão (origem1, origem2,..., defaultValue)

**Descrição:** Retorna o primeiro valor de origem que não é nulo. Se todos os argumentos forem nulos e defaultValue estiver presente, o defaultValue será retornado. Se todos os argumentos forem nulos e defaultValue não estiver presente, o adesão retornará nulo.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source1  … sourceN** | Obrigatório | Cadeia de caracteres |Obrigatório, número de vezes variável. Normalmente o nome do atributo do objeto de source. |
| **defaultValue** | Opcional | Cadeia de caracteres | Valor padrão a ser usado quando todos os valores de origem forem nulos. Pode ser uma cadeia de caracteres vazia ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Função:** ConvertToBase64 (origem)

**Descrição:** A função ConvertToBase64 converte uma cadeia de caracteres em uma cadeia de caracteres Base64 Unicode.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres a ser convertida em base 64|

**Exemplo**
`ConvertToBase64("Hello world!")`

 retorna "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Função:** ConvertToUTF8Hex (origem)

**Descrição:** A função ConvertToUTF8Hex converte uma cadeia de caracteres em um valor codificado hexadecimal UTF8.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres a ser convertida em UTF8 hex|

**Exemplo**
`ConvertToUTF8Hex("Hello world!")`

 retorna 48656C6C6F20776F726C6421

---
### <a name="count"></a>Contagem
**Função:** Contagem (atributo)

**Descrição:** A função Count retorna o número de elementos em um atributo de valores múltiplos

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **Attribute** |Obrigatório |Atributo |Atributo com valores múltiplos que terá elementos contados|

---
### <a name="cstr"></a>CStr
**Função:** CStr (valor)

**Descrição:** A função CStr converte um valor em um tipo de dados de cadeia de caracteres.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **value** |Obrigatório | Numeric, Reference ou Boolean | pode ser um valor numérico, o atributo de referência ou booliano. |

**Exemplo**
`CStr([dn])`

Retorna "CN = Joe, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Função:** DateFromNum (valor)

**Descrição:** A função DateFromNum converte um valor no formato de data do AD em um tipo DateTime.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **value** |Obrigatório | Data | A data do AD a ser convertida no tipo DateTime |

**Exemplo**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

Retorna um DateTime que representa 1º de janeiro de 2012 às 11:13h.

---
### <a name="formatdatetime"></a>FormatDateTime
**Função:** FormatDateTime (origem, inputFormat, outputFormat)

**Descrição:** Usa uma cadeia de caracteres de data de um formato e a converte em um formato diferente.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **inputFormat** |Obrigatório |Cadeia de caracteres |Formato esperado do valor de source. Para formatos com suporte, consulte [/dotnet/Standard/base-Types/Custom-Date-and-Time-Format-Strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |
| **outputFormat** |Obrigatório |Cadeia de caracteres |Formato da data de saída. |

---
### <a name="guid"></a>Guid
**Função:** GUID ()

**Descrição:** O GUID da função gera um novo GUID aleatório

---
### <a name="iif"></a>IIF
**Função:** IIF (condição, valueIfTrue, valueIfFalse)

**Descrição:** A função IIF retorna um de um conjunto de valores possíveis com base em uma condição especificada.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **problema** |Obrigatório |Variável ou expressão |Qualquer valor ou expressão que possa ser avaliada como verdadeira ou falsa. |
| **valueIfTrue** |Obrigatório |Variável ou cadeia de caracteres | se a condição for avaliada como true, o valor será retornado. |
| **valueIfFalse** |Obrigatório |Variável ou cadeia de caracteres |se a condição for avaliada como false, o valor será retornado.|

**Exemplo**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**Função:** InStr (value1, value2, Start, comparetype)

**Descrição:** A função InStr localiza a primeira ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **value1** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres a ser pesquisada |
| **value2** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres a ser encontrada |
| **start** |Opcional |Inteiro |Posição inicial para localizar a subcadeia de caracteres|
| **comparetype** |Opcional |Enumeração |Pode ser vbTextCompare ou vbBinaryCompare |

**Exemplo**
`InStr("The quick brown fox","quick")`

Avalia para 5

`InStr("repEated","e",3,vbBinaryCompare)`

 é avaliado como 7

---
### <a name="isnull"></a>IsNull
**Função:** IsNull (expressão)

**Descrição:** Se a expressão for avaliada como NULL, a função IsNull retornará true.  para um atributo, um valor Null é expresso pela ausência do atributo.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo**
`IsNull([displayName])`

Retornará true se o atributo não estiver presente.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Função:** IsNullOrEmpty (expressão)

**Descrição:** Se a expressão for nula ou uma cadeia de caracteres vazia, a função IsNullOrEmpty retornará true.  para um atributo, isso seria avaliado como True se o atributo estivesse ausente ou presente, mas fosse uma cadeia de caracteres vazia.
O inverso dessa função é chamado de IsPresent.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo**
`IsNullOrEmpty([displayName])`

Retornará true se o atributo não estiver presente ou for uma cadeia de caracteres vazia.

---
### <a name="ispresent"></a>IsPresent
**Função:** IsPresent (expressão)

**Descrição:** Se a expressão for avaliada como uma cadeia de caracteres que não é nula e não estiver vazia, a função IsPresent retornará true.  o inverso dessa função é chamado de IsNullOrEmpty.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

**Exemplo**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**Função:** IsString (expressão)

**Descrição:** Se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função isString será avaliada como true.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **expressão** |Obrigatório |expressão |Expressão a ser avaliada |

---
### <a name="item"></a>Item
**Função:** Item (atributo, índice)

**Descrição:** A função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **Attribute** |Obrigatório |Atributo |Atributo de valores múltiplos a ser pesquisado |
| **index** |Necessária |Inteiro | Índice para um item na cadeia de caracteres de valores múltiplos|

**Exemplo:** 
 `Item([proxyAddresses], 1)` Retorna o segundo item no atributo de valores múltiplos.

---
### <a name="join"></a>Join
**Função:** Junção (separador, origem1, origem2,...)

**Descrição:** Join () é semelhante a Append (), exceto pelo fato de que ele pode combinar vários valores de cadeia de caracteres de **origem** em uma única cadeia de caracteres, e cada valor será separado por uma cadeia de caracteres **separadora** .

Se um dos valores de source for um atributo com vários valores, todos os valores nesse atributo serão unidos, separados pelo valor do separador.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **separator** |Obrigatório |Cadeia de caracteres |Cadeia de caracteres usada para separar os valores de source quando eles são concatenados em uma cadeia de caracteres. Pode ser "" se não for necessário nenhum separador. |
| **source1  … sourceN** |Obrigatório, número de vezes variável |Cadeia de caracteres |Valores de cadeia de caracteres a serem unidos. |

---
### <a name="left"></a>Esquerda
**Função:** Left (cadeia de caracteres, NumChars)

**Descrição:** A função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres. Se numChars = 0, retorne a cadeia de caracteres vazia.
Se numChars < 0, retorne a cadeia de caracteres de entrada.
Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.
Se a cadeia de caracteres contiver menos caracteres que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia (ou seja, que contém todos os caracteres no parâmetro 1) será retornada.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **Cadeia de caracteres** |Obrigatório |Atributo | A cadeia de caracteres da qual retornar os caracteres |
| **NumChars** |Necessária |Inteiro | Um número que identifica o número de caracteres a serem retornados do início (à esquerda) da cadeia de caracteres|

**Exemplo**
`Left("John Doe", 3)`

 retorna "Joh".

---
### <a name="mid"></a>Mid
**Função:** Mid (origem, início, comprimento)

**Descrição:** Retorna uma subcadeia de caracteres do valor de origem. Uma subcadeia de caracteres é uma cadeia de caracteres que contém apenas alguns dos caracteres da cadeia de caracteres de source.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Geralmente o nome do atributo. |
| **start** |Obrigatório |Número inteiro |Índice na cadeia de caracteres de **origem** em que a subcadeia deve iniciar. O primeiro caractere na cadeia de caracteres terá o índice de 1, o segundo caractere terá o índice 2 e assim por diante. |
| **length** |Obrigatório |Número inteiro |Comprimento da subcadeia de caracteres. Se o comprimento terminar fora da cadeia de caracteres **source**, a função retornará uma subcadeia de caracteres do índice **start** até o final da cadeia de caracteres **source**. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Função:** NormalizeDiacritics (origem)

**Descrição:** Requer um argumento de cadeia de caracteres. Retorna a cadeia de caracteres, mas com nenhum dos caracteres diacríticos substituídos por caracteres não diacríticas equivalentes. Normalmente usado para converter os nomes e sobrenomes que contêm caracteres diacríticos (acentos) em valores legais que podem ser usados em vários identificadores de usuário, como nomes de entidade de segurança do usuário, nomes de conta SAM e endereços de email.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres | Geralmente um atributo de nome ou sobrenome. |

---
### <a name="not"></a>Not
**Função:** Não (origem)

**Descrição:** Inverte o valor booliano da **origem**. Se o valor de **Source** for true, retornará false. Caso contrário, retornará true.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres booliana |Valores da **origem** esperados são "True" ou "False". |

---
### <a name="numfromdate"></a>NumFromDate
**Função:** NumFromDate (valor)

**Descrição:** A função NumFromDate converte um valor DateTime em Active Directory formato necessário para definir atributos como [accountExpires](/windows/win32/adschema/a-accountexpires). Use essa função para converter valores de data e hora recebidos de aplicativos de RH de nuvem, como workday e SuccessFactors, para sua representação de AD equivalente. 

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **value** |Obrigatório | Cadeia de caracteres | Cadeia de caracteres de data e hora no formato com suporte. Para formatos com suporte, consulte https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Exemplo:**
* Exemplo de workday supondo que você deseja mapear o atributo *ContractEndDate* do workday, que está no formato *2020-12-31-08:00* para o campo *accountExpires* no AD, aqui está como você pode usar essa função e alterar o deslocamento do fuso horário para corresponder à sua localidade. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors exemplo supondo que você deseja mapear o atributo *EndDate* de SuccessFactors que está no formato *M/d/aaaa hh: mm: SS TT* para *accountExpires* no AD, aqui está como você pode usar essa função e alterar o deslocamento de fuso horário para corresponder à sua localidade.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Função:** RemoveDuplicates (atributo)

**Descrição:** A função RemoveDuplicates usa uma cadeia de caracteres com valores múltiplos e garante que cada valor seja exclusivo.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **Attribute** |Obrigatório |Atributo de valores múltiplos |Atributo com valores múltiplos que terá duplicatas removidas|

**Exemplo:** 
 `RemoveDuplicates([proxyAddresses])` Retorna um atributo proxyAddress limpo em que todos os valores duplicados foram removidos.

---
### <a name="replace"></a>Substitua
**Função:** Replace (origem, oldValue, regexPattern, regexGroupName, replacevalue, replacementAttributeName, modelo)

**Descrição:** Substitui valores dentro de uma cadeia de caracteres de maneira sensível a maiúsculas e minúsculas. A função se comporta de maneira diferente dependendo dos parâmetros fornecidos:

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

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de **origem** . |
| **oldValue** |Opcional |Cadeia de caracteres |Valor a ser substituído em **source** ou **template**. |
| **regexPattern** |Opcional |Cadeia de caracteres |Padrão Regex para o valor a ser substituído na **origem**. Ou, quando **replacementPropertyName** for usado, o padrão para extrair o valor de **replacementPropertyName**. |
| **regexGroupName** |Opcional |Cadeia de caracteres |Nome do grupo dentro de **regexPattern**. Somente quando **replacementPropertyName** for usado, extrairemos o valor desse grupo como **replacementValue** de **replacementPropertyName**. |
| **replacementValue** |Opcional |Cadeia de caracteres |Novo valor com o qual substituir um antigo. |
| **replacementAttributeName** |Opcional |Cadeia de caracteres |Nome do atributo a ser usado para o valor de substituição |
| **modelos** |Opcional |Cadeia de caracteres |Quando o valor do **modelo** for fornecido, procuraremos **OldValue** dentro do modelo e o substituíremos pelo valor de **origem** . |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Função:** SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Descrição:** Requer um mínimo de dois argumentos, que são regras exclusivas de geração de valor definidas usando expressões. A função avalia cada regra e, em seguida, verifica o valor gerado para exclusividade no aplicativo/diretório de destino. O primeiro valor exclusivo encontrado será retornado o um. Se todos os valores já existem no destino, a entrada será obter mantida em garantia e o motivo pelo qual obtém registrado nos logs de auditoria. Não há nenhum limite superior para o número de argumentos que podem ser fornecidos.


 - Essa é uma função de nível superior, ele não pode ser aninhado.
 - Esta função não pode ser aplicada a atributos que têm uma precedência correspondente.   
 - Essa função destina-se somente a ser usado para criações de entrada. Ao usá-lo com um atributo, defina a **Aplicar mapeamento** propriedade **somente durante a criação do objeto**.
 - Atualmente, essa função só tem suporte para "WORKDAY para Active Directory provisionamento de usuário" e "SuccessFactors para Active Directory provisionamento de usuário". Ele não pode ser usado com outros aplicativos de provisionamento. 


**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |Pelo menos 2 são necessários, sem limite superior |Cadeia de caracteres | Lista de regras de geração de valor exclusivo para avaliar. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Função:** SingleAppRoleAssignment ([appRoleAssignments])

**Descrição:** Retorna um único appRoleAssignment da lista de todos os appRoleAssignments atribuídos a um usuário para um determinado aplicativo. Essa função é necessária para converter o objeto appRoleAssignments em uma cadeia de caracteres de nome de função única. Observe que a prática recomendada é garantir que apenas um appRoleAssignment seja atribuído por usuário por vez, e se várias funções forem atribuídas a cadeia de caracteres de função retornada pode não ser previsível. 

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Obrigatório |Cadeia de caracteres |Objeto **[appRoleAssignments]**. |

---
### <a name="split"></a>Divisão
**Função:** Divisão (origem, delimitador)

**Descrição:** Divide uma cadeia de caracteres em uma matriz de valores múltiplos, usando o caractere delimitador especificado.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Valor da **origem** para atualizar. |
| **delimitador** |Obrigatório |Cadeia de caracteres |Especifica o caractere que será usado para dividir a cadeia de caracteres (exemplo: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Função:** StripSpaces (origem)

**Descrição:** Remove todos os caracteres de espaço ("") da cadeia de caracteres de origem.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Valor da **origem** para atualizar. |

---
### <a name="switch"></a>Comutador
**Função:** Switch (Source, defaultValue, key1, value1, Key2, value2,...)

**Descrição:** Quando o valor de **origem** corresponde a uma **chave**, retorna um **valor** para essa **chave**. Se o valor da **origem** não corresponder a nenhuma chave, retorna **defaultValue**.  Os parâmetros **key** e **value** devem sempre ocorrer em pares. A função sempre espera um número par de parâmetros. A função não deve ser usada para atributos referenciais como Manager. 

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |**Source** a atualizar. |
| **defaultValue** |Opcional |Cadeia de caracteres |Valor padrão a ser usado quando source não corresponde a nenhum parâmetro. Pode ser uma cadeia de caracteres vazia (""). |
| **chave** |Obrigatório |Cadeia de caracteres |Parâmetro **key** com o qual comparar o valor de **source**. |
| **value** |Obrigatório |Cadeia de caracteres |Valor de substituição para o **source** que corresponde ao parâmetro key. |

---
### <a name="tolower"></a>ToLower
**Função:** ToLower (origem, cultura)

**Descrição:** Usa um valor de cadeia de caracteres de *origem* e o converte em letras minúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source |
| **cultura** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---
### <a name="toupper"></a>ToUpper
**Função:** ToUpper (origem, cultura)

**Descrição:** Usa um valor de cadeia de caracteres de *origem* e converte-o em maiúsculas usando as regras de cultura especificadas. Se não houver nenhuma informação de *cultura* especificada, ela usará a Cultura invariável.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **source** |Obrigatório |Cadeia de caracteres |Normalmente o nome do atributo do objeto de source. |
| **cultura** |Opcional |Cadeia de caracteres |O formato para o nome da cultura com base em RFC 4646 é *languagecode2-country/regioncode2*, em que *regioncode2* é o código de idioma de duas letras e *country/regioncode2* é o código de subcultura de duas letras. Exemplos incluem ja-JP para japonês (Japão) e en-US para inglês (Estados Unidos). Quando não há um código de idioma de duas letras disponível, um código de três letras derivado da ISO 639-2 é usado.|

---
### <a name="word"></a>Word
**Função:** Word (cadeia de caracteres, WordNumber, delimitadores)

**Descrição:** A função Word retorna uma palavra contida em uma cadeia de caracteres, com base nos parâmetros que descrevem os delimitadores a serem usados e o número da palavra a ser retornado.  cada cadeia de caracteres separada por um dos caracteres delimitadores na cadeia de caracteres é identificada como palavra:

Se number < 1, retorna uma cadeia de caracteres vazia.
Se a cadeia de caracteres for nula, retorna a cadeia de caracteres vazia.
Se a cadeia de caracteres for menor que o número de palavras ou a cadeia não contiver nenhuma palavra identificada por delimitadores, uma cadeia de caracteres vazia será retornada.

**Parâmetro** 

| Nome | Obrigatório/repetição | Type | Observações |
| --- | --- | --- | --- |
| **Cadeia de caracteres** |Obrigatório |Atributo de valores múltiplos |Cadeia de caracteres da qual retornar uma palavra.|
| **WordNumber** |Necessária | Inteiro | Número que identifica qual número de palavras deve retornar|
| **delimitadores** |Obrigatório |Cadeia de caracteres| Uma cadeia de caracteres que representa os delimitadores que devem ser usados para identificar palavras|

**Exemplo**
`Word("The quick brown fox",3," ")`

Retorna "Brown".

`Word("This,string!has&many separators",3,",!&#")`

Retorna "tem".

---

## <a name="examples"></a>Exemplos
### <a name="strip-known-domain-name"></a>Retirar o nome de domínio conhecido
Você precisa retirar um nome de domínio conhecido do email de um usuário para obter um nome de usuário.  Por exemplo, se o domínio for "contoso.com", você pode usar a seguinte expressão:

**Expressão** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**Entrada/saída de exemplo:** 

* **INPUT** (mail): "john.doe@contoso.com"
* **SAÍDA**: "davi.barros"

### <a name="append-constant-suffix-to-user-name"></a>Acrescentar sufixo constante ao nome de usuário
Se você estiver usando um Salesforce Sandbox, talvez seja necessário acrescentar um sufixo adicional a todos os nomes de usuário antes de sincronizá-los.

**Expressão** 
`Append([userPrincipalName], ".test")`

**Entrada/saída de exemplo:** 

* **INPUT**: (userPrincipalName): "John.Doe@contoso.com"
* **Saída**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Gerar o alias de usuário concatenando partes do nome e do sobrenome
Você precisa gerar um alias de usuário selecionando as três primeiras letras do nome do usuário e as cinco primeiras letras do sobrenome do usuário.

**Expressão** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Entrada/saída de exemplo:** 

* **ENTRADA** (givenName): "Davi"
* **ENTRADA** (sobrenome): "Barros"
* **Saída**: "davibarros"

### <a name="remove-diacritics-from-a-string"></a>Remover diacríticos de uma cadeia de caracteres
Você precisa substituir caracteres que contenham os acentos por caracteres equivalentes que não contenham acentos.

**Expressão:** NormalizeDiacritics ([excertoname])

**Entrada/saída de exemplo:** 

* **INPUT** (givenName): "Zoë"
* **SAÍDA**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Dividir uma cadeia de caracteres em uma matriz de vários valores
É necessário usar uma lista de cadeias de caracteres delimitada por vírgula e dividi-las em uma matriz que possa ser conectada a um atributo com vários valores, como o atributo PermissionSets do Salesforce. Neste exemplo, uma lista de conjuntos de permissões foi preenchida em extensionAttribute5 no Azure AD.

**Expressão:** Split ([extensionAttribute5], ",")

**Entrada/saída de exemplo:** 

* **Entrada** (extensionAttribute5): "PermissionSetOne, PermissionSetTwo"
* **SAÍDA**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Gerar data como uma cadeia de caracteres em um determinado formato
Você deseja enviar datas para um aplicativo SaaS em um determinado formato.  Por exemplo, você deseja formatar datas para o ServiceNow.

**Expressão** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Entrada/saída de exemplo:**

* **ENTRADA** (extensionAttribute1): "20150123105347.1Z"
* **Saída**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Substituir um valor com base em um conjunto predefinido de opções

Você precisa definir o fuso horário do usuário com base no código de estado armazenado no AD do Azure.  Se o código de estado não corresponder a nenhuma das opções predefinidas, use o valor padrão de "Australia/Sydney".

**Expressão** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Entrada/saída de exemplo:**

* **ENTRADA** (estado): "QLD"
* **SAÍDA**: "Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Substituir caracteres usando uma expressão regular
É necessário localizar caracteres que correspondam a um valor de expressão regular e removê-los.

**Expressão** 

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

**Expressão** 

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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>O valor do fluxo de email se não for nulo; caso contrário, flua userPrincipalName
Você deseja fluir o atributo mail se ele estiver presente. Se não for, você deseja fluir o valor de userPrincipalName em vez disso.

**Expressão** 
`Coalesce([mail],[userPrincipalName])`

**Entrada/saída de exemplo:** 

* **Entrada** (email): nulo
* **Entrada** (userPrincipalName): " John.Doe@contoso.com "
* **Saída**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Artigos relacionados
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](../app-provisioning/user-provisioning.md)
* [Personalizando mapeamentos de atributo para provisionamento de usuário](../app-provisioning/customize-application-attributes.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Notificações de provisionamento de conta](../app-provisioning/user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
