---
title: Solução de problemas do Hub IoT do Azure erro 403002 IoTHubQuotaExceeded
description: Entenda como corrigir o erro 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "76961108"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Este artigo descreve as causas e soluções para erros de **403002 IoTHubQuotaExceeded** .

## <a name="symptoms"></a>Sintomas

Todas as solicitações ao Hub IoT falham com o erro  **403002 IoTHubQuotaExceeded**. No portal do Azure, a lista de dispositivos do Hub IoT não é carregada.

## <a name="cause"></a>Causa

A cota de mensagem diária para o Hub IoT foi excedida. 

## <a name="solution"></a>Solução

[Atualize ou aumente o número de unidades no Hub IOT](iot-hub-upgrade.md) ou aguarde o próximo dia UTC para que a cota diária seja atualizada.

## <a name="next-steps"></a>Próximas etapas

* Para entender como as operações são contadas para a cota, como consultas de entrelaçamento e métodos diretos, consulte [entender os preços do Hub IOT](iot-hub-devguide-pricing.md#charges-per-operation)
* Para configurar o monitoramento para uso diário de cotas, configure um alerta com o *número total de mensagens usadas*. Para obter as instruções passo a passo, consulte [Configurar métricas e alertas com o Hub IOT](tutorial-use-metrics-and-diags.md#set-up-metrics)