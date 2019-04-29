---
title: Modere textos usando a API de Moderação de Texto – Content Moderator
titlesuffix: Azure Cognitive Services
description: Moderação de texto baseada em teste usando a API de Moderação de Texto no console online.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995584"
---
# <a name="moderate-text-from-the-api-console"></a>Moderar texto no console da API

Use a [API de Moderação de Texto](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) no Content Moderator do Azure para examinar o conteúdo do texto. A operação examina o conteúdo buscando obscenidades e compara o conteúdo com listas negras personalizadas e compartilhadas.


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

### <a name="parameters"></a>parâmetros
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

> [!NOTE]
> O número do seguro social inválido no texto de exemplo a seguir é intencional. A finalidade é transmitir o formato de entrada e saída de exemplo.

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>Recurso de classificação de texto

No exemplo a seguir, você verá a resposta de classificação de texto assistido por computador do Content Moderator. Ele ajuda a detectar um conteúdo potencialmente indesejado. O conteúdo sinalizado pode ser considerado inadequado, dependendo do contexto. Além de transmitir a probabilidade de cada categoria, pode recomendar uma análise humana do conteúdo. O recurso utiliza um modelo treinado para identificar uma possível linguagem abusiva, depreciativa ou discriminatória. Isso inclui gírias, palavras abreviadas, palavras ofensivas e intencionalmente incorretas para a análise. 

#### <a name="explanation"></a>Explicação

- `Category1` representa a presença potencial de uma linguagem que pode ser considerada sexualmente explícita ou de conteúdo para adultos em determinadas situações.
- `Category2` representa a presença potencial de uma linguagem que pode ser considerada sexualmente sugestiva ou para adultos em determinadas situações.
- `Category3` representa a presença potencial de uma linguagem que pode ser considerada ofensiva em determinadas situações.
- `Score` fica entre 0 e 1. Quanto maior a pontuação, mais o modelo irá prever que a categoria pode ser aplicável. Essa visualização se baseia em um modelo estatístico, em vez dos resultados codificados manualmente. É recomendável testar com seu próprio conteúdo para determinar como cada categoria se alinha com seus requisitos.
- `ReviewRecommended` é verdadeiro ou falso, dependendo dos limites internos da pontuação. Os clientes devem avaliar se é para usar esse valor ou escolher limites personalizados com base nas políticas de conteúdo.

### <a name="analyze-the-response"></a>Analisar a resposta
A resposta a seguir mostra as várias informações da API. Contém obscenidades em potencial, PII, classificação (visualização) e a versão corrigida automaticamente.

> [!NOTE]
> O recurso “Classificação” assistido por computador está na visualização e suporta apenas o inglês.

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Para obter uma explicação detalhada de todas as seções na resposta JSON, consulte a [visão geral da API de moderação de texto](text-moderation-api.md).

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou comece com o [início rápido do .NET de moderação de texto](text-moderation-quickstart-dotnet.md) para integrar em seu aplicativo.
