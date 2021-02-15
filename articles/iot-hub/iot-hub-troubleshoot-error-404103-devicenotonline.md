---
title: Solução de problemas do Hub IoT do Azure erro 404103 DeviceNotOnline
description: Entenda como corrigir o erro 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960809"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Este artigo descreve as causas e soluções para erros de **404103 DeviceNotOnline** .

## <a name="symptoms"></a>Sintomas

Um método direto para um dispositivo falha com o erro **404103 DeviceNotOnline** mesmo que o dispositivo esteja online. 

## <a name="cause"></a>Causa

Se você souber que o dispositivo está online e ainda receber o erro, é provável que o retorno de chamada do método direto não esteja registrado no dispositivo.

## <a name="solution"></a>Solução

Para configurar o dispositivo corretamente para retornos de chamada de método direto, consulte [manipular um método direto em um dispositivo](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).