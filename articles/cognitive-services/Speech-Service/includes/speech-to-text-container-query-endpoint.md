---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98947765"
---
O contêiner fornece APIs de ponto de extremidade de consulta baseadas no WebSocket, que são acessadas por meio do [SDK de fala](../index.yml). Por padrão, o SDK de fala usa os serviços de fala online. Para usar o contêiner, você precisa alterar o método de inicialização.

> [!TIP]
> Ao usar o SDK de fala com contêineres, você não precisa fornecer a chave de assinatura de recurso de fala do Azure [ou um token de portador de autenticação](../rest-speech-to-text.md#authentication).

Confira os exemplos abaixo.

# <a name="c"></a>[C#](#tab/csharp)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Para usar essa chamada com o [host](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost)do contêiner:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Em vez de usar esta chamada de inicialização de nuvem do Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Para usar essa chamada com o [ponto de extremidade](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)do contêiner:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
