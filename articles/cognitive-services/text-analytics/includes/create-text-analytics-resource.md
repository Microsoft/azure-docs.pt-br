---
title: Criar um recurso de Análise de Texto dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009837"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto dos Serviços Cognitivos

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** e vá para **IA + Machine Learning** > **Análise de Texto**.
   Ou vá para [criar análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Insira um nome (2 a 64 caracteres).|
    |Assinatura|Selecione a assinatura apropriada.|
    |Localização|Selecione uma localização próxima.|
    |Tipo de preço| Insira **S**, o tipo de preço padrão.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|

1. Selecione **Criar** e aguarde até que o recurso seja criado. O navegador redireciona automaticamente para a página de recursos recém-criada.
1. Coletar o configurado `endpoint` e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de caracteres alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
