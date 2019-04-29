---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: a6958ff41220bfe21ee8a5dc4a3fe94975eabbd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60805460"
---
No topo da classe Program, adicione as constantes a seguir para acessar o QnA Maker:

```csharp
// Represents the various elements used to create HTTP request URIs
// for QnA Maker operations.
static string host = "https://westus.api.cognitive.microsoft.com";
static string service = "/qnamaker/v4.0";
static string method = "/knowledgebases/create";

// NOTE: Replace this value with a valid QnA Maker subscription key.
static string key = "<your-qna-maker-subscription-key>";
```

As três primeiras constantes são usadas para criar a URL completa para a API. A última constante fornece autenticação para a API. 
