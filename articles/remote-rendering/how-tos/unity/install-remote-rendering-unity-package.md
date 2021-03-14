---
title: Instalar o pacote do Remote Rendering o para o Unity
description: Explica como instalar as DLLs de cliente de renderização remota para o Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720810"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalar o pacote do Remote Rendering o para o Unity

A renderização remota do Azure usa um pacote do Unity para encapsular a integração no Unity.
Esse pacote contém a API C# inteira, bem como todos os binários de plug-in necessários para usar a renderização remota do Azure com o Unity.
O esquema de nomenclatura do Unity a seguir para pacotes, o pacote é chamado de **com. Microsoft. Azure. Remote-renderizando**.

Você pode escolher uma das opções a seguir para instalar o pacote do Unity.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Instalar o pacote de renderização remoto usando a ferramenta de funcionalidade de realidade misturada

[A ferramenta de funcionalidade mista da realidade](https://aka.ms/MRFeatureToolDocs) ([Download](https://aka.ms/mrfeaturetool)) é uma ferramenta usada para integrar pacotes de recursos de realidade misturada em projetos do Unity. O pacote não faz parte do [repositório de exemplos de arr](https://github.com/Azure/azure-remote-rendering)e não está disponível no registro de pacote interno do Unity.

Para adicionar o pacote a um projeto, você precisa:
1. [Baixe a ferramenta de funcionalidade de realidade misturada](https://aka.ms/mrfeaturetool)
1. Siga as [instruções completas](https://aka.ms/MRFeatureToolDocs) sobre como usar a ferramenta.
1. Na página **descobrir recursos** , marque a caixa para o **Microsoft Azure pacote de renderização remoto** e selecione a versão do pacote que você deseja adicionar ao seu projeto

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Para atualizar seu pacote local, basta selecionar uma versão mais recente da ferramenta de recursos de realidade misturada e instalá-lo. A atualização do pacote pode ocasionalmente levar a erros de console. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="install-remote-rendering-package-manually"></a>Instalar o pacote de renderização remota manualmente

Para instalar o pacote de renderização remoto manualmente, você precisa:

1. Baixe o pacote do feed NPM de pacotes de realidade misturados em `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Você pode usar [NPM](https://www.npmjs.com/get-npm) e executar o comando a seguir para baixar o pacote para a pasta atual.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Ou você pode usar o script do PowerShell no `Scripts/DownloadUnityPackages.ps1` [repositório GitHub Azure-Remote-renderizando](https://github.com/Azure/azure-remote-rendering).
        * Editar o conteúdo de `Scripts/unity_sample_dependencies.json` para
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Execute o comando a seguir no PowerShell para baixar o pacote no diretório de destino fornecido.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Instale o pacote baixado com o](https://docs.unity3d.com/Manual/upm-ui-tarball.html) Gerenciador de pacotes do Unity.

Para atualizar seu pacote local, basta executar novamente o respectivo comando usado e reimportar o pacote. A atualização do pacote pode ocasionalmente levar a erros de console. Se isso ocorrer, tente fechar e reabrir o projeto.

## <a name="unity-render-pipelines"></a>Pipelines de renderização do Unity

A renderização remota funciona com o **:::no-loc text="Universal render pipeline":::** e o **:::no-loc text="Standard render pipeline":::** . Por motivos de desempenho, o pipeline de processamento universal é recomendado.

Para usar o **:::no-loc text="Universal render pipeline":::** , seu pacote deve ser instalado no Unity. Isso pode ser feito na interface do usuário do **Gerenciador de pacotes** do Unity (nome do pacote **Universal RP**, versão 7.3.1 ou mais recente) ou por meio do `Packages/manifest.json` arquivo, conforme descrito no [tutorial de configuração do projeto do Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Próximas etapas

* [Componentes e objetos de jogos do Unity](objects-components.md)
* [Tutorial: exibir modelos remotos](../../tutorials/unity/view-remote-models/view-remote-models.md)
