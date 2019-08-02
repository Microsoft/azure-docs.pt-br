---
title: Executar as instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implantar o contêiner de Face em uma instância de contêiner do Azure e testá-lo em um navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: e67846b6b304b5425f7e8334eb3a4499a029d5ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711588"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Implantar o contêiner de Face em instâncias de contêiner do Azure

Saiba como implantar os serviços Cognitivos [Face](../face-how-to-install-containers.md) contêiner do Azure [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso de detecção facial do Azure. Em seguida, discutiremos a efetuar pull da imagem de contêiner associado. Por fim, podemos destacar a capacidade de exercitar a orquestração dos dois em um navegador. Usando contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]