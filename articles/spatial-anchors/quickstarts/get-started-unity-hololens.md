---
title: 'Início Rápido: criar um aplicativo HoloLens com o Unity'
description: Neste Início Rápido, você aprenderá a criar um aplicativo HoloLens com Unity usando as Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b99c2efcb746a056adc76fdc161c8d8a8e2799e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670113"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Início Rápido: criar um aplicativo Unity para HoloLens que usa as Âncoras Espaciais do Azure

Neste início rápido, você criará um aplicativo Unity para HoloLens que usa as [Âncoras Espaciais do Azure](../overview.md). Âncoras Espaciais são um serviço de desenvolvedor de plataforma cruzada que permite criar experiências de realidade misturada com objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando terminar, você terá um aplicativo HoloLens criado com o Unity que pode salvar e fazer recall de uma âncora espacial.

Você aprenderá a:

- Criar uma conta de Âncoras Espaciais.
- Preparar as configurações de criação do Unity.
- Configurar a chave de conta e o identificador da conta das Âncoras Espaciais.
- Exportar o projeto do Visual Studio do HoloLens.
- Implantar o aplicativo e executá-lo em um dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- É necessário ter um computador Windows com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity (LTS)</a> e o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ou posterior instalados. Use o **Unity 2020 LTS** com o SDK do ASA versão 2.9 ou posterior (que usa a [Estrutura de Plug-in de XR do Unity](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)) ou o **Unity 2019 LTS** com o SDK do ASA versão 2.8 ou anterior. A instalação do Visual Studio deverá incluir a carga de trabalho de **Desenvolvimento com a Plataforma Universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** . Além disso, será necessário instalar o <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e o <a href="https://git-lfs.github.com/">Git LFS</a>.
- Você precisará de um dispositivo do HoloLens com o [modo de desenvolvedor](/windows/mixed-reality/using-visual-studio) habilitado. A [Atualização do Windows de 10 de maio de 2020](/windows/mixed-reality/whats-new/release-notes-may-2020) precisa estar instalada no dispositivo. Para atualizar para a versão mais recente do HoloLens, abra o aplicativo de **Configurações**, acesse **Atualização e Segurança** e, em seguida, selecione **Verificar atualizações**.
- No aplicativo, é necessário habilitar o recurso **SpatialPerception**. Essa configuração está em **Configurações de Build** > **Configurações do Player** > **Configurações de Publicação** > **Recursos**.
- No seu aplicativo, é necessário habilitar **Realidade Virtual com Suporte** com **SDK de Realidade Misturada do Windows**. Esta configuração está em **Configurações de Build** > **Configurações do Player** > **Configurações XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixe e abra o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Abra **Configurações de Build** selecionando **Arquivo** > **Configurações de Build**.

Na seção **Plataforma**, selecione **Plataforma Universal do Windows**. Altere o **Dispositivo de Destino** para **HoloLens**.

Selecione **Alternar Plataforma** para alterar a plataforma para a **Plataforma Universal do Windows**. O Unity poderá solicitar que você instale componentes de suporte UWP, se estiverem faltando.

![Janela Configurações de Build do Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Feche a janela **Configurações de Build**.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto do Visual Studio do HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Compilar**. Na caixa de diálogo, selecione uma pasta para exportar o projeto do Visual Studio do HoloLens.

Quando a exportação estiver concluída, uma pasta contendo o projeto HoloLens exportado será exibida.

## <a name="deploy-the-hololens-application"></a>Implantar o aplicativo HoloLens

Na pasta, clique duas vezes em **HelloAR U3D.sln** para abrir o projeto no Visual Studio.

Altere a **Configuração da Solução** para **Lançamento**, altere a **Plataforma da Solução** para **x86** e selecione **Dispositivo** das opções de destino de implantação.

Se estiver usando o HoloLens 2, use o **ARM64** como a **Plataforma de Solução**, em vez de **x86**.

   ![Configuração do Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre e conecte o dispositivo ao computador usando um cabo USB.

Selecione **Depurar** > **Iniciar depuração** para implantar seu aplicativo e iniciar a depuração.

No aplicativo, selecione **BasicDemo** usando as setas e pressione o botão **Ir!** para executar a demonstração. Siga as instruções para colocar uma âncora e fazer recall dela.

![Captura de tela 1](./media/get-started-unity-hololens/screenshot-1.jpg)
![Captura de tela 2](./media/get-started-unity-hololens/screenshot-2.jpg)
![Captura de tela 3](./media/get-started-unity-hololens/screenshot-3.jpg)
![Captura de tela 4](./media/get-started-unity-hololens/screenshot-4.jpg)

No Visual Studio, pare o aplicativo selecionando **Parar Depuração** ou Shift+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Como configurar as Âncoras Espaciais do Azure em um projeto do Unity](../how-tos/setup-unity-project.md)