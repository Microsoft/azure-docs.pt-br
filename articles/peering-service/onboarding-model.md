---
title: Modelo de integração do serviço de emparelhamento do Azure
description: Saiba mais sobre como carregar o serviço de emparelhamento do Azure
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871396"
---
# <a name="onboarding-peering-service-model"></a>Modelo de serviço de emparelhamento de integração

O processo de integração do serviço de emparelhamento é composto de dois modelos, conforme listado abaixo:

 - Conexão do serviço de emparelhamento de integração

 - Telemetria de conexão de serviço de emparelhamento

Os planos de ação para os modelos listados acima são descritos abaixo:

| **Step** | **Ação**| **O que você obtém**|
|-----------|---------|---------|---------|
|1|Cliente para provisionar a conectividade de um parceiro de conectividade (sem interação com a Microsoft). |Um provedor de Internet que está bem conectado à Microsoft e atende aos requisitos técnicos para uma conectividade de alto desempenho e confiável à Microsoft.  |
|2 (opcional)|O cliente registra os locais no portal do Azure. Um local é definido por: nome do ISP/IXP, local físico do site do cliente (nível do estado), prefixo de IP fornecido para o local pelo provedor de serviços ou pela empresa.  |Telemetria: monitoramento de rota de Internet, priorização de tráfego da Microsoft para o local POP de borda mais próximo do usuário. |



## <a name="onboarding-peering-service-connection"></a>Conexão do serviço de emparelhamento de integração

Para carregar a conexão do serviço de emparelhamento, faça o seguinte:

Trabalhe com o Internet Service Provider (ISP) ou Internet Exchange (IX) Partner para obter o serviço de emparelhamento para conectar sua rede com a rede da Microsoft.

Verifique se os [provedores de conectividade](location-partners.md) estão em parceria com a Microsoft para o serviço de emparelhamento. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Telemetria de conexão de serviço de emparelhamento

Os clientes podem optar por sua telemetria, como a análise de rota BGP para monitorar a latência e o desempenho da rede ao acessar a rede da Microsoft. Isso pode ser feito registrando a conexão no portal do Azure.

Para integrar a telemetria de conexão de serviço de emparelhamento, o cliente deve registrar a conexão de serviço de emparelhamento no portal do Azure. Consulte o [serviço de emparelhamento de registro-portal do Azure](azure-portal.md) para aprender o procedimento.

Depois disso, você pode medir a telemetria fazendo referência [aqui](measure-connection-telemetry.md).

## <a name="next-steps"></a>Próximas etapas

Para aprender o processo passo a passo sobre como registrar a conexão de serviço de emparelhamento, consulte [registrar serviço de emparelhamento-portal do Azure](azure-portal.md).

Para saber mais sobre como medir a telemetria de conexão, consulte [medir a telemetria de conexão](measure-connection-telemetry.md).
