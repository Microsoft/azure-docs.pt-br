---
title: Como criar uma sobreposição com Media Encoder Standard
description: Saiba como criar uma sobreposição com Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 69930e06e2ce7f2679feec74ca8ccbc93bdb8d30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101721116"
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

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
