---
title: Guia de início rápido da API REST do Content Moderator
titleSuffix: Azure Cognitive Services
description: Neste guia início rápido, saiba como começar a usar a API REST do Content Moderator do Azure. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 747f97e54dbe10ca055281c189c805e3dd97c867
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582804"
---
Introdução à API REST do Azure Content Moderator. 

O Content Moderator é um serviço de IA que permite que você lide com conteúdos potencialmente ofensivos, arriscados ou, de alguma outra forma, indesejáveis. Use o serviço de moderação de conteúdo baseado em IA para digitalizar texto, imagem e vídeos e aplicar sinalizadores de conteúdo automaticamente. Em seguida, integre seu aplicativo com a ferramenta de Análise, um ambiente de moderador online para uma equipe de revisores humanos. Insira software de filtragem de conteúdo em seu aplicativo para obedecer a regulamentos ou manter o ambiente pretendido para seus usuários.

Use a API REST do Content Moderator para:

* Moderar texto
* Moderar imagens

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de obter uma assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Criar um recurso do Content Moderator"  target="_blank">crie um recurso do Content Moderator </a> no portal do Azure para obter uma chave e um ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Será necessário obter uma chave e um ponto de extremidade do recurso criado para conectar o aplicativo ao Content Moderator. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* [PowerShell versão 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows) ou um aplicativo de linha de comando semelhante.


## <a name="moderate-text"></a>Moderar texto

Você usará um comando como este a seguir para chamar a API do Content Moderator para analisar um corpo de texto e imprimir os resultados no console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

Copie o comando para um editor de texto e faça as seguintes alterações:

1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de assinatura de Detecção Facial válida.
1. Altere a primeira parte da URL de consulta para que ela corresponda ao ponto de extremidade referente à sua chave de assinatura.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Opcionalmente, altere o corpo da solicitação para qualquer cadeia de caracteres de texto que você queira analisar.

Depois de fazer suas alterações, abra um prompt de comando e digite o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Você verá os resultados de moderação de texto exibidos como dados JSON na janela do console. Por exemplo:

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Para obter mais informações sobre os atributos de texto que o Content Moderator avalia, confira o guia [Conceitos de moderação de texto](../../text-moderation-api.md).

## <a name="moderate-images"></a>Moderar imagens

Você usará um comando como este a seguir para chamar a API do Content Moderator para moderar uma imagem remota e imprimir os resultados no console.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

Copie o comando para um editor de texto e faça as seguintes alterações:

1. Atribua `Ocp-Apim-Subscription-Key` à sua chave de assinatura de Detecção Facial válida.
1. Altere a primeira parte da URL de consulta para que ela corresponda ao ponto de extremidade referente à sua chave de assinatura.
1. Opcionalmente, altere a URL de `"Value"` no corpo da solicitação para qualquer imagem remota que você gostaria de moderar.

> [!TIP]
> Você também pode moderar imagens locais, passando os dados de byte para o corpo da solicitação. Confira a [documentação de referência](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) para saber mais sobre como fazer isso.

Depois de fazer suas alterações, abra um prompt de comando e digite o novo comando. 

### <a name="examine-the-results"></a>Examinar os resultados

Você verá os resultados de moderação de imagem exibidos como dados JSON na janela do console. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Para obter mais informações sobre os atributos de imagem que o Content Moderator avalia, consulte o guia [Conceitos de moderação de imagem](../../image-moderation-api.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a usar a API REST do Content Moderator para realizar tarefas de moderação. Em seguida, saiba mais sobre a moderação de imagens ou outra mídia lendo um guia conceitual.

* [Conceitos de moderação de imagem](../../image-moderation-api.md)
* [Conceitos de moderação de texto](../../text-moderation-api.md)