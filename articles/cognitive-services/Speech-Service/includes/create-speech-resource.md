---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717267"
---
## <a name="create-a-speech-resource"></a>Criar um recurso de fala

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [Create **fala** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) recursos
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |NOME|Nome desejado (2 a 64 caracteres)|
    |Assinatura|Selecione a assinatura apropriada|
    |Location|Selecione qualquer local disponível e próximo|
    |Camada de preços|`F0` -o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **criar** e aguarde até que o recurso a ser criado. Depois que ele é criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Chaves**|Chave de API|Copie 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 caracteres sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
