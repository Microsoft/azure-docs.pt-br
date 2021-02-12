---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 9554e04c82299016076f09f85f604af10a94ab4a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185223"
---
## <a name="android"></a>[Android](#tab/Android)

O exemplo do Java Android é compatível com o compartilhamento entre dispositivos.

No Android Studio, abra o arquivo *SharedActivity.java* da pasta de exemplos. 

Insira a URL que você copiou na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `SharingAnchorsServiceUrl` no arquivo *SharedActivity.java*. 

Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

O exemplo do iOS Objective-C dá suporte ao compartilhamento entre dispositivos.

Abra o arquivo *SharedDemoViewController.m* na pasta de exemplos. 

Insira a URL obtida na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `SharingAnchorsServiceUrl` no arquivo *SharedDemoViewController.m*. 

Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Implante o aplicativo em seu dispositivo. 

Depois que o aplicativo for iniciado, selecione a opção **Toque para iniciar a demonstração compartilhada** e siga as instruções no aplicativo. Selecione **Toque para localizar a Âncora pelo número de âncora** ou **Toque para criar uma Âncora e salvá-la no serviço**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Os exemplos do Xamarin Android e iOS dão suporte ao compartilhamento entre dispositivos.

Abra o arquivo *AccountDetails.cs* na pasta de exemplos. 

Insira a URL obtida na etapa anterior (em sua implantação do Azure do aplicativo Web ASP.NET) como o valor de `AnchorSharingServiceUrl` no arquivo *AccountDetails.cs*. 

Substitua o `index.html` na URL por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel **Projeto**, acesse `Assets\AzureSpatialAnchors.Examples\Resources`. 

Selecione **SpatialAnchorSamplesConfig**. Em seguida, no painel **Inspetor**, insira a URL `Sharing Anchors Service` (de sua implantação do Azure do aplicativo Web ASP.NET) como o valor para `Base Sharing Url`. Substitua `index.html` por `api/anchors`. O resultado deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **Arquivo** > **Salvar**.

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

### <a name="deploy-to-an-android-device"></a>Implantar em um dispositivo Android

Entre no dispositivo Android e conecte-o em seu computador usando um cabo USB.

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas no Build**, verifique se cada cena tem a marca de seleção ao lado dela.

A opção **Exportar Projeto** não deve ter a marca de seleção. Selecione **Compilar e Executar**. Você será solicitado a salvar o arquivo *.apk*. Você pode escolher qualquer nome para ele.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implantar em um dispositivo iOS

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Em **Cenas no Build**, verifique se cada cena tem a marca de seleção ao lado dela.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

No Xcode, interrompa o aplicativo selecionando **Parar**.
