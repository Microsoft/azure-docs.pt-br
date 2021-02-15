---
title: Solução de problemas do Hub IoT do Azure erro 412002 DeviceMessageLockLost
description: Entenda como corrigir o erro 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144266"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para erros de **412002 DeviceMessageLockLost** .

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem da nuvem para o dispositivo, a solicitação falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem da nuvem para o dispositivo da fila (por exemplo, usando [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), a mensagem é bloqueada pelo Hub IOT por uma duração de tempo limite de bloqueio de um minuto. Se o dispositivo tentar concluir a mensagem depois que o tempo limite de bloqueio expirar, o Hub IoT lançará essa exceção.

## <a name="solution"></a>Solução

Se o Hub IoT não obtiver a notificação dentro da duração do tempo limite de bloqueio de um minuto, ele definirá a mensagem de volta para o estado *enfileirado* . O dispositivo pode tentar receber a mensagem novamente. Para evitar que o erro ocorra no futuro, implemente a lógica do lado do dispositivo para concluir a mensagem dentro de um minuto após o recebimento da mensagem. Esse tempo limite de um minuto não pode ser alterado.