---
title: Atualização de dispositivo para requisitos de rede do Hub IoT | Microsoft Docs
description: A atualização de dispositivo para o Hub IoT usa uma variedade de portas de rede para finalidades diferentes.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678986"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Portas usadas com a atualização de dispositivo para o Hub IoT
O ADU usa uma variedade de portas de rede para finalidades diferentes.

## <a name="default-ports"></a>Portas padrão

Finalidade|Número da porta |
---|---
Baixar de redes/CDNs  | 80 (protocolo HTTP)
Baixar do MCC/CDNs | 80 (protocolo HTTP)
Conexão do agente do ADU com o Hub IoT do Azure  | 8883 (protocolo MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Usar protocolos com suporte do Hub IoT do Azure
O agente do ADU pode ser modificado para usar qualquer um dos protocolos de Hub IoT do Azure com suporte.

[Saiba mais](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) sobre a lista atual de protocolos com suporte.
