---
title: 'Início Rápido: Criar um aplicativo Android'
description: Neste Início Rápido, você aprenderá a criar um aplicativo para Android usando as Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b7be4e257fc884c655fe380f69b08797a907fbbb
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022642"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Início Rápido: Criar um aplicativo para Android com as Âncoras Espaciais do Azure

Este início rápido aborda como criar um aplicativo Android usando [Âncoras Espaciais do Azure](../overview.md) em Java ou C++/NDK. As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo do ARCore para Android que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que:

- Um computador Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Se estiver executando no Windows, você também precisará do <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e do <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se estiver executando no macOS, instale o Git por meio do HomeBrew. Insira o seguinte comando em uma única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git` e `brew install git-lfs`.
  - Para compilar o exemplo NDK, você também precisará instalar o NDK e o CMake 3.6 ou superior das SDK Tools no Android Studio.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">compatível com ARCore</a>.
  - Drivers de dispositivo adicionais podem ser necessários para que seu computador se comunique com seu dispositivo Android. Confira [aqui](https://developer.android.com/studio/run/device.html) para obter informações e instruções adicionais.
- Seu aplicativo deve ser destinado ao ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Baixe o `arcore_c_api.h`[daqui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) e coloque-o em `Android\NDK\libraries\include`.

No repositório recém clonado, inicialize os submódulos executando o seguinte comando:

```console
git submodule update --init --recursive
```

---

Abra o Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Selecione **Abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/Java/`.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Selecione **Abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Abra o `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

Localize o campo `SpatialAnchorsAccountDomain` e substitua `Set me` pelo domínio da conta.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Abra o `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

Localize o campo `SpatialAnchorsAccountDomain` e substitua `Set me` pelo domínio da conta.

---

## <a name="deploy-the-app-to-your-android-device"></a>Implantar o aplicativo em seu dispositivo Android

Ligue o dispositivo Android, entre nele e conecte-o ao computador usando um cabo USB.

Selecione **Executar** na barra de ferramentas do Android Studio.

![Botão Implantar e Executar do Android Studio](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android na caixa de diálogo **Selecionar o Destino de Implantação** e selecione **OK** para executar o aplicativo no dispositivo Android.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

Interrompa o aplicativo, selecionando **Parar** na barra de ferramentas do Android Studio.

![Botão Parar do Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
