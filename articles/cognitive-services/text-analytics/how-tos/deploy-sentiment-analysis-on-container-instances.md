---
title: Executar instâncias de contêiner do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante os contêineres de análise de texto com a imagem de análise de sentimentos, para a instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552521"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Implantar um contêiner de Análise de Sentimento nas instâncias de contêiner do Azure

Saiba como implantar o contêiner de [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) de serviços cognitivas com a imagem de análise de sentimento para [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Esse procedimento exemplifica a criação de um recurso de Análise de Texto, a criação de uma imagem de Análise de Sentimento associada e a capacidade de exercitar essa orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Use uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Próximas etapas 

* Use mais [Contêineres de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../vs-text-connected-service.md)
