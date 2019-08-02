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
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717184"
---
## <a name="create-a-luis-resource"></a>Criar um recurso do LUIS

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [criar **compreensão de idioma**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
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
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Chaves**|Chave de API|Copie 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 caracteres sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
