---
title: Telemetria de conexão do serviço de emparelhamento do Azure
description: Saiba mais sobre a telemetria de conexão de serviço de emparelhamento Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84871296"
---
# <a name="peering-service-connection-telemetry"></a>Telemetria de conexão de serviço de emparelhamento

A telemetria de conexão fornece informações coletadas para a conectividade entre o local do cliente e a rede da Microsoft. Os clientes podem obter telemetria para a conexão do serviço de emparelhamento do Azure registrando a conexão no portal do Azure. Esse recurso fornece segurança de prefixo e informações sobre o desempenho de rede.


A telemetria de conexão é composta pelos seguintes escopos:

### <a name="latency-measurement"></a>Medição de latência

 A latência é medida do cliente para o PoP do Microsoft Edge para os prefixos registrados no serviço de emparelhamento.

### <a name="route-prefix-monitoring-and-protection"></a>Monitoramento e proteção de prefixo de rota

Os caminhos de roteamento são monitorados para qualquer atividade suspeita que, em seguida, é capturada nos logs de eventos. Por exemplo, os logs de eventos são criados para alguns desses fatores:

- Seqüestros de prefixo
- Retirada de prefixo
- Vazamentos de rota

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão do Serviço de Emparelhamento, confira [Conexão do Serviço de Emparelhamento](connection.md).
- Para carregar uma conexão de serviço de emparelhamento, consulte [integração de um modelo de serviço de emparelhamento](onboarding-model.md).
- Para medir a telemetria, confira [Medir telemetria de conexão](measure-connection-telemetry.md).
