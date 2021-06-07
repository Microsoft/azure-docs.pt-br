---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076678"
---
A próxima etapa é baixar os pacotes de Âncoras Espaciais do Azure para o Unity. 

> [!WARNING]
> O SDK 2.7.0 do ASA é a versão mínima compatível. Se você está usando o Unity 2020, o SDK 2.9.0 do ASA é a versão mínima compatível.

Para usar as Âncoras Espaciais do Azure no Unity, você precisará baixar o pacote principal, bem como um pacote específico da plataforma para cada plataforma (Android/iOS/HoloLens) para a qual planeja dar suporte.

| Plataforma | Nome do pacote                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<número_de_versão> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<número_de_versão> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<número_de_versão>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<número_de_versão> |

# <a name="download-with-web-browser"></a>[Baixar com o navegador da Web](#tab/unity-package-web-ui)

Localize o pacote principal das Âncoras Espaciais do Azure para o Unity [aqui](https://aka.ms/aoa/unity-sdk/package). Selecione a versão desejada e baixe o pacote usando o botão **Baixar**. Repita essa etapa para baixar o pacote para cada plataforma para a qual você planeja dar suporte.

# <a name="download-with-npm"></a>[Baixar com o NPM](#tab/unity-package-npm)

Essa etapa requer que o <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> esteja instalado e disponível.

Execute o seguinte comando substituindo `<version_number>` pela versão das Âncoras Espaciais do Azure que você deseja baixar na pasta atual:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Para listar as versões disponíveis do pacote das Âncoras Espaciais do Azure, execute o seguinte:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

O pacote principal das Âncoras Espaciais do Azure será baixado para a pasta em que você executou o comando. Repita essa etapa para baixar o pacote para cada plataforma para a qual você planeja dar suporte.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalar com a Ferramenta de Recurso de Realidade Misturada (beta)](#tab/unity-package-mixed-reality-feature-tool)

Siga para a próxima etapa. Você usará a <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Ferramenta de Recurso de Realidade Misturada</a> em uma etapa posterior.

---