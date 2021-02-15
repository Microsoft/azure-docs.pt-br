---
title: Solução de problemas do Hub IoT do Azure erro 404104 DeviceConnectionClosedRemotely
description: Entenda como corrigir o erro 404104 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538248"
---
# <a name="404104-deviceconnectionclosedremotely"></a>DeviceConnectionClosedRemotely 404104

Este artigo descreve as causas e soluções para erros de **404104 DeviceConnectionClosedRemotely** .

## <a name="symptoms"></a>Sintomas

### <a name="symptom-1"></a>Sintoma 1

Os dispositivos se desconectam em intervalos regulares (a cada 65 minutos, por exemplo) e você vê **404104 DeviceConnectionClosedRemotely** nos logs de recursos do Hub IOT. Às vezes, você também vê **401003 IoTHubUnauthorized** e um evento de conexão de dispositivo bem-sucedido menos de um minuto depois.

### <a name="symptom-2"></a>Sintoma 2

Os dispositivos desconectam-se aleatoriamente e você vê **404104 DeviceConnectionClosedRemotely** em logs de recursos do Hub IOT.

### <a name="symptom-3"></a>Sintoma 3

Muitos dispositivos desconectam ao mesmo tempo, você vê um DIP na [métrica de dispositivos conectados (connectedDeviceCount)](monitor-iot-hub-reference.md)e há mais erros de **404104 DeviceConnectionClosedRemotely** e [500xxx internos](iot-hub-troubleshoot-error-500xxx-internal-errors.md) em logs de Azure monitor do que o normal.

## <a name="causes"></a>Causas

### <a name="cause-1"></a>Causa 1

O [token SAS usado para se conectar ao Hub IOT](iot-hub-devguide-security.md#security-tokens) expirou, o que faz com que o Hub IOT desconecte o dispositivo. A conexão é restabelecida quando o token é atualizado pelo dispositivo. Por exemplo, [o token SAS expira a cada hora por padrão para o SDK do C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), o que pode levar a desconexões regulares.

Para saber mais, confira [401003 IoTHubUnauthorized causa](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Causa 2

Algumas possibilidades incluem:

- O dispositivo perdeu a conectividade de rede subjacente por mais tempo do que o [MQTT Keep-Alive](iot-hub-mqtt-support.md#default-keep-alive-timeout), resultando em um tempo limite de ociosidade remoto. A configuração Keep-Alive MQTT pode ser diferente por dispositivo.

- O dispositivo enviou uma redefinição de nível de TCP/IP, mas não enviou um nível de aplicativo `MQTT DISCONNECT` . Basicamente, o dispositivo fechou abruptamente a conexão de soquete subjacente. Às vezes, esse problema é causado por bugs em versões mais antigas do SDK do Azure IoT.

- O aplicativo do lado do dispositivo falhou.

### <a name="cause-3"></a>Causa 3

O Hub IoT pode estar enfrentando um problema transitório. Consulte [causa de erro do servidor interno do Hub IOT](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Soluções

### <a name="solution-1"></a>Solução 1

Consulte a [solução de 401003 IoTHubUnauthorized 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Solução 2

- Verifique se o dispositivo tem boa conectividade com o Hub IoT [testando a conexão](tutorial-connectivity.md). Se a rede não for confiável ou intermitente, não é recomendável aumentar o valor Keep-Alive porque isso pode resultar na detecção (via Azure Monitor alertas, por exemplo) demorando mais. 

- Use as versões mais recentes dos [SDKs de IOT](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Solução 3

Consulte [soluções para erros de servidor interno do Hub IOT](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Próximas etapas

É recomendável usar os SDKs do dispositivo IoT do Azure para gerenciar conexões de forma confiável. Para saber mais, consulte [gerenciar conectividade e mensagens confiáveis usando os SDKs do dispositivo do Hub IOT do Azure](iot-hub-reliability-features-in-sdks.md)