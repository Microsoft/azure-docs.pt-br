---
title: Executar as instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implantar o contêiner de LUIS para uma instância de contêiner do Azure e testá-lo em um navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711566"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implantar o contêiner de compreensão de idioma (LUIS) nas instâncias de contêiner do Azure

Saiba como implantar os serviços Cognitivos [LUIS](luis-container-howto.md) contêiner do Azure [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso do Detector de anomalias. Em seguida, discutiremos a efetuar pull da imagem de contêiner associado. Por fim, podemos destacar a capacidade de exercitar a orquestração dos dois em um navegador. Usando contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
