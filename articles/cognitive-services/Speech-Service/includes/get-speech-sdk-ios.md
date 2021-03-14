---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 0a9ca21cc14bb87797c962a89cf87ac184e73735
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434424"
---
:::row:::
    :::column span="3":::
        Ao desenvolver para iOS, há dois SDKs de fala disponíveis. O SDK de fala do Objective-C está disponível nativamente como um pacote iOS CocoaPod. Como alternativa, o SDK de fala do .NET pode ser usado com o Xamarin. iOS, pois ele implementa .NET Standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Para obter detalhes sobre como usar o SDK de fala do Objective-C com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importando Objective-c para Swift </a>.

### <a name="system-requirements"></a>Requisitos do sistema

- Um macOS versão 10,3 ou posterior
- Destino iOS 9,3 ou posterior

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        O pacote iOS CocoaPod está disponível para download e uso com o ambiente de desenvolvimento integrado (IDE) do <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) </a> . Primeiro, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">Baixe o CocoaPod binário </a>. Extraia o pod no mesmo diretório para seu uso pretendido, crie um *Podfile* e liste o `pod` como um `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS expõe o SDK de iOS completo para desenvolvedores de .NET. Compile aplicativos iOS totalmente nativos usando C# ou F# no Visual Studio. Para obter mais informações, consulte <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin. Ios </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Objetivo do início rápido do SDK de fala do iOS-código-fonte C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">código-fonte do início rápido do SDK de fala do iOS </a>