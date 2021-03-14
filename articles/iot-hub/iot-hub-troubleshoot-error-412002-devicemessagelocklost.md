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
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435470"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Este artigo descreve as causas e soluções para erros de **412002 DeviceMessageLockLost** .

## <a name="symptoms"></a>Sintomas

Ao tentar enviar uma mensagem da nuvem para o dispositivo, a solicitação falha com o erro **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Causa

Quando um dispositivo recebe uma mensagem da nuvem para o dispositivo da fila (por exemplo, usando [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ), a mensagem é bloqueada pelo Hub IOT por uma duração de tempo limite de bloqueio de um minuto. Se o dispositivo tentar concluir a mensagem depois que o tempo limite de bloqueio expirar, o Hub IoT lançará essa exceção.

## <a name="solution"></a>Solução

Se o Hub IoT não obtiver a notificação dentro da duração do tempo limite de bloqueio de um minuto, ele definirá a mensagem de volta para o estado *enfileirado* . O dispositivo pode tentar receber a mensagem novamente. Para evitar que o erro ocorra no futuro, implemente a lógica do lado do dispositivo para concluir a mensagem dentro de um minuto após o recebimento da mensagem. Esse tempo limite de um minuto não pode ser alterado.