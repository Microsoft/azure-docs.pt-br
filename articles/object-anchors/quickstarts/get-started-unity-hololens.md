---
title: 'Início Rápido: criar um aplicativo HoloLens com o Unity'
description: Neste início rápido, você aprenderá a criar um aplicativo do HoloLens no Unity usando as Âncoras de Objeto.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 4f85a258042430d58690ef578db6d21a6c831d50
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044537"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>Início rápido: criar um aplicativo do HoloLens com as Âncoras de Objeto do Azure, no Unity

Neste início rápido, você criará um aplicativo do HoloLens no Unity que usa as [Âncoras de Objeto do Azure](../overview.md). As Âncoras de Objeto do Azure são um serviço de nuvem gerenciado que converte ativos 3D em modelos de IA que habilitam experiências de realidade misturada com reconhecimento de objeto para o HoloLens. Quando tiver terminado, você terá um aplicativo do HoloLens criado com o Unity que poderá detectar objetos no mundo físico.

Você aprenderá a:

> [!div class="checklist"]
> * Preparar as configurações de criação do Unity.
> * Exportar o projeto do Visual Studio do HoloLens.
> * Implantar o aplicativo e executá-lo em um dispositivo do HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

No Unity, abra o projeto `quickstarts/apps/unity/basic`.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Ative o dispositivo, selecione **Todos os aplicativos** e depois localize e inicie o aplicativo. Após a tela inicial do Unity, você verá uma mensagem indicando que o Observador do Objeto foi inicializado. No entanto, você precisará adicionar seu modelo ao aplicativo.

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

O aplicativo procura objetos no campo de exibição atual e, uma vez detectados, rastreia esses objetos. Uma instância será removida quando estiver a 6 metros de distância da localização do usuário. O texto de depuração mostra detalhes sobre uma instância, como a ID, o carimbo de data/hora atualizado e a taxa de cobertura da superfície.

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [HoloLens no Unity com MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [Conceitos: visão geral do SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [perguntas frequentes](../faq.md)
