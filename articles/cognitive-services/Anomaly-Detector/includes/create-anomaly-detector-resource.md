---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: feb79d047a6c3b25176a13dcc3c3afd53a51459e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444734"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias

1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.
1. Selecione <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">criar recurso de detector de anomalias</a> .
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` -10 chamadas por segundo, transações de 20 mil por mês. <br> Ou:<br> `S0` -80 chamadas por segundo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia chaves e ponto de extremidade no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Chaves**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



