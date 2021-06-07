---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fc4cc5063f72ff3f0db62cde79f7908add86166e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434426"
---
:::row:::
    :::column span="3":::
        O SDK de fala dá suporte ao Windows 10 e ao Windows Server 2016 ou versões posteriores. **Não** há suporte oficialmente para versões anteriores. É possível usar partes do SDK de fala com versões anteriores do Windows, embora não seja recomendável.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Requisitos de sistema

O SDK do Speech no Windows requer o <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ redistribuível para o Visual Studio 2019 </a> no sistema.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalar para x86 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalar para x64 </a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalar para ARMx64 </a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Para a entrada do microfone, as bibliotecas do Media Foundation precisam ser instaladas. Essas bibliotecas fazem parte do Windows 10 e do Windows Server 2016. É possível usar o SDK de Fala sem essas bibliotecas, contanto que o microfone não seja usado como o dispositivo de entrada de áudio.

Os arquivos necessários do SDK de Fala podem ser implantados no mesmo diretório do seu aplicativo. Dessa forma, seu aplicativo pode acessar diretamente as bibliotecas. Verifique se você selecionou a versão correta (x86/x64) que corresponde ao seu aplicativo.

| Nome                                            | Função                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | SDK principal, necessário para implantação nativa e gerenciada |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Necessário para implantação gerenciada                      |

> [!NOTE]
> A partir da versão 1.3.0, o arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada no SDK principal.

> [!IMPORTANT]
> Para o projeto do Windows Forms app (.NET Framework) C#, verifique se as bibliotecas estão incluídas nas configurações de implantação do seu projeto. Você pode verificar isso em `Properties -> Publish Section` . Clique no `Application Files` botão e localize as bibliotecas correspondentes na lista rolar para baixo. Verifique se o valor está definido como `Included` . O Visual Studio incluirá o arquivo quando o projeto for publicado/implantado.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
