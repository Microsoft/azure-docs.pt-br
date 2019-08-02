---
title: Modere textos usando a API de Moderação de Texto – Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderação de texto baseada em teste usando a API de Moderação de Texto no console online.
services: cognitive-services
author: sanjeev3
ms.author: sajagtap
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 49047c21f86530aac6ed3e0138a643fd08c35af8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561179"
---
# <a name="moderate-text-from-the-api-console"></a>Moderar texto no console da API

Use a [API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) de moderação de texto no Content moderator do Azure para verificar seu conteúdo de texto em busca de profanação e compará-lo em listas personalizadas e compartilhadas.

## <a name="get-your-api-key"></a>Obter sua chave de API

Antes de testar a API no console online, você precisa da chave de assinatura. Ela está localizada na guia **Configurações**, na caixa **Ocp-Apim-Subscription-Key**. Para obter mais informações, consulte [Visão Geral](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navegar até a referência da API

Vá para a [referência da API de Moderação de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  A página **Texto - Tela** será aberta.

## <a name="open-the-api-console"></a>Abrir o console da API

Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

  ![Seleção de região da página Texto - Tela](images/test-drive-region.png)

  O console da API de **Texto - Tela** será aberto.

## <a name="select-the-inputs"></a>Selecionar entradas

### <a name="parameters"></a>Parâmetros

Selecione os parâmetros de consulta que você deseja usar em sua tela de texto. Para este exemplo, use o valor padrão para **idioma**. Você também pode deixar em branco porque a operação detectará automaticamente o idioma provável como parte de sua execução.

> [!NOTE]
> Para o parâmetro **language**, atribua `eng` ou deixe vazio para ver a resposta de **classificação** assistida por computador (recurso de visualização). **Esse recurso dá suporte ao inglês apenas**.
>
> Para a detecção de **termos obscenos**, use o [código ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) dos idiomas com suporte listados neste artigo ou deixe vazio.

Para a **correção automática**, **PII** e **classificar (visualização)** , selecione **true**. Deixe o campo **ListId** vazio.

  ![Parâmetros de consulta do console Texto - Tela](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo de conteúdo

Para **Content-Type**, selecione o tipo de conteúdo que você deseja na tela. Para este exemplo, use o tipo de conteúdo **texto/simples** padrão. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

### <a name="sample-text-to-scan"></a>Texto de exemplo para examinar

Na caixa **Corpo da solicitação**, digite algum texto. O exemplo a seguir mostra um erro de digitação intencional no texto.

> [!NOTE]
> O número do seguro social inválido no texto de exemplo a seguir é intencional. A finalidade é transmitir o formato de entrada e saída de exemplo.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analisar a resposta

A resposta a seguir mostra as várias informações da API. Ele contém possíveis obscenidades, dados pessoais, classificação (visualização) e a versão corrigida automaticamente.

> [!NOTE]
> O recurso “Classificação” assistido por computador está na visualização e suporta apenas o inglês.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Para obter uma explicação detalhada de todas as seções na resposta JSON, consulte o guia conceitual de moderação de [texto](text-moderation-api.md) .

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou comece com o guia de [início rápido do .net](text-moderation-quickstart-dotnet.md) de moderação de texto para integrar com seu aplicativo.
