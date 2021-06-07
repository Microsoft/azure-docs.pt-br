---
title: Atualização de dispositivo para requisitos de rede do Hub IoT | Microsoft Docs
description: A atualização de dispositivo para o Hub IoT usa uma variedade de portas de rede para finalidades diferentes.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558373"
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

[Saiba mais](../iot-hub/iot-hub-devguide-protocols.md) sobre a lista atual de protocolos com suporte.
