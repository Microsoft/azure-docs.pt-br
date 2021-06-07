---
title: Solução de problemas do Hub IoT do Azure erro 409002 LinkCreationConflict
description: Entenda como corrigir o erro 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 70b9be6fdb500d9f877659a12e6fdc0e206ea964
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538214"
---
# <a name="409002-linkcreationconflict"></a>LinkCreationConflict 409002

Este artigo descreve as causas e soluções para erros de **409002 LinkCreationConflict** .

## <a name="symptoms"></a>Sintomas

Você verá o erro **409002 LinkCreationConflict** em logs junto com a desconexão do dispositivo ou falha na mensagem da nuvem para o dispositivo.

<!-- When using AMQP? -->

## <a name="cause"></a>Causa

Em geral, esse erro ocorre quando o Hub IoT detecta que um cliente tem mais de uma conexão. Na verdade, quando uma nova solicitação de conexão chega para um dispositivo com uma conexão existente, o Hub IoT fecha a conexão existente com esse erro.

### <a name="cause-1"></a>Causa 1

No caso mais comum, um problema separado (como [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) faz com que o dispositivo se desconecte. O dispositivo tenta restabelecer a conexão imediatamente, mas o Hub IoT ainda considera o dispositivo conectado. O Hub IoT fecha a conexão anterior e registra esse erro.

### <a name="cause-2"></a>Causa 2

A lógica do lado do dispositivo com falha faz com que o dispositivo estabeleça a conexão quando uma já estiver aberta.

## <a name="solution"></a>Solução

Esse erro geralmente aparece como um efeito colateral de um problema diferente e transitório, portanto, procure outros erros nos logs para solucionar problemas adicionais. Caso contrário, certifique-se de emitir uma nova solicitação de conexão apenas se a conexão cair.
