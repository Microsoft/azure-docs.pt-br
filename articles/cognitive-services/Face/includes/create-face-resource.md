---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878351"
---
## <a name="create-an-face-resource"></a>Criar um recurso facial

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [criar recurso **facial** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chaves**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
