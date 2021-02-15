---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876059"
---
## <a name="create-an-computer-vision-resource"></a>Criar um recurso de Pesquisa Visual Computacional

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Clique em [criar **Pesquisa Visual computacional** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) recurso.
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Após a criação dele, navegue até a página do recurso.
1. Coletar configurado `{ENDPOINT_URI}` e `{API_KEY}` , consulte [coletando parâmetros necessários](../computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter detalhes.
