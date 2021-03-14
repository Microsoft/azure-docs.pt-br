---
title: Práticas recomendadas de desenvolvimento de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo descreve as práticas recomendadas para conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: e8ae8b0173e53c0a46ded1a2690175e367997c9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054431"
---
# <a name="best-practices-for-device-development"></a>Práticas recomendadas para o desenvolvimento de dispositivos

*Este artigo se aplica a desenvolvedores de dispositivos.*

Essas recomendações mostram como implementar dispositivos para aproveitar a recuperação de desastre interna e o dimensionamento automático no IoT Central.

A lista a seguir mostra o fluxo de alto nível quando um dispositivo se conecta a IoT Central:

1. Use o DPS para provisionar o dispositivo e obter uma cadeia de conexão de dispositivo.

1. Use a cadeia de conexão para conectar o ponto de extremidade do Hub IoT interno do IoT Central. Envie dados para e receba dados do seu aplicativo IoT Central.

1. Se o dispositivo obtiver falhas de conexão, dependendo do tipo de erro, repita a conexão ou provisione o dispositivo.

## <a name="use-dps-to-provision-the-device"></a>Usar o DPS para provisionar o dispositivo

Para provisionar um dispositivo com o DPS, use a ID do escopo, as credenciais e a ID do dispositivo do seu aplicativo IoT Central. Para saber mais sobre os tipos de credenciais, consulte [registro de grupo X. 509](concepts-get-connected.md#x509-group-enrollment) e [registro de grupo SAS](concepts-get-connected.md#sas-group-enrollment). Para saber mais sobre as IDs de dispositivo, confira [registro de dispositivo](concepts-get-connected.md#device-registration).

Em caso de sucesso, o DPS retorna uma cadeia de conexão que o dispositivo pode usar para se conectar ao seu aplicativo IoT Central. Para solucionar problemas de erros de provisionamento, consulte [verificar o status de provisionamento do seu dispositivo](troubleshoot-connection.md#check-the-provisioning-status-of-your-device).

O dispositivo pode armazenar em cache a cadeia de conexão a ser usada para conexões posteriores. No entanto, o dispositivo deve estar preparado para [lidar com falhas de conexão](#handle-connection-failures).

## <a name="connect-to-iot-central"></a>Conectar-se ao IoT Central

Use a cadeia de conexão para conectar o ponto de extremidade do Hub IoT interno do IoT Central. A conexão permite que você envie telemetria para seu aplicativo IoT Central, sincronize valores de propriedade com seu aplicativo IoT Central e responda a comandos enviados por seu aplicativo IoT Central.

## <a name="handle-connection-failures"></a>Lidar com falhas de conexão

Para fins de dimensionamento ou recuperação de desastre, IoT Central pode atualizar seu hub IoT subjacente. Para manter a conectividade, o código do dispositivo deve tratar erros de conexão específicos estabelecendo uma conexão com o novo ponto de extremidade do Hub IoT.

Se o dispositivo obtiver qualquer um dos erros a seguir ao se conectar, ele deverá refazer a etapa de provisionamento com o DPS para obter uma nova cadeia de conexão. Esses erros significam a cadeia de conexão que o dispositivo está usando não é mais válido:

- Ponto de extremidade do Hub IoT inacessível.
- Token de segurança expirado.
- Dispositivo desabilitado no Hub IoT.

Se o dispositivo obtiver qualquer um dos erros a seguir ao se conectar, ele deverá usar uma estratégia de retirada para tentar a conexão novamente. Esses erros significam a cadeia de conexão que o dispositivo está usando ainda é válido, mas condições transitórias estão interrompendo o dispositivo de se conectar:

- Dispositivo bloqueado pelo operador.
- Erro interno 500 do serviço.

Para saber mais sobre os códigos de erro do dispositivo, consulte [Solucionando problemas de conexões de dispositivo](troubleshoot-connection.md).

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Examine um exemplo de código que mostra como usar tokens SAS no [tutorial: criar e conectar um aplicativo cliente ao aplicativo de IOT central do Azure](tutorial-connect-device.md)
- Saiba como [conectar dispositivos com certificados X. 509 usando Node.js SDK do dispositivo para IOT central aplicativo](how-to-connect-devices-x509.md)
- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)
- Saiba como [Definir um novo tipo de dispositivo IoT em seu aplicativo do Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [Dispositivos do Azure IoT Edge e do Azure IoT Central](./concepts-iot-edge.md)
