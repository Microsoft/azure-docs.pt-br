---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 4a4705647b90d29f47e37b88531f3432c6a2f448
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434423"
---
Ao desenvolver para macOS, há três SDKs de fala disponíveis.

- O SDK de fala do Objective-C está disponível nativamente como um pacote CocoaPod
- O SDK de fala do .NET pode ser usado com o **Xamarin. Mac** , pois ele implementa .net Standard 2,0
- O SDK de fala do Python está disponível como um módulo PyPI

> [!TIP]
> Para obter detalhes sobre como usar o SDK de fala do Objective-C com Swift, consulte <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importando Objective-c para Swift </a>.

### <a name="system-requirements"></a>Requisitos do sistema

- Um macOS versão 10,13 ou posterior

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        O pacote macOS CocoaPod está disponível para download e uso com o ambiente de desenvolvimento integrado (IDE) do <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (ou posterior) </a> . Primeiro, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Baixe o CocoaPod binário </a>. Extraia o pod no mesmo diretório para seu uso pretendido, crie um *Podfile* e liste o `pod` como um `target` .
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        O Xamarin.Mac expõe o SDK completo do macOS para desenvolvedores do .NET para compilar aplicativos do Mac nativos usando o C#. Para obter mais informações, consulte <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Recursos adicionais

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Guia de início rápido do SDK de fala do macOS-C código-fonte </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">código-fonte do QuickStart Swift do SDK de fala do macOS </a>