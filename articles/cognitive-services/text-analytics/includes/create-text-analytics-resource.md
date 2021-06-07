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
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750356"
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
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de caracteres alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
