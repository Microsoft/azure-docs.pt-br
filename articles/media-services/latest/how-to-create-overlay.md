---
title: Como criar uma sobreposição com Media Encoder Standard
description: Saiba como criar uma sobreposição com Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844277"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Como criar uma sobreposição com Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Media Encoder Standard permite que você sobreponha uma imagem, um arquivo de áudio ou outro vídeo em outro vídeo. A entrada deve especificar exatamente um arquivo. Você pode especificar um arquivo de imagem no formato JPG, PNG, GIF ou BMP ou um arquivo de áudio (como um arquivo WAV, MP3, WMA ou M4A) ou um arquivo de vídeo.


## <a name="prerequisites"></a>Pré-requisitos

* Colete as informações da conta de que você precisa para configurar o *appsettings.jsno* arquivo no exemplo. Se você não tiver certeza de como fazer isso, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../../active-directory/develop/quickstart-register-app.md). Os valores a seguir são esperados no *appsettings.jsno* arquivo.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Se você ainda não estiver familiarizado com as transformações, é recomendável que você conclua as seguintes atividades:

* Ler [codificação de vídeo e áudio com os serviços de mídia](encoding-concept.md)
* Leia [como codificar com uma transformação personalizada-.net](customize-encoder-presets-how-to.md). Siga as etapas neste artigo para configurar o .NET necessário para trabalhar com transformações e, em seguida, retornar aqui para experimentar uma amostra predefinida de sobreposições.
* Consulte o [documento de referência de transformações](/rest/api/media/transforms).

Quando estiver familiarizado com as transformações, baixe o exemplo de sobreposições.

## <a name="overlays-preset-sample"></a>Amostra predefinida de sobreposições

Baixe a [amostra Media-Services-Overlay](https://github.com/Azure-Samples/media-services-overlays) para começar com sobreposições.

## <a name="next-steps"></a>Próximas etapas

* [Subclipe um vídeo ao codificar com os serviços de mídia-.NET](subclip-video-dotnet-howto.md)
