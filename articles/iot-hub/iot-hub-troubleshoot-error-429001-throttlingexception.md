---
title: Solução de problemas do Hub IoT do Azure erro 429001 Limitaçãoexception
description: Entenda como corrigir o erro 429001 Limitaçãoexception
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d2f12a6982886eeaa375151c5b8a73acc573aab9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545354"
---
# <a name="429001-throttlingexception"></a>429001 ExceçãoLimitação

Este artigo descreve as causas e soluções para erros **429001 de limitaçãoexception** .

## <a name="symptoms"></a>Sintomas

Suas solicitações ao Hub IoT falham com o erro **429001 regulaexception** .

## <a name="cause"></a>Causa

[Os limites de limitação](./iot-hub-devguide-quotas-throttling.md) do Hub IOT foram excedidos para a operação solicitada.

## <a name="solution"></a>Solução

Verifique se você está atingindo o limite de limitação comparando sua métrica de *tentativas de envio de mensagem de telemetria* em relação aos limites especificados acima. Você também pode verificar a métrica *número de erros de limitação* . Para obter informações sobre essas métricas, consulte [métricas de telemetria do dispositivo](monitor-iot-hub-reference.md#device-telemetry-metrics). Para obter informações sobre como usar métricas para ajudá-lo a monitorar o Hub IoT, consulte [monitorar o Hub IOT](monitor-iot-hub.md).

O Hub IoT retorna 429 Throttlingexception somente depois que o limite tiver sido violado por um período muito longo. Isso é feito para que suas mensagens não sejam descartadas se o Hub IoT obtiver tráfego intermitente. Enquanto isso, o Hub IoT processa as mensagens na taxa de limitação da operação, o que pode ser lento se houver muito tráfego na lista de pendências. Para saber mais, consulte [modelagem de tráfego do Hub IoT](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Próximas etapas

Considere [escalar verticalmente o Hub IOT](./iot-hub-scaling.md) se você estiver executando limites de cota ou limitação.