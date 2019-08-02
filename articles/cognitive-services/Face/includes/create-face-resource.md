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
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717137"
---
## <a name="create-an-face-resource"></a>Criar um recurso de detecção facial

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [Create **Face** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) recursos
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Assinatura|Selecione a assinatura apropriada|
    |Location|Selecione qualquer local disponível e próximo|
    |Camada de preços|`F0` -o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **criar** e aguarde até que o recurso a ser criado. Depois que ele é criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chaves**|Chave de API|Copie 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 caracteres sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
