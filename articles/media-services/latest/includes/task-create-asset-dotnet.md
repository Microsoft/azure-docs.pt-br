---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653861"
---
<!--Create a media services asset REST-->

O comando do Azure .NET a seguir cria um novo ativo de serviços de mídia. Substitua os valores `subscriptionID` , `resourceGroup` e `amsAccountName` pelos valores com os quais você está trabalhando no momento. Dê um nome ao seu ativo Configurando `assetName` aqui.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
