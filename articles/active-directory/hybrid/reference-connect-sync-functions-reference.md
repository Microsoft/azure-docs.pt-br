---
title: 'Sincronização do Azure AD Connect: Referências de funções | Microsoft Docs'
description: Referência de expressões de provisionamento declarativo na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b21c5f8630598a4b7117d23ad7c8da46de07d2fa
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204492"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Sincronização do Azure AD Connect: Referência de funções
No Azure Active Directory Sync, as funções são usadas para manipular um valor de atributo durante a sincronização.  
A sintaxe das funções é expressa usando o seguinte formato:  
`<output type> FunctionName(<input type> <position name>, ..)`

Se uma função está sobrecarregada e aceita diversas sintaxes, todas as sintaxes válidas são listadas.  
As funções são fortemente tipadas e verificam que o tipo passado corresponde ao tipo documentado.  
Se o tipo não corresponder, um erro será gerado.

Os tipos são expressos com a seguinte sintaxe:

* **bin** - binário
* **bool** - booliano
* **dt** - data/hora UTC
* **enum** - enumeração das constantes conhecidas
* **exp** - expressão, que espera-se que seja avaliada como um valor Booliano
* **mvbin** - Binário de Valores Múltiplos
* **mvstr** - Cadeia de Caracteres de Valores Múltiplos
* **mvstr** - Referência de Valores Múltiplos
* **num** - numérico
* **ref** – Referência
* **str** – Cadeia de Caracteres
* **var** - uma variante de (quase) qualquer outro tipo
* **void** - não retorna um valor

As funções com os tipos **mvbin**, **mvstr** e **mvref** funcionam somente nos atributos de valores múltiplos. As funções com **bin**, **str** e **ref** funcionam nos atributos de valor único e valores múltiplos.

## <a name="functions-reference"></a>Referência de funções

| Lista de funções |  |  |  |  |
| --- | --- | --- | --- | --- |
| **Certificate** | | | | |
| [CertExtensionOids](#certextensionoids) |[CertFormat](#certformat) |[CertFriendlyName](#certfriendlyname) |[CertHashString](#certhashstring) | |
| [CertIssuer](#certissuer) |[CertIssuerDN](#certissuerdn) |[CertIssuerOid](#certissueroid) |[CertKeyAlgorithm](#certkeyalgorithm) | |
| [CertKeyAlgorithmParams](#certkeyalgorithmparams) |[CertNameInfo](#certnameinfo) |[CertNotAfter](#certnotafter) |[CertNotBefore](#certnotbefore) | |
| [CertPublicKeyOid](#certpublickeyoid) |[CertPublicKeyParametersOid](#certpublickeyparametersoid) |[CertSerialNumber](#certserialnumber) |[CertSignatureAlgorithmOid](#certsignaturealgorithmoid) | |
| [CertSubject](#certsubject) |[CertSubjectNameDN](#certsubjectnamedn) |[CertSubjectNameOid](#certsubjectnameoid) |[CertThumbprint](#certthumbprint) | |
[CertVersion](#certversion) |[IsCert](#iscert) | | | |
| **Conversão** | | | | |
| [CBool](#cbool) |[CDate](#cdate) |[CGuid](#cguid) |[ConvertFromBase64](#convertfrombase64) | |
| [ConvertToBase64](#converttobase64) |[ConvertFromUTF8Hex](#convertfromutf8hex) |[ConvertToUTF8Hex](#converttoutf8hex) |[CNum](#cnum) | |
| [CRef](#cref) |[CStr](#cstr) |[StringFromGuid](#stringfromguid) |[StringFromSid](#stringfromsid) | |
| **Data / hora** | | | | |
| [DateAdd](#dateadd) |[DateFromNum](#datefromnum) |[FormatDateTime](#formatdatetime) |[Now](#now) | |
| [NumFromDate](#numfromdate) | | | | |
| **Diretório** | | | | |
| [DNComponent](#dncomponent) |[DNComponentRev](#dncomponentrev) |[EscapeDNComponent](#escapedncomponent) | | |
| **Avaliação** | | | | |
| [IsBitSet](#isbitset) |[IsDate](#isdate) |[IsEmpty](#isempty) |[IsGuid](#isguid) | |
| [IsNull](#isnull) |[IsNullOrEmpty](#isnullorempty) |[IsNumeric](#isnumeric) |[IsPresent](#ispresent) | |
| [IsString](#isstring) | | | | |
| **Matemática** | | | | |
| [BitAnd](#bitand) |[BitOr](#bitor) |[RandomNum](#randomnum) | | |
| **De valores múltiplos** | | | | |
| [Contém:](#contains) |[Contagem](#count) |[Item](#item) |[ItemOrNull](#itemornull) | |
| [Join](#join) |[RemoveDuplicates](#removeduplicates) |[Divisão](#split) | | |
| **Fluxo do Programa** | | | | |
| [Erro](#error) |[IIF](#iif) |[Seleção](#select) |[Switch](#switch) | |
| [Onde](#where) |[With](#with) | | | |
| **Texto** | | | | |
| [GUID](#guid) |[InStr](#instr) |[InStrRev](#instrrev) |[LCase](#lcase) | |
| [Left](#left) |[Len](#len) |[LTrim](#ltrim) |[Mid](#mid) | |
| [PadLeft](#padleft) |[PadRight](#padright) |[PCase](#pcase) |[Substitua](#replace) | |
| [ReplaceChars](#replacechars) |[Right](#right) |[RTrim](#rtrim) |[Trim](#trim) | |
| [UCase](#ucase) |[Word](#word) | | | |

---
### <a name="bitand"></a>BitAnd
**Descrição:**  
a função BitAnd define os bits especificados em um valor.

**Sintaxe:**  
`num BitAnd(num value1, num value2)`

* value1, value2: os valores numéricos que devem ser agrupados com AND

**Comentários:**  
esta função converte ambos os parâmetros na representação binária e define um bit para:

* 0 - se um ou ambos os bits correspondentes na *máscara* e no *sinalizador* forem 0
* 1 - se ambos os bits correspondentes são 1.

Em outras palavras, ele retorna 0 em todos os casos, exceto quando os bits correspondentes de ambos os parâmetros são 1.

**Exemplo:**  
`BitAnd(&HF, &HF7)`  
Retorna 7, já que os hexadecimais "F" AND "F7" são avaliados como esse valor.

---
### <a name="bitor"></a>BitOr
**Descrição:**  
a função BitOr define os bits especificados em um valor.

**Sintaxe:**  
`num BitOr(num value1, num value2)`

* value1, value2: valores numéricos que devem ser agrupados com OR

**Comentários:**  
esta função converte ambos os parâmetros na representação binária e define um bit para 1 se um ou ambos os bits correspondentes na máscara e no sinalizador são 1; e para 0 se ambos os bits correspondentes são 0. Em outras palavras, ele retorna 1 em todos os casos, exceto naqueles em que os bits correspondentes de ambos os parâmetros são 0.

---
### <a name="cbool"></a>CBool
**Descrição:**  
a função CBool retorna um valor booliano com base na expressão avaliada

**Sintaxe:**  
`bool CBool(exp Expression)`

**Comentários:**  
se a expressão é avaliada como um valor diferente de zero, CBool retorna True; caso contrário, retorna False.

**Exemplo:**  
`CBool([attrib1] = [attrib2])`  

Retorna True se ambos os atributos têm o mesmo valor.

---
### <a name="cdate"></a>CDate
**Descrição:**  
a função CDate retorna um DateTime UTC a partir de uma cadeia de caracteres. DateTime não é um tipo de atributo nativo no Sync, mas é usado por algumas funções.

**Sintaxe:**  
`dt CDate(str value)`

* Valor: uma cadeia de caracteres com uma data, hora e opcionalmente um fuso horário

**Comentários:**  
a cadeia de caracteres retornada é sempre em UTC.

**Exemplo:**  
`CDate([employeeStartTime])`  
retorna um DateTime com base na hora de início do funcionário

`CDate("2013-01-10 4:00 PM -8")`  
Retorna um DateTime que representa "11/01/2013 12:00"


---
### <a name="certextensionoids"></a>CertExtensionOids
**Descrição:**  
Retorna os valores de OID de todas as extensões críticas de um objeto de certificado.

**Sintaxe:**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certformat"></a>CertFormat
**Descrição:**  
Retorna o nome do formato desse certificado X.509v3.

**Sintaxe:**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Descrição:**  
Retorna o alias associado de um certificado.

**Sintaxe:**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certhashstring"></a>CertHashString
**Descrição:**  
Retorna o valor de hash SHA1 do certificado X.509v3 como uma cadeia de caracteres hexadecimal.

**Sintaxe:**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certissuer"></a>CertIssuer
**Descrição:**  
Retorna o nome da autoridade de certificação que emitiu o certificado X.509v3.

**Sintaxe:**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Descrição:**  
Retorna o nome diferenciado do emissor do certificado.

**Sintaxe:**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certissueroid"></a>CertIssuerOid
**Descrição:**  
Retorna o OID do emissor do certificado.

**Sintaxe:**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Descrição:**  
Retorna as informações de algoritmo de chave do certificado X.509v3 como uma cadeia de caracteres.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Descrição:**  
Retorna os parâmetros de algoritmo de chave do certificado X.509v3 como uma cadeia de caracteres hexadecimal.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certnameinfo"></a>CertNameInfo
**Descrição:**  
Retorna os nomes de entidade e emissor de um certificado.

**Sintaxe:**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.
*   X509NameType: o valor de X509NameType para a entidade.
*   includesIssuerName: verdadeiro para incluir o nome do emissor; caso contrário, falso.

---
### <a name="certnotafter"></a>CertNotAfter
**Descrição:**  
Retorna a data, em hora local, depois da qual um certificado não será mais válido.

**Sintaxe:**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certnotbefore"></a>CertNotBefore
**Descrição:**  
Retorna a data, em hora local, na qual um certificado se torna válido.

**Sintaxe:**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Descrição:**  
Retorna o OID da chave pública do certificado X.509v3.

**Sintaxe:**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Descrição:**  
Retorna o OID dos parâmetros de chave pública do certificado X.509v3.

**Sintaxe:**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Descrição:**  
Retorna o número de série do certificado X.509v3.

**Sintaxe:**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Descrição:**  
Retorna o OID do algoritmo usado para criar a assinatura de um certificado.

**Sintaxe:**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certsubject"></a>CertSubject
**Descrição:**  
Obtém o nome diferenciado da entidade de um certificado.

**Sintaxe:**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Descrição:**  
Retorna o nome diferenciado da entidade de um certificado.

**Sintaxe:**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Descrição:**  
Retorna o OID do nome da entidade de um certificado.

**Sintaxe:**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certthumbprint"></a>CertThumbprint
**Descrição:**  
Retorna a impressão digital de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="certversion"></a>CertVersion
**Descrição:**  
Retorna a versão do formato X.509 de um certificado.

**Sintaxe:**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.

---
### <a name="cguid"></a>CGuid
**Descrição:**  
a função CGuid converte a representação da cadeia de caracteres de um GUID em sua representação binária.

**Sintaxe:**  
`bin CGuid(str GUID)`

* Uma cadeia de caracteres formatada nesse padrão: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

---
### <a name="contains"></a>Contém:
**Descrição:**  
a função Contains localiza uma cadeia de caracteres dentro de um atributo de valores múltiplos

**Sintaxe:**  
`num Contains (mvstring attribute, str search)` - diferencia letras maiúsculas de minúsculas  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` - diferencia letras maiúsculas de minúsculas

* attribute: o atributo de valores múltiplos para a pesquisa.
* search: a cadeia de caracteres a localizar no atributo.
* Casetype: CaseInsensitive ou CaseSensitive.

Retorna o índice no atributo com vários valores em que a cadeia de caracteres foi encontrada. Se a cadeia de caracteres não for encontrada, 0 será retornado.

**Comentários:**  
para os atributos da cadeia de caracteres de valores múltiplos, a pesquisa encontra as subcadeias nos valores.  
Para atributos de referência, a cadeia de caracteres pesquisada deve corresponder exatamente ao valor para que sejam considerados como uma correspondência.

**Exemplo:**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
se o atributo proxyAddresses tiver um endereço de email principal (indicado pelas letras maiúsculas "SMTP:"), o atributo proxyAddress será retornado; caso contrário, um erro será retornado.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Descrição:**  
a função ConvertFromBase64 converte o valor codificado base64 especificado em uma cadeia de caracteres regular.

**Sintaxe:**  
`str ConvertFromBase64(str source)` - adota Unicode para codificação  
`str ConvertFromBase64(str source, enum Encoding)`

* source: Cadeia codificada em Base64  
* Codificação: Unicode, ASCII, UTF8

**Exemplo**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Ambos os exemplos retornam "*Hello world!* "

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Descrição:**  
a função ConvertFromUTF8Hex converte o valor codificado em UTF8 hexadecimal especificado em uma cadeia de caracteres.

**Sintaxe:**  
`str ConvertFromUTF8Hex(str source)`

* source: cadeia de caracteres codificada em UTF8 de 2 bytes

**Comentários:**  
A diferença entre essa função e ConvertFromBase64([],UTF8) é que o resultado é amigável para o atributo DN.  
Esse formato é usado pelo Active Directory do Azure como DN.

**Exemplo:**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
retorna "*Hello world!* "

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

**Comentários:**  
o formato de saída dessa função é usado pelo Azure Active Directory como o formato do atributo DN.

**Exemplo:**  
`ConvertToUTF8Hex("Hello world!")`  
retorna 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**Descrição:**  
a função Count retorna o número de elementos em um atributo de valores múltiplos

**Sintaxe:**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Descrição:**  
a função CNum obtém uma cadeia de caracteres e retorna um tipo de dados numérico.

**Sintaxe:**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Descrição:**  
converte uma cadeia de caracteres em um atributo de referência

**Sintaxe:**  
`ref CRef(str value)`

**Exemplo:**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

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
### <a name="dateadd"></a>DateAdd
**Descrição:**  
retorna um Date contendo uma data à qual um intervalo de tempo especificado foi adicionado.

**Sintaxe:**  
`dt DateAdd(str interval, num value, dt date)`

* interval: expressão de cadeia de caracteres que é o intervalo de tempo que você deseja adicionar. A cadeia de caracteres deve ter um dos seguintes valores:
  * aaaa Ano
  * q - Trimestre
  * m - Mês
  * y - Dia do ano
  * d - Dia
  * w - Dia da semana
  * ww - Semana
  * h - Hora
  * m - Minuto
  * s - Segundo
* valor: o número de unidades que você deseja adicionar. Ele pode ser positivo (para obter datas no futuro) ou negativo (para obter datas no passado).
* date: DateTime, representando a data à qual o intervalo é adicionado.

**Exemplo:**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
adiciona três meses e retorna um DateTime que representa "01/04/2001".

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
### <a name="dncomponentrev"></a>DNComponentRev
**Descrição:**  
a função DNComponentRev retorna o valor de um componente DN especificado saindo da direita (o final).

**Sintaxe:**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* dn: o atributo de referência a interpretar
* ComponentNumber - o componente no DN a retornar
* Opções: DC – ignorar todos os componentes com "dc="

**Exemplo:**  
Se dn for "cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com" então  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
ambos retornam US.

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
### <a name="escapedncomponent"></a>EscapeDNComponent
**Descrição:**  
a função EscapeDNComponent obtém um componente de um DN e aplica o escape para que ele possa ser representado no LDAP.

**Sintaxe:**  
`str EscapeDNComponent(str value)`

**Exemplo:**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
garante que o objeto possa ser criado em um diretório LDAP, mesmo que o atributo displayName tenha caracteres que devam ter o escape aplicado no LDAP.

---
### <a name="formatdatetime"></a>FormatDateTime
**Descrição:**  
a função FormatDateTime é usada para formatar um DateTime para uma cadeia de caracteres com um formato especificado

**Sintaxe:**  
`str FormatDateTime(dt value, str format)`

* value: um valor no formato DateTime
* format: uma cadeia de caracteres que representa o formato para o qual converter.

**Comentários:**  
os valores possíveis para o formato podem ser encontrados aqui: [Formatos de data e hora personalizados para a função FORMAT](https://docs.microsoft.com/dax/custom-date-and-time-formats-for-the-format-function).

**Exemplo:**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-mm-dd")`  
resulta em "25/12/2007".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
pode resultar em "20140905081453.0Z"

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
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: cadeia de caracteres a ser pesquisada
* stringmatch: cadeia de caracteres a ser localizada
* start: posição inicial para se localizar a subcadeia de caracteres
* compare: vbTextCompare ou vbBinaryCompare

**Comentários:**  
retorna a posição onde a subcadeia de caracteres foi encontrada ou 0, se não foi encontrada.

**Exemplo:**  
`InStr("The quick brown fox","quick")`  
é avaliado como 5

`InStr("repEated","e",3,vbBinaryCompare)`  
é avaliado como 7

---
### <a name="instrrev"></a>InStrRev
**Descrição:**  
a função InStrRev localiza a última ocorrência de uma subcadeia de caracteres em uma cadeia de caracteres

**Sintaxe:**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* stringcheck: cadeia de caracteres a ser pesquisada
* stringmatch: cadeia de caracteres a ser localizada
* start: posição inicial para se localizar a subcadeia de caracteres
* compare: vbTextCompare ou vbBinaryCompare

**Comentários:**  
retorna a posição onde a subcadeia de caracteres foi encontrada ou 0, se não foi encontrada.

**Exemplo:**  
`InStrRev("abbcdbbbef","bb")`  
retorna 7

---
### <a name="isbitset"></a>IsBitSet
**Descrição:**  
a função IsBitSet testa se um bit está definido ou não

**Sintaxe:**  
`bool IsBitSet(num value, num flag)`

* value: um valor numérico que é evaluated.flag: um valor numérico que contém o bit a ser avaliado

**Exemplo:**  
`IsBitSet(&HF,4)`  
retorna True porque o bit "4" está definido no valor hexadecimal "F"

---
### <a name="isdate"></a>IsDate
**Descrição:**  
se a expressão puder ser avaliada como um tipo DateTime, a função IsDate será avaliada como True.

**Sintaxe:**  
`bool IsDate(var Expression)`

**Comentários:**  
usada para determinar se CDate() pode ter êxito.

---
### <a name="iscert"></a>IsCert
**Descrição:**  
Retorna verdadeiro se os dados brutos puderem ser serializados no objeto de certificado .NET X509Certificate2.

**Sintaxe:**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: Representação da matriz de bytes de um certificado X.509. A matriz de bytes pode ser codificada binária (DER) ou de dados X.509 codificados em Base64.
---
### <a name="isempty"></a>IsEmpty
**Descrição:**  
se o atributo estiver presente no CS ou no MV, mas for avaliado como uma cadeia de caracteres vazia, a função IsEmpty será avaliada como True.

**Sintaxe:**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Descrição:**  
se a cadeia de caracteres puder ser convertida em um GUID, a função IsGuid será avaliada como true.

**Sintaxe:**  
`bool IsGuid(str GUID)`

**Comentários:**  
um GUID é definido como uma cadeia de caracteres seguindo um destes padrões: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Usada para determinar se CGuid() pode ter êxito.

**Exemplo:**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
se StrAttribute tiver um formato de GUID, retornará uma representação binária; caso contrário, retornará Null.

---
### <a name="isnull"></a>IsNull
**Descrição:**  
se a expressão for avaliada como Null, a função IsNull retornará true.

**Sintaxe:**  
`bool IsNull(var Expression)`

**Comentários:**  
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

**Comentários:**  
para um atributo, isso seria avaliado como True se o atributo estivesse ausente ou presente, mas fosse uma cadeia de caracteres vazia.  
O inverso dessa função é chamado de IsPresent.

**Exemplo:**  
`IsNullOrEmpty([displayName])`  
retornará True se o atributo não estiver presente ou for uma cadeia de caracteres vazia no CS ou no MV.

---
### <a name="isnumeric"></a>IsNumeric
**Descrição:**  
a função IsNumeric retorna um valor booliano que indica se uma expressão pode ser avaliada como um tipo numérico.

**Sintaxe:**  
`bool IsNumeric(var Expression)`

**Comentários:**  
usada para determinar se CNum() pode ter êxito ao analisar a expressão.

---
### <a name="isstring"></a>IsString
**Descrição:**  
se a expressão puder ser avaliada como um tipo de cadeia de caracteres, a função IsString será avaliada como True.

**Sintaxe:**  
`bool IsString(var expression)`

**Comentários:**  
usada para determinar se CStr() pode ter êxito ao analisar a expressão.

---
### <a name="ispresent"></a>IsPresent
**Descrição:**  
se a expressão for avaliada como uma cadeia de caracteres que não é Null nem vazia, a função IsPresent retornará true.

**Sintaxe:**  
`bool IsPresent(var expression)`

**Comentários:**  
o inverso dessa função é chamado de IsNullOrEmpty.

**Exemplo:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>item
**Descrição:**  
a função Item retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe:**  
`var Item(mvstr attribute, num index)`

* attribute: atributo com valores múltiplos
* index: índice para um item na cadeia de caracteres com vários valores.

**Comentários:**  
a função Item é útil com a função Contains, desde que a última função retorne o índice para um item no atributo de valores múltiplos.

Gera um erro se o índice está fora dos limites.

**Exemplo:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
retorna o endereço de email principal.

---
### <a name="itemornull"></a>ItemOrNull
**Descrição:**  
a função ItemOrNull retorna um item de um atributo/cadeia de caracteres de valores múltiplos.

**Sintaxe:**  
`var ItemOrNull(mvstr attribute, num index)`

* attribute: atributo com valores múltiplos
* index: índice para um item na cadeia de caracteres com vários valores.

**Comentários:**  
a função ItemOrNull é útil com a função Contains, desde que a última função retorne o índice para um item no atributo de valores múltiplos.

Se o índice estiver fora dos limites, retornará um valor Null.

---
### <a name="join"></a>Ingressar
**Descrição:**  
a função Join obtém uma cadeia de caracteres de valores múltiplos e retorna uma cadeia de caracteres de um único valor com um separador especificado inserido entre cada item.

**Sintaxe:**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* atributo: um atributo de valores múltiplos contendo cadeias de caracteres a serem unidas.
* delimiter: qualquer cadeia de caracteres usada para separar as subcadeias de caracteres na cadeia de caracteres retornada. Se omitido, o caractere de espaço (" ") é usado. Se o Delimitador é uma cadeia de caracteres de comprimento zero ("") ou Nada, todos os itens na lista são concatenados sem delimitadores.

**Comentários**  
há paridade entre as funções Join e Split. A função Join pega uma matriz de cadeias de caracteres e une-as usando uma cadeia de caracteres do delimitador, para retornar uma única cadeia de caracteres. A função Split pega uma cadeia de caracteres e a separa no delimitador, para retornar uma matriz de cadeias de caracteres. No entanto, uma diferença importante é que a Join pode concatenar cadeias de caracteres com qualquer cadeia de caracteres delimitadora, enquanto Split só pode separar cadeias de caracteres usando um único caractere delimitador.

**Exemplo:**  
`Join([proxyAddresses],",")`  
Poderia retornar: "SMTP:john.doe@contoso.com,smtp:jd@contoso.com"

---
### <a name="lcase"></a>LCase
**Descrição:**  
a função LCase converte todos os caracteres de uma cadeia de caracteres em letras minúsculas.

**Sintaxe:**  
`str LCase(str value)`

**Exemplo:**  
`LCase("TeSt")`  
retorna "test".

---
### <a name="left"></a>Left
**Descrição:**  
a função Left retorna um número especificado de caracteres a partir da esquerda de uma cadeia de caracteres.

**Sintaxe:**  
`str Left(str string, num NumChars)`

* string: a cadeia de caracteres da qual retornar caracteres
* NumChars: um número que identifica o número de caracteres a ser retroando do início (esquerda) da cadeia de caracteres

**Comentários:**  
uma cadeia de caracteres que contém os primeiros caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retorne a cadeia de caracteres vazia.
* Se numChars < 0, retorne a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contiver menos caracteres que o número especificado em numChars, uma cadeia de caracteres idêntica à cadeia (ou seja, que contém todos os caracteres no parâmetro 1) será retornada.

**Exemplo:**  
`Left("John Doe", 3)`  
retorna "Joh".

---
### <a name="len"></a>Len
**Descrição:**  
a função Len retorna o número de caracteres em uma cadeia de caracteres.

**Sintaxe:**  
`num Len(str value)`

**Exemplo:**  
`Len("John Doe")`  
retorna 8

---
### <a name="ltrim"></a>LTrim
**Descrição:**  
a função LTrim remove os espaços em branco à esquerda de uma cadeia de caracteres.

**Sintaxe:**  
`str LTrim(str value)`

**Exemplo:**  
`LTrim(" Test ")`  
retorna "Test"

---
### <a name="mid"></a>Mid
**Descrição:**  
a função Mid retorna um número especificado de caracteres a partir de uma posição especificada em uma cadeia de caracteres.

**Sintaxe:**  
`str Mid(str string, num start, num NumChars)`

* string: a cadeia de caracteres da qual retornar caracteres
* start: um número que identifica a posição inicial na cadeia de caracteres da qual retornar caracteres
* NumChars: um número que identifica o número de caracteres a ser retornado da posição

**Comentários:**  
retorna os caracteres numChars começando na posição inicial da cadeia de caracteres.  
Uma cadeia de caracteres contendo caracteres numChars desde a posição inicial na cadeia de caracteres:

* Se numChars = 0, retorne a cadeia de caracteres vazia.
* Se numChars < 0, retorne a cadeia de caracteres de entrada.
* Se start > o comprimento da cadeia de caracteres, retorne a cadeia de caracteres de entrada.
* Se start <= 0, retorne a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se não houver nenhum caractere numChar restante na cadeia de caracteres a partir da posição inicial, o máximo possível de caracteres será retornado.

**Exemplo:**  
`Mid("John Doe", 3, 5)`  
retorna "hn Do".

`Mid("John Doe", 6, 999)`  
retorna "Doe"

---
### <a name="now"></a>Now
**Descrição:**  
a função Now retorna um DateTime especificando a data e a hora atuais, de acordo com a data e a hora do sistema do seu computador.

**Sintaxe:**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Descrição:**  
a função NumFromDate retorna uma data no formato de data do AD.

**Sintaxe:**  
`num NumFromDate(dt value)`

**Exemplo:**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
retorna 129699324000000000

---
### <a name="padleft"></a>PadLeft
**Descrição:**  
a função PadLeft preenche à esquerda uma cadeia de caracteres até um tamanho especificado usando um caractere de preenchimento fornecido.

**Sintaxe:**  
`str PadLeft(str string, num length, str padCharacter)`

* string: a cadeia de caracteres a preencher.
* length: um inteiro que representa o comprimento da cadeia de caracteres desejado.
* padCharacter: uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários:**

* Se o comprimento da cadeia de caracteres for menor que length, padCharacter será acrescentado repetidamente ao início (esquerda) da cadeia de caracteres até que ela tenha um comprimento igual a length.
* PadCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres é igual ou maior que length, a cadeia de caracteres é retornada inalterada.
* Se a cadeia de caracteres tem um comprimento maior que ou igual a length, uma cadeia de caracteres idêntica à cadeia de caracteres em questão será retornada.
* Se o comprimento da cadeia de caracteres for menor que length, uma nova cadeia de caracteres do comprimento desejado é retornada, contendo a cadeia de caracteres preenchida com um padCharacter.
* Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:**  
`PadLeft("User", 10, "0")`  
retorna "000000User".

---
### <a name="padright"></a>PadRight
**Descrição:**  
a função PadRight preenche à direita uma cadeia de caracteres até um comprimento especificado usando um caractere de preenchimento fornecido.

**Sintaxe:**  
`str PadRight(str string, num length, str padCharacter)`

* string: a cadeia de caracteres a preencher.
* length: um inteiro que representa o comprimento da cadeia de caracteres desejado.
* padCharacter: uma cadeia de caracteres que consiste em um único caractere a ser usado como o caractere de preenchimento

**Comentários:**

* Se o comprimento da cadeia de caracteres for menor que length, padCharacter será acrescentado repetidamente ao final (direita) da cadeia de caracteres até que ela tenha um comprimento igual a length.
* PadCharacter pode ser um caractere de espaço, mas não pode ser um valor nulo.
* Se o comprimento da cadeia de caracteres é igual ou maior que length, a cadeia de caracteres é retornada inalterada.
* Se a cadeia de caracteres tem um comprimento maior que ou igual a length, uma cadeia de caracteres idêntica à cadeia de caracteres em questão será retornada.
* Se o comprimento da cadeia de caracteres for menor que length, uma nova cadeia de caracteres do comprimento desejado é retornada, contendo a cadeia de caracteres preenchida com um padCharacter.
* Se a cadeia de caracteres é nula, a função retorna uma cadeia de caracteres vazia.

**Exemplo:**  
`PadRight("User", 10, "0")`  
retorna "User000000".

---
### <a name="pcase"></a>PCase
**Descrição:**  
a função PCase converte em letras maiúsculas o primeiro caractere de cada palavra delimitada por espaço em uma cadeia de caracteres, enquanto todos os outros caracteres são convertidos em letras minúsculas.

**Sintaxe:**  
`String PCase(string)`

**Comentários:**

* Essa função atualmente não fornece o uso de maiúsculas apropriado para converter uma palavra que está totalmente em letras maiúsculas, como um acrônimo.

**Exemplo:**  
`PCase("TEsT")`  
retorna "test".

`PCase(LCase("TEST"))`  
Retorna "Test"

---
### <a name="randomnum"></a>RandomNum
**Descrição:**  
a função RandomNum retorna um número aleatório em um intervalo especificado.

**Sintaxe:**  
`num RandomNum(num start, num end)`

* start: um número que identifica o limite inferior do valor aleatório a ser gerado
* end: um número que identifica o limite superior do valor aleatório a gerar

**Exemplo:**  
`Random(100,999)`  
pode retornar 734.

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
**Descrição:**  
a função Replace substitui todas as ocorrências de uma cadeia de caracteres por outra cadeia de caracteres.

**Sintaxe:**  
`str Replace(str string, str OldValue, str NewValue)`

* string: uma cadeia de caracteres na qual substituir valores.
* OldValue: a cadeia de caracteres pela qual pesquisar e a qual substituir.
* NewValue: a cadeia de caracteres para substituir.

**Comentários:**  
a função reconhece os seguintes monikers especiais:

* \n - Nova linha
* \r - Retorno de carro
* \t - Guia

**Exemplo:**  
`Replace([address],"\r\n",", ")`  
substitui CRLF por uma vírgula e espaço, e pode levar a "One Microsoft Way, Redmond, WA, USA"

---
### <a name="replacechars"></a>ReplaceChars
**Descrição:**  
a função ReplaceChars substitui todas as ocorrências de caracteres encontradas na cadeia de caracteres ReplacePattern.

**Sintaxe:**  
`str ReplaceChars(str string, str ReplacePattern)`

* string: uma cadeia de caracteres na qual substituir caracteres.
* ReplacePattern: uma cadeia de caracteres que contém um dicionário com caracteres a substituir.

O formato é {origem1}:{destino1},{origem2}:{destino2},{origemN},{destinoN}, em que a origem é o caractere a localizar e destino é a cadeia de caracteres com a qual trabalhar.

**Comentários:**

* A função considera cada ocorrência de origens definidas e as substitui pelos destinos.
* A origem deve ter exatamente um caractere (unicode).
* A origem não pode ser vazia nem maior que um caractere (erro de análise).
* O destino pode ter vários caracteres, por exemplo, ö:oe, β:ss.
* O destino pode ser vazio, o que indica que o caractere deve ser removido.
* A origem diferencia as letras maiúsculas de minúsculas e deve ser uma correspondência exata.
* A , (vírgula) e : (dois-pontos) são caracteres reservados e não podem ser substituídos usando essa função.
* Espaços e outros caracteres em branco na cadeia de caracteres ReplacePattern são ignorados.

**Exemplo:**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
retorna Raksmorgas

`ReplaceChars("O’Neil",%ReplaceString%)`  
retorna "ONeil", o único tique é definido para ser removido.

---
### <a name="right"></a>Right
**Descrição:**  
a função Right retorna um número especificado de caracteres a partir da direita (final) de uma cadeia de caracteres.

**Sintaxe:**  
`str Right(str string, num NumChars)`

* string: a cadeia de caracteres da qual retornar caracteres
* NumChars: um número que identifica o número de caracteres a ser retornado  do final (direita) da cadeia de caracteres

**Comentários:**  
os caracteres de NumChars são retornados a partir da última posição da cadeia de caracteres.

Uma cadeia de caracteres que contém os últimos caracteres numChars na cadeia de caracteres:

* Se numChars = 0, retorne a cadeia de caracteres vazia.
* Se numChars < 0, retorne a cadeia de caracteres de entrada.
* Se a cadeia de caracteres for nula, retorne a cadeia de caracteres vazia.

Se a cadeia de caracteres contém menos caracteres do que o número especificado em NumChars, uma cadeia de caracteres idêntica à cadeia de caracteres será retornada.

**Exemplo:**  
`Right("John Doe", 3)`  
retorna "Doe".

---
### <a name="rtrim"></a>RTrim
**Descrição:**  
a função RTrim remove os espaços em branco à direita de uma cadeia de caracteres.

**Sintaxe:**  
`str RTrim(str value)`

**Exemplo:**  
`RTrim(" Test ")`  
retorna "Test".

---
### <a name="select"></a>Selecionar
**Descrição:**  
Processa todos os valores em um atributo de valores múltiplos (ou a saída de uma expressão) com base na função especificada.

**Sintaxe:**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* item: representa um elemento no atributo de valores múltiplos
* attribute: o atributo de valores múltiplos
* expression: uma expressão que retorna uma coleção de valores
* condition: qualquer função que possa processar um item no atributo

**Exemplos:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
Retorna todos os valores no atributo de valores múltiplos otherPhone depois que os hifens (-) foram removidos.

---
### <a name="split"></a>Divisão
**Descrição:**  
a função Split obtém uma cadeia de caracteres separada por um delimitador e transforma-a em uma cadeia de caracteres de valores múltiplos.

**Sintaxe:**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* valor: a cadeia de caracteres com um caractere delimitador para separar.
* delimitador: um único caractere a ser usado como o delimitador.
* limite: o número máximo de valores que podem ser retornados.

**Exemplo:**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
retorna uma cadeia de caracteres de valores múltiplos com dois elementos úteis para o atributo proxyAddress.

---
### <a name="stringfromguid"></a>StringFromGuid
**Descrição:**  
a função StringFromGuid obtém um GUID binário e converte-o em uma cadeia de caracteres

**Sintaxe:**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Descrição:**  
a função StringFromSid converte uma matriz de bytes, que contém um identificador de segurança, em uma cadeia de caracteres.

**Sintaxe:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Alternar
**Descrição:**  
a função Switch é usada para retornar um único valor com base nas condições avaliadas.

**Sintaxe:**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* expr: expressão variante que você deseja avaliar.
* valor: valor a ser retornado se a expressão correspondente for True.

**Comentários:**  
a lista de argumentos da função Switch consiste em pares de expressões e valores. As expressões são avaliadas da esquerda para a direita e o valor associado à primeira expressão avaliada como True é retornado. Se as partes não tiverem pares adequados, ocorrerá um erro em tempo de execução.

Por exemplo, se expr1 for True, o comutador retornará valor1. Se expr-1 for False, mas expr-2 for True, Switch retorna valor-2 e assim por diante.

Switch retorna um Nothing se:

* Nenhuma das expressões são True.
* A primeira expressão True tem um valor correspondente que é Null.

Switch avalia todas as expressões, mesmo que retorne apenas uma delas. Por essa razão, você deve tomar cuidado com efeitos colaterais indesejáveis. Por exemplo, se a avaliação de qualquer expressão resulta em um erro de divisão por zero, ocorrerá um erro.

O valor também pode ser a função Error, que retornaria uma cadeia de caracteres personalizada.

**Exemplo:**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
retorna o idioma falado em algumas das maiores cidades; caso contrário, retorna um Erro.

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
### <a name="ucase"></a>UCase
**Descrição:**  
a função UCase converte todos os caracteres de uma cadeia de caracteres em letras maiúsculas.

**Sintaxe:**  
`str UCase(str string)`

**Exemplo:**  
`UCase("TeSt")`  
retorna "test".

---
### <a name="where"></a>Where

**Descrição:**  
Retorna um subconjunto de valores de um atributo de valores múltiplos (ou a saída de uma expressão) com base em uma condição específica.

**Sintaxe:**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* item: representa um elemento no atributo de valores múltiplos
* attribute: o atributo de valores múltiplos
* condition: qualquer expressão que possa ser avaliada como verdadeira ou falsa
* expression: uma expressão que retorna uma coleção de valores

**Exemplo:**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
Retorna os valores do certificado no atributo de valores múltiplos userCertificate que não estão expirados.

---
### <a name="with"></a>With
**Descrição:**  
A função With fornece uma maneira para simplificar uma expressão complexa, usando uma variável para representar uma subexpressão que aparece uma ou mais vezes na expressão complexa.

**Sintaxe:** 
`With(var variable, exp subExpression, exp complexExpression)`  
* variable: representa a subexpressão.
* subExpression: a subexpressão representada pela variável.
* complexExpression: uma expressão complexa.

**Exemplo:**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
É funcionalmente equivalente à:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Que retorna apenas os valores de certificado não expirados no atributo userCertificate.


---
### <a name="word"></a>Word
**Descrição:**  
a função Word retorna uma palavra contida em uma cadeia de caracteres com base nos parâmetros que descrevem os delimitadores a serem usados e o número de palavras a serem retornadas.

**Sintaxe:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: a cadeia de caracteres da qual retornar uma palavra
* WordNumber: um número que identifica qual número de palavras deve retornar.
* delimitadores: uma cadeia de caracteres que representa o delimitador(es) que deve ser usado para identificar palavras

**Comentários:**  
cada cadeia de caracteres separada por um dos caracteres delimitadores na cadeia de caracteres é identificada como palavra:

* Se number < 1, retorna uma cadeia de caracteres vazia.
* Se a cadeia de caracteres for nula, retorna a cadeia de caracteres vazia.

Se a cadeia de caracteres for menor que o número de palavras ou a cadeia não contiver nenhuma palavra identificada por delimitadores, uma cadeia de caracteres vazia será retornada.

**Exemplo:**  
`Word("The quick brown fox",3," ")`  
retorna "brown"

`Word("This,string!has&many separators",3,",!&#")`  
retornaria "has"

## <a name="additional-resources"></a>Recursos adicionais
* [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Sincronização do Azure AD Connect: personalizando opções de Sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)
