---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89055356"
---
### <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No portal do Azure, no recurso do Personalizador na página **Configuração**, altere a **Frequência de atualização de modelo** para 10 segundos. Essa duração curta treinará o serviço rapidamente, permitindo que você veja como a ação superior é alterada a cada iteração.

![Alterar a frequência de atualização do modelo](../media/settings/configure-model-update-frequency-settings.png)

Quando o loop do Personalizador é instanciado pela primeira vez, não há modelo, pois não houve nenhuma chamada à API de Recompensa com base na qual treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve classificar sempre o conteúdo usando a saída de RewardActionId.