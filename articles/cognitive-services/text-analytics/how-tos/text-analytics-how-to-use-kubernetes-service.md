---
title: Executar serviço kubernetes do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante a imagem de contêiner Análise de Texto no serviço kubernetes do Azure e teste-a em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fb33bd4fe70c51e1e2afffece10ba3b29dcb0450
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932440"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Implantar um contêiner de Análise de Texto no serviço kubernetes do Azure

Saiba como implantar os serviços cognitivas do Azure [análise de texto](./text-analytics-how-to-install-containers.md) a imagem de contêiner no AKs (serviço kubernetes do Azure). Este procedimento mostra como criar um recurso de Análise de Texto, como criar uma imagem de análise de opiniões associada e como exercitar essa orquestração dos dois em um navegador. Usar contêineres pode mudar sua atenção para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use Azure Cloud Shell. Você precisará do seguinte:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.
* Um editor de texto, por exemplo, [Visual Studio Code](https://code.visualstudio.com/download).
* O [CLI do Azure](/cli/azure/install-azure-cli) instalado.
* A [CLI do kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalada.
* Um recurso do Azure com o tipo de preço correto. Nem todos os tipos de preços funcionam com esse contêiner:
    * O recurso **análise de texto do Azure** somente com tipos de preço F0 ou Standard.
    * Recurso de **Serviços cognitivas do Azure** com o tipo de preço S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

**_

## <a name="next-steps"></a>Próximas etapas

_ Usar mais [contêineres de serviços cognitivas](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../index.yml)
