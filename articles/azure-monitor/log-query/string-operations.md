---
title: Trabalhe com cadeias de caracteres nas consultas de log no Azure Monitor | Microsoft Docs
description: Descreve como editar, comparar, pesquisar e executar uma variedade de outras operações em cadeias de caracteres nas consultas do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424696"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Trabalhe com cadeias de caracteres nas consultas de log no Azure Monitor


> [!NOTE]
> Você deve concluir [Introdução ao Log Analytics do Azure Monitor](get-started-portal.md) e [Introdução às consultas de log do Azure Monitor](get-started-queries.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Este artigo descreve como editar, comparar, pesquisar e executar uma variedade de outras operações em cadeias de caracteres.

Cada caractere em uma cadeia de caracteres tem um número de índice, de acordo com o local. O primeiro caractere está no índice 0, o próximo caractere é 1, e assim por diante. Diferentes funções de cadeia de caracteres usam números de índice conforme mostrado nas seções a seguir. Muitos dos exemplos a seguir usam o comando **print** para demonstrar a manipulação da cadeia de caracteres sem usar uma fonte de dados específica.


## <a name="strings-and-escaping-them"></a>Cadeias de caracteres e seus escapes
Os valores da cadeia de caracteres são encapsulados com caracteres de aspas simples ou duplas. Barra invertida (\) é usada para caracteres de escape para o caractere seguinte, como \t para tab \n para a newline e \" o próprio caractere de aspas.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Para evitar que "\\" atue como um caractere de escape, adicione "\@" como um prefixo para a cadeia de caracteres:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Comparações de cadeias de caracteres

operador       |DESCRIÇÃO                         |Diferencia maiúsculas de minúsculas|Exemplo (suspende `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |É igual a                              |Sim           |`"aBc" == "aBc"`
`!=`           |Não é igual a                          |Sim           |`"abc" != "ABC"`
`=~`           |É igual a                              |Não             |`"abc" =~ "ABC"`
`!~`           |Não é igual a                          |Não             |`"aBc" !~ "xyz"`
`has`          |O lado direito é um termo completo no lado esquerdo |Não |`"North America" has "america"`
`!has`         |O lado direito não é um termo completo no lado esquerdo       |Não             |`"North America" !has "amer"` 
`has_cs`       |O lado direito é um termo completo no lado esquerdo |Sim|`"North America" has_cs "America"`
`!has_cs`      |O lado direito não é um termo completo no lado esquerdo       |Sim            |`"North America" !has_cs "amer"` 
`hasprefix`    |O lado direito é um prefixo de termo no lado esquerdo         |Não             |`"North America" hasprefix "ame"`
`!hasprefix`   |O lado direito não é um prefixo de termo no lado esquerdo     |Não             |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |O lado direito é um prefixo de termo no lado esquerdo         |Sim            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |O lado direito não é um prefixo de termo no lado esquerdo     |Sim            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |O lado direito é um sufixo de termo no lado esquerdo         |Não             |`"North America" hassuffix "ica"`
`!hassuffix`   |O lado direito não é um sufixo de termo no lado esquerdo     |Não             |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |O lado direito é um sufixo de termo no lado esquerdo         |Sim            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |O lado direito não é um sufixo de termo no lado esquerdo     |Sim            |`"North America" !hassuffix_cs "icA"`
`contains`     |O lado direito ocorre como uma subsequência do lado esquerdo  |Não             |`"FabriKam" contains "BRik"`
`!contains`    |O lado direito não ocorre no lado esquerdo           |Não             |`"Fabrikam" !contains "xyz"`
`contains_cs`   |O lado direito ocorre como uma subsequência do lado esquerdo  |Sim           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |O lado direito não ocorre no lado esquerdo           |Sim           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |O lado direito é uma subsequência inicial do lado esquerdo|Não             |`"Fabrikam" startswith "fab"`
`!startswith`  |O lado direito não é uma subsequência inicial do lado esquerdo|Não         |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |O lado direito é uma subsequência inicial do lado esquerdo|Sim            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |O lado direito não é uma subsequência inicial do lado esquerdo|Sim        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |O lado direito é uma subsequência de fechamento do lado esquerdo|Não              |`"Fabrikam" endswith "Kam"`
`!endswith`    |O lado direito não é uma subsequência de fechamento do lado esquerdo|Não          |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |O lado direito é uma subsequência de fechamento do lado esquerdo|Sim             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |O lado direito não é uma subsequência de fechamento do lado esquerdo|Sim         |`"Fabrikam" !endswith "brik"`
`matches regex`|O lado esquerdo contém uma correspondência para o lado Direito        |Sim           |`"Fabrikam" matches regex "b.*k"`
`in`           |Equivale a um dos elementos       |Sim           |`"abc" in ("123", "345", "abc")`
`!in`          |Não equivale a qualquer um dos elementos   |Sim           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Conta as ocorrências de uma subcadeia de caracteres em uma cadeia de caracteres. Pode corresponder cadeias de caracteres sem formatação ou usar regex. As correspondências de cadeia de caracteres sem formatação podem se sobrepor, enquanto as correspondências de regex não.

### <a name="syntax"></a>Sintaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumentos:
- `text` - A cadeia de caracteres de entrada 
- `search` - Cadeia de caracteres sem formatação ou expressão regular a ser correspondida no texto.
- `kind` - _normal_ | _regex_ (padrão: normal).

### <a name="returns"></a>Retornos

O número de vezes que a cadeia de caracteres de pesquisa pode ser correspondida no contêiner. As correspondências de cadeia de caracteres sem formatação podem se sobrepor, enquanto as correspondências de regex não podem.

### <a name="examples"></a>Exemplos

#### <a name="plain-string-matches"></a>Correspondências de cadeia de caracteres sem formatação

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondências de regex

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

Obtém uma correspondência para uma expressão regular a partir de uma determinada cadeia de caracteres. Opcionalmente, também converte a subcadeia de caracteres extraída no tipo especificado.

### <a name="syntax"></a>Sintaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumentos

- `regex` - Uma expressão regular.
- `captureGroup` - Uma inteiro constante que indica o grupo de captura para extração. 0 para a correspondência inteira, 1 para o valor correspondido pelo primeiro '('parêntese')' na expressão regular, 2 ou mais para os parênteses subsequentes.
- `text` - Uma cadeia de caracteres a ser pesquisada.
- `typeLiteral` - Um literal de tipo opcional (por exemplo, typeof(long)). Se for fornecido, a subcadeia de caracteres extraída será convertida para esse tipo.

### <a name="returns"></a>Retornos
A subcadeia de caracteres correspondida com base no grupo de captura indicado captureGroup, opcionalmente convertida em typeLiteral.
Se não houver correspondência, ou se a conversão de tipo retornar nulo.

### <a name="examples"></a>Exemplos

O exemplo a seguir extrai o último octeto do *ComputerIP* de um registro de pulsação:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

O exemplo a seguir extrai o último octeto, converte-o para um tipo *real* (número) e calcula o próximo valor IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

No exemplo a seguir, a cadeia de caracteres *Trace* é pesquisada para obter uma definição de "Duração". A correspondência é convertida para *real* e multiplicada por uma constante de tempo (1 s) *que converte Duração para o tipo intervalo de tempo*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* retorna true se o argumento for uma cadeia de caracteres vazia ou nulo (Consulte também *isnull*).
- *isnotempty* retorna true se o argumento não for uma cadeia de caracteres vazia ou um nulo (Consulte também *isnull*). alias: *notempty*.

### <a name="syntax"></a>Sintaxe

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Exemplos

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Divide uma URL em suas partes (protocolo, host, porta, etc.) e retorna um objeto de dicionário que contém as partes como cadeias de caracteres.

### <a name="syntax"></a>Sintaxe

```
parseurl(urlstring)
```

### <a name="examples"></a>Exemplos

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

O resultado será:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>substituir

Substitui todas as correspondências de regex por outra cadeia de caracteres. 

### <a name="syntax"></a>Sintaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumentos

- `regex` - A expressão regular para fazer a correspondência. Pode conter grupos de captura entre '('parênteses')'.
- `rewrite` - O regex de substituição para qualquer correspondência feita pelo regex de correspondência. Use \0 para referir-se à correspondência inteira, \1 para o primeiro grupo de captura, \2, e assim por diante para grupos de captura subsequentes.
- `input_text` - A cadeia de caracteres de entrada para pesquisar.

### <a name="returns"></a>Retornos
O texto depois de substituir todas as correspondências de regex por avaliações de regenerar. Correspondências não se sobrepõem.

### <a name="examples"></a>Exemplos

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Pode ter os seguintes resultados:

Atividade                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663 - Foi feita uma tentativa de acessar um objeto  |ID da atividade 4663: Foi feita uma tentativa de acessar um objeto.


## <a name="split"></a>split

Divide uma determinada cadeia de caracteres de acordo com um delimitador especificado e retorna uma matriz de subcadeias de caracteres resultantes.

### <a name="syntax"></a>Sintaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumentos:

- `source` - A cadeia de caracteres a ser dividida de acordo com o delimitador especificado.
- `delimiter` - O delimitador que será usado para dividir a cadeia de caracteres de origem.
- `requestedIndex` - Um índice opcional com base em zero. Se fornecida, a matriz de cadeia de caracteres retornada conterá apenas esse item (se existir).


### <a name="examples"></a>Exemplos

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Concatena os argumentos de cadeia de caracteres (dá suporte para 1 a 16 argumentos).

### <a name="syntax"></a>Sintaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Exemplos
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Retorna o comprimento de uma cadeia de caracteres.

### <a name="syntax"></a>Sintaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Exemplos
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Extrai uma subcadeia de caracteres a partir de uma determinada cadeia de caracteres de origem, começando pelo índice especificado. Opcionalmente, é possível especificar o comprimento da subcadeia de caracteres solicitada.

### <a name="syntax"></a>Sintaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumentos:

- `source` - A cadeia de caracteres de origem da qual a subcadeia de caracteres será extraída.
- `startingIndex` - A posição do caractere inicial com base em zero da subcadeia de caracteres solicitada.
- `length` - Um parâmetro opcional que pode ser usado para especificar o comprimento solicitado da subcadeia de caracteres retornada.

### <a name="examples"></a>Exemplos
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Converte uma determinada cadeia de caracteres em todas as letras minúsculas ou maiúsculas.

### <a name="syntax"></a>Sintaxe
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Exemplos
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Próximas etapas
Continue com os tutoriais avançados:
* [Funções de agregação](aggregations.md)
* [Agregações avançadas](advanced-aggregations.md)
* [Gráficos e diagramas](charts.md)
* [Trabalhar com JSON e estruturas de dados](json-data-structures.md)
* [Gravação de consulta avançada](advanced-query-writing.md)
* [Junções - análise cruzada](joins.md)
