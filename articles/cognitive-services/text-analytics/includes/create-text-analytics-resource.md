---
title: Criar um recurso de Análise de Texto de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de serviços cognitivas.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377434"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de serviços cognitivas

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso**e vá para **ia + Machine Learning** > **análise de texto**.
   Ou vá para [criar análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Insira um nome (2-64 caracteres).|
    |Assinatura|Selecione a assinatura apropriada.|
    |Location|Selecione um local próximo.|
    |Tipo de preço| Insira **S**, o tipo de preço Standard.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|

1. Selecione **criar**e aguarde até que o recurso seja criado. Seu navegador redireciona automaticamente para a página de recursos recém-criada.
1. Coletar o configurado `endpoint` e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de caracteres alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
