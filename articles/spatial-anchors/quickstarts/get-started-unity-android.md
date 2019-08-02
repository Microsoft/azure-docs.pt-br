---
title: Início Rápido – Criar um aplicativo para Unity Android com as Âncoras Espaciais do Azure | Microsoft Docs
description: Neste Início Rápido, você aprenderá a criar um aplicativo do Android com o Unity usando as Âncoras Espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 642abfb99b40d67802b7194ad225ebcd2872a72b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135058"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo para Unity Android com as Âncoras Espaciais do Azure

Este Início Rápido aborda como criar um aplicativo para Unity Android usando as [Âncoras Espaciais do Azure](../overview.md). As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando terminar, você terá um aplicativo do ARCore Android criado com o Unity que pode salvar e fazer recall de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Preparar as configurações de build do Unity
> * Baixar e importar o SDK do ARCore para Unity
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Exportar o projeto do Android Studio
> * Implantar e executar um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

- Um computador Windows ou macOS com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 ou posterior</a> e o <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 ou posterior</a>.
  - Se estiver executando no Windows, você também precisará do <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
  - Se estiver executando no macOS, instale o Git por meio do HomeBrew. Digite o comando a seguir em uma única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.
- Seu aplicativo deve usar a versão **1.7** do SDK ARCore para Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixe e abra o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

No painel **Projeto**, navegue para `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo de cena `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **Arquivo** -> **Salvar**.

## <a name="export-the-android-studio-project"></a>Exportar o projeto do Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Verifique se a caixa de seleção **Exportar Projeto** não tem uma marca de seleção. Clique em **Compilar e Executar**. Você será solicitado a salvar seu arquivo `.apk`; você pode escolher qualquer nome para ele.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

> [!NOTE]
> Ao executar o aplicativo, se a câmera não for exibida como a tela de fundo (por exemplo, é exibida uma tela em branco, azul ou outras texturas), você provavelmente precisará reimportar os ativos no Unity. Interrompa o aplicativo. No menu superior do Unity, escolha **Ativos -> Reimportar todos**. Em seguida, execute o aplicativo novamente.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
