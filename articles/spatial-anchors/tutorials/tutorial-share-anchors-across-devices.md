---
title: 'Tutorial: Compartilhar âncoras entre sessões e dispositivos'
description: Neste tutorial, você aprenderá como compartilhar identificadores de Âncora Espacial do Azure entre dispositivos Android/iOS no Unity com um serviço back-end.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3ed9b18144bb727af751fbcbbdd5cc6631afc40f
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105727222"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Compartilhar âncoras espaciais entre sessões e dispositivos

As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma com o qual você pode criar experiências de realidade misturada usando objetos que persistem sua localização entre dispositivos ao longo do tempo. 

Neste tutorial, você usa as [Âncoras Espaciais do Azure](../overview.md) para criar âncoras durante uma sessão e, depois, localizá-las, no mesmo dispositivo ou em um diferente. As mesmas âncoras também podem ser localizadas por vários dispositivos no mesmo lugar e ao mesmo tempo.

![Animação mostrando as âncoras espaciais criadas com um dispositivo móvel e usadas com um dispositivo diferente ao longo dos dias.](./media/persistence.gif)


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar um aplicativo Web ASP.NET Core no Azure que possa ser usado para compartilhar âncoras e armazenar as âncoras na memória por um período de tempo especificado.
> * Configurar a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity de nossos inícios rápidos para aproveitar o aplicativo Web de Compartilhamento de Âncoras.
> * Implantar e executar as âncoras em um ou mais dispositivos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> Você usará o Unity e um aplicativo Web ASP.NET Core neste tutorial, mas essa abordagem é apenas para fornecer um exemplo de como compartilhar identificadores de Âncoras Espaciais do Azure em outros dispositivos. Você pode usar outras linguagens e tecnologias de back-end para atingir o mesmo objetivo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Baixar o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Implantar o serviço de Compartilhamento de Âncoras

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Abra o Visual Studio e, depois, abra o projeto na pasta *Sharing\SharingServiceSample*.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Você precisa criar um grupo de recursos e um plano do Serviço de Aplicativo para implantar o serviço no Visual Studio Code.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Acesse o <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e entre em sua assinatura do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos como **myResourceGroup** e selecione **Ok**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Ao lado de **Plano de Hospedagem**, selecione **Novo**.

No painel **Configurar Plano de Hospedagem**, use essas configurações:

| Configuração | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicativo| MySharingServicePlan | Nome do plano do Serviço de Aplicativo |
| Location | Oeste dos EUA | O datacenter em que o aplicativo Web está hospedado |
| Tamanho | Grátis | O [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina os recursos de hospedagem |

Selecione **OK**.

Abra o Visual Studio Code e, depois, abra o projeto na pasta *Sharing\SharingServiceSample*. 

Para implantar o serviço de compartilhamento por meio do Visual Studio Code, siga as instruções em <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2&preserve-view=true#open-it-with-visual-studio-code" target="_blank">Publicar um aplicativo ASP.NET Core no Azure com Visual Studio Code</a>. Inicie na seção "Abrir com o Visual Studio Code". Não crie outro projeto ASP.NET, conforme explicado na etapa anterior, porque você já tem um projeto a ser implantado e publicado: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Implantar o aplicativo de exemplo

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um aplicativo Web do ASP.NET Core no Azure e, então, configurou e implantou um aplicativo Unity. Você criou âncoras espaciais com o aplicativo e as compartilhou com outros dispositivos usando seu aplicativo Web ASP.NET Core.

Você pode aprimorar o aplicativo Web ASP.NET Core para que ele use o Azure Cosmos DB para persistir o armazenamento dos identificadores de âncoras espaciais compartilhadas. Ao adicionar suporte ao Azure Cosmos DB, você pode fazer com que seu aplicativo Web ASP.NET Core crie uma âncora hoje mesmo. Em seguida, usando o identificador de âncora armazenado no aplicativo Web, você pode fazer com que o aplicativo retorne dias mais tarde para localizar a âncora novamente.

> [!div class="nextstepaction"]
> [Usar o Azure Cosmos DB para armazenar âncoras](./tutorial-use-cosmos-db-to-store-anchors.md)