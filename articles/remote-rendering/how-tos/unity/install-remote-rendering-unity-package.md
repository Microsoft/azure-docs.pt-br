---
title: Instalar o pacote do Remote Rendering o para o Unity
description: Explica como instalar as DLLs de cliente de renderização remota para o Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564303"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering o para o Unity

A renderização remota do Azure usa um pacote do Unity para encapsular a integração no Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Gerenciar os pacotes de renderização remota no Unity

Os pacotes do Unity são contêineres que podem ser gerenciados por meio do [Gerenciador de pacotes](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)do Unity.
Esse pacote contém a API C# inteira, bem como todos os binários de plug-in necessários para usar a renderização remota do Azure com o Unity.
O esquema de nomenclatura do Unity a seguir para pacotes, o pacote é chamado de **com. Microsoft. Azure. Remote-renderizando**.

O pacote não faz parte do [repositório de exemplos de arr](https://github.com/Azure/azure-remote-rendering)e não está disponível no registro de pacote interno do Unity. Para adicioná-lo a um projeto, você precisa editar manualmente o arquivo do projeto `manifest.md` para adicionar o seguinte:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

Depois que isso tiver sido adicionado, você poderá usar o Gerenciador de pacotes do Unity para garantir que tem a versão mais recente.
Instruções mais abrangentes são fornecidas no [tutorial: exibir modelos remotos](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Pipelines de renderização do Unity

A renderização remota funciona com o **:::no-loc text="Universal render pipeline":::** e o **:::no-loc text="Standard render pipeline":::** . Por motivos de desempenho, o pipeline de processamento universal é recomendado.

Para usar o **:::no-loc text="Universal render pipeline":::** , seu pacote deve ser instalado no Unity. Isso pode ser feito na interface do usuário do **Gerenciador de pacotes** do Unity (nome do pacote **Universal RP**, versão 7.3.1 ou mais recente) ou por meio do `Packages/manifest.json` arquivo, conforme descrito no [tutorial de configuração do projeto do Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Próximas etapas

* [Componentes e objetos de jogos do Unity](objects-components.md)
* [Tutorial: exibir modelos remotos](../../tutorials/unity/view-remote-models/view-remote-models.md)
