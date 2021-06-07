---
title: Modere textos usando a API de Moderação de Texto – Content Moderator
titleSuffix: Azure Cognitive Services
description: Moderação de texto baseada em teste usando a API de Moderação de Texto no console online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001993"
---
# <a name="moderate-text-from-the-api-console"></a>Moderar texto no console da API

Use a [API de moderação de texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Content moderator do Azure para verificar seu conteúdo de texto em busca de profanação e compará-lo em listas personalizadas e compartilhadas.

## <a name="get-your-api-key"></a>Obter sua chave de API

Antes de testar a API no console online, você precisa da chave de assinatura. Ela está localizada na guia **Configurações**, na caixa **Ocp-Apim-Subscription-Key**. Para obter mais informações, consulte [visão geral](overview.md).

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

Para a **correção automática**, **PII** e **classificar (visualização)**, selecione **true**. Deixe o campo **ListId** vazio.

  ![Parâmetros de consulta do console Texto - Tela](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tipo de conteúdo

Para **Content-Type**, selecione o tipo de conteúdo que você deseja na tela. Para este exemplo, use o tipo de conteúdo **texto/simples** padrão. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

### <a name="sample-text-to-scan"></a>Texto de exemplo para examinar

Na caixa **Corpo da solicitação**, digite algum texto. O exemplo a seguir mostra um erro de digitação intencional no texto.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analisar a resposta

A resposta a seguir mostra as várias informações da API. Ele contém possíveis obscenidades, dados pessoais, classificação (visualização) e a versão corrigida automaticamente.

> [!NOTE]
> O recurso “Classificação” assistido por computador está na visualização e suporta apenas o inglês.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Para obter uma explicação detalhada de todas as seções na resposta JSON, consulte o guia conceitual de [moderação de texto](text-moderation-api.md) .

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou siga o [início rápido do SDK do .net](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) para integrar com seu aplicativo.