---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172024"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender as ações de gerenciamento do dispositivo

As soluções de IoT podem expandir o conjunto definido de padrões de gerenciamento do dispositivo ou habilitar padrões personalizados usando o dispositivo gêmeo e os primitivos do método da nuvem para o dispositivo. Outros exemplos de ações de gerenciamento do dispositivo incluem a redefinição de fábrica, atualização do firmware, atualização do software, gerenciamento de energia, gerenciamento da rede e da conectividade, e criptografia dos dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, você pode configurar os dispositivos para executar ações em um horário que minimiza as interrupções e a inatividade. As janelas de manutenção do dispositivo são um padrão usado para definir a hora em que um dispositivo deve atualizar sua configuração. As soluções de back-end podem usar as propriedades desejadas do dispositivo gêmeo para definir e ativar uma política no dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política da janela de manutenção, ele pode usar a propriedade relatada do dispositivo gêmeo para informar o status da política. O aplicativo de back-end pode usar as consultas do dispositivo gêmeo para atestar a conformidade dos dispositivos e cada política.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um método direto para disparar uma reinicialização remota em um dispositivo. Você usou as propriedades relatadas para relatar a hora da última reinicialização do dispositivo e consultou o dispositivo gêmeo para descobrir a hora da última reinicialização do dispositivo na nuvem.

Para continuar a introdução ao IoT Hub e padrões de gerenciamento de dispositivo como remoto sobre a atualização de firmware de ar, consulte [como fazer uma atualização de firmware](../articles/iot-hub/tutorial-firmware-update.md)

Para saber como estender sua solução e agendar chamadas de método em vários dispositivos de IoT, consulte [agendar e difundir trabalhos](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).