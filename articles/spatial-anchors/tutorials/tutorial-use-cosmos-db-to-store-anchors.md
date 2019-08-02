---
title: Tutorial – Compartilhar Âncoras Espaciais do Azure entre sessões e dispositivos com um back-end do Azure Cosmos DB | Microsoft Docs
description: Neste tutorial, você aprenderá a compartilhar os identificadores de Âncoras Espaciais do Azure entre os dispositivos Android/iOS no Unity com um serviço de back-end e Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: fc172b5327d72687fea7d13ddb706ecc7ab630b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135313"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Compartilhar Âncoras Espaciais do Azure entre sessões e dispositivos com um back-end do Azure Cosmos DB

Neste tutorial, você aprenderá a usar [Âncoras Espaciais do Azure](../overview.md) para criar âncoras durante uma sessão e, em seguida, localizá-las durante outra sessão, no mesmo dispositivo ou em um diferente. Por exemplo, a segunda sessão pode ser em outro dia. Essas mesmas âncoras também poderiam ser localizadas por vários dispositivos no mesmo lugar e ao mesmo tempo.

![Persistência de objetos que ilustram GIF](./media/persistence.gif)

As [Âncoras Espaciais do Azure](../overview.md) são um serviço de desenvolvedor multiplataforma em que você pode criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo que pode ser implantado em dois ou mais dispositivos. Âncoras espaciais do Azure criadas por uma instância compartilhará seus identificadores com outros usando o Azure Cosmos DB.

Você aprenderá a:

> [!div class="checklist"]
> * Implante um aplicativo Web do ASP.NET Core no Azure que possa ser usado para compartilhar âncoras, armazenando-as no Azure Cosmos DB.
> * Configure a cena AzureSpatialAnchorsLocalSharedDemo dentro do exemplo do Unity dos inícios rápidos para aproveitar o aplicativo Web de Âncoras de Compartilhamento.
> * Implante um aplicativo ou mais dispositivos e execute-os.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Vale a pena lembrar que, embora você vá usar o Unity e o Azure Cosmos DB neste tutorial, será apenas um exemplo de como compartilhar identificadores de Âncoras Espaciais do Azure em outros dispositivos. Você poderá usar outras linguagens e tecnologias de back-end para alcançar a mesma meta. Além disso, o aplicativo Web do ASP.NET Core usado neste tutorial tem uma dependência do SDK 2.2 do .NET Core. Ele é executado bem em aplicativos Web para Windows, mas atualmente ele não será executado nos aplicativos Web para Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie o `Connection String` porque você precisará.

## <a name="download-the-unity-sample-project"></a>Baixe o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Implante o serviço de compartilhamento de âncoras

Abra o Visual Studio e abra o projeto na pasta `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Configure o serviço para usar seu banco de dados do Azure Cosmos DB

No **Gerenciador de Soluções**, abra `SharingService\Startup.cs`.

Localize `#define INMEMORY_DEMO` na parte superior do arquivo e comente-a. Salve o arquivo.

No **Gerenciador de Soluções**, abra `SharingService\appsettings.json`.

Localize a propriedade `StorageConnectionString` e defina o valor para que seja o mesmo `Connection String` que você copiou na [etapa criar uma conta de banco de dados](#create-a-database-account). Salve o arquivo.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Azure Cosmos DB para compartilhar identificadores de âncora entre dispositivos. Para saber mais sobre como usar Âncoras Espaciais do Azure em um novo aplicativo Unity HoloLens, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Como iniciar um novo aplicativo Android](./tutorial-new-unity-hololens-app.md)