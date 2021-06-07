---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88653861"
---
<!--Create a media services asset REST-->

O comando do Azure .NET a seguir cria um novo ativo de serviços de mídia. Substitua os valores `subscriptionID` , `resourceGroup` e `amsAccountName` pelos valores com os quais você está trabalhando no momento. Dê um nome ao seu ativo Configurando `assetName` aqui.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
