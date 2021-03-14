---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: 4345810292896cf88de19baf419eee025ba5853f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044535"
---
A próxima etapa é baixar o pacote de Âncoras de Objeto do Azure para o Unity.

# <a name="download-with-web-browser"></a>[Baixar com o navegador da Web](#tab/unity-package-web-ui)

Localize o pacote de Âncoras de Objeto do Azure para o Unity [aqui](https://aka.ms/aoa/unity-sdk/package). Selecione a versão desejada e baixe o pacote usando o botão **Baixar**.

# <a name="download-with-npm"></a>[Baixar com o NPM](#tab/unity-package-npm)

Essa etapa requer que o <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> esteja instalado e disponível.

Execute o comando a seguir, substituindo `<version_number>` pela versão das Âncoras de Objeto do Azure que você deseja baixar:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Para listar as versões disponíveis do pacote das Âncoras de Objeto do Azure, execute o seguinte:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

O pacote das Âncoras de Objeto do Azure será baixado para a pasta em que você executou o comando.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Instalar com a Ferramenta de Recurso de Realidade Misturada (beta)](#tab/unity-package-mixed-reality-feature-tool)

Siga para a próxima etapa. Você usará a <a a href="https://aka.ms/MRFeatureToolDocs" target="_blank">Ferramenta de Recurso de Realidade Misturada</a> em uma etapa posterior.

---
