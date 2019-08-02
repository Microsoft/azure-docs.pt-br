---
title: Como usar a API de Verificação Ortográfica do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os modos de Verificação Ortográfica do Bing, as configurações e outras informações relacionadas à API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 7fd528b5f21622051bdad67c6ffcb6dd8500386a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423636"
---
# <a name="using-the-bing-spell-check-api"></a>Como usar a API de Verificação Ortográfica do Bing

Use este artigo para saber como usar a API de Verificação Ortográfica do Bing para fazer verificações gramaticais e ortográficas contextuais. Embora a maioria dos verificadores ortográficos dependa de conjuntos de regras baseados em dicionário, o verificador ortográfico do Bing aproveita o aprendizado de máquina e a tradução automática estatística para fornecer correções precisas e contextuais. 

## <a name="spell-check-modes"></a>Modos de verificação ortográfica

A API dá suporte a dois modos de revisão de texto, `Proof` e `Spell`.  Experimente exemplos [aqui](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Prova – para documentos 

O modo padrão é `Proof`. O modo de verificação ortográfica `Proof` faz as verificações mais abrangentes, adicionando capitalização, pontuação básica e outros recursos para auxiliar na criação de documentos. mas está disponível somente nos mercados en-US (Inglês (Estados Unidos)), es-ES (Espanhol), pt-BR (Português (Brasil)) (Observação: somente na versão beta para espanhol e português). Em todos os outros mercados, defina o parâmetro de consulta de modo como Spell. 

> [!NOTE]
> se o comprimento do texto de consulta exceder 4096, ele será truncado para 4096 caracteres e, em seguida, será processado. 

### <a name="spell----for-web-searchesqueries"></a>Ortografia – para consultas/pesquisas na Web

`Spell` é mais agressiva para retornar melhores resultados de pesquisa. O modo `Spell` localiza a maioria dos erros de ortografia, mas não encontra alguns erros gramaticais que `Proof` encontra, por exemplo, uso de maiúsculas e palavras repetidas.

> [!NOTE]
> * O tamanho máximo da consulta com suporte é mestrado abaixo. Se a consulta exceder o comprimento máximo, a consulta e seus resultados não serão alterados.
>    * 130 caracteres para os seguintes códigos de idioma: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, ele, zh, ko. 
>    * Sessenta e cinco caracteres para todas as outras.
> * O Modo de ortografia não oferece suporte a caracteres de colchete (`[` e `]`) em consultas e pode causar resultados inconsistentes. É recomendável removê-los de suas consultas ao usar o Modo de ortografia.

## <a name="market-setting"></a>Configuração do mercado

Um [código de mercado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) deve ser especificado com o parâmetro de consulta `mkt` na solicitação. Caso contrário, a API usará um mercado padrão com base no endereço IP da solicitação.


## <a name="http-post-and-get-support"></a>Suporte a HTTP POST e GET

A API dá suporte a HTTP POST ou HTTP GET. Qual você vai usar dependerá do tamanho do texto que deve ser revisado. Se as cadeias de caracteres tiverem sempre menos de 1.500 caracteres, o ideal será GET. Mas se você quiser dar suporte a cadeias de até 10 mil caracteres, use POST. A cadeia de caracteres de texto pode incluir qualquer caractere UTF-8 válido.

O exemplo a seguir mostra uma solicitação POST para verificar a ortografia e a gramática de uma cadeia de caracteres de texto. O exemplo inclui o parâmetro de consulta [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) para integridade de consulta (ele poderia ter sido deixado de fora já que o `mode` padrão é Proof). O parâmetro de consulta [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) contém a cadeia de caracteres a ser revisada.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Se você usar HTTP GET, deverá incluir o parâmetro de consulta `text` na cadeia de caracteres de consulta da URL
  
O exemplo a seguir mostra a resposta à solicitação anterior. A resposta contém um objeto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
O campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) lista os erros de ortografia e gramática que a API encontrou na cadeia de caracteres [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). O campo `token` contém a palavra a ser substituída. Você usaria o deslocamento de base zero no campo `offset` para localizar o token na cadeia de caracteres `text`. Em seguida, você substituiria a palavra nesse local pela palavra no campo `suggestion`. 

Se o campo `type` for RepeatedToken, você ainda poderia substituir o token por `suggestion`, mas provavelmente também precisaria remover o espaço à direita.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

- [O que é API de Verificação Ortográfica do Bing?](../overview.md)
- [Referência de API de Verificação Ortográfica do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
