---
title: Compilar a ponte de dispositivo do Azure IoT Central | Microsoft Docs
description: Compile a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT (Sigfox, Particle, The Things Network etc.) ao aplicativo IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: c98574bc3447482429d7a028c6b47197e08e2e38
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850232"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Compilar a ponte de dispositivo do IoT Central para conectar outras nuvens de IoT ao IoT Central

*Este tópico aplica-se aos administradores.*

A ponte de dispositivo do IoT Central é uma solução de software livre que conecta o Sigfox, Particle, The Things Network e outras nuvens ao aplicativo IoT Central. Se você estiver usando dispositivos de rastreamento de ativos conectados à Rede de Longa Distância de baixo consumo de energia da Sigfox, usando dispositivos de monitoramento da qualidade do ar no Particle Device Cloud ou, usando dispositivos de monitoramento de umidade do solo no TTN, você poderá aproveitar diretamente a potência do IoT Central usando a ponte de dispositivo do IoT Central. A ponte de dispositivo conecta outras nuvens de IoT com o IoT Central, enviando os dados que os dispositivos enviam para outras nuvens até o aplicativo IoT Central. No aplicativo IoT Central, é possível compilar regras e executar análises nesses dados, criar fluxos de trabalho no Microsoft Flow e Aplicativos Lógicos do Azure, exportar esses dados, e muito mais. Obter a [ponte de dispositivo do IoT Central](https://aka.ms/iotcentralgithubdevicebridge) do GitHub

## <a name="what-is-it-and-how-does-it-work"></a>O que é e como funciona?
A ponte de dispositivo do IoT Central é uma solução de software livre no GitHub. Ela está pronta para ser usada com o botão "Implantar no Azure", que implanta um modelo personalizado do Azure Resource Manager com vários recursos do Azure na sua assinatura do Azure. Os recursos incluem:
-   Aplicativo Azure Functions
-   Conta de Armazenamento do Azure
-   Plano de consumo
-   Cofre da Chave do Azure

O aplicativo de funções é a parte crucial da ponte do dispositivo. Ele recebe solicitações HTTP POST de outras plataformas de IoT ou de qualquer plataforma personalizada por meio de uma integração de webhook simples. Nós fornecemos exemplos que mostram como conectar as nuvens Sigfox, Particle e TTN. É possível estender facilmente essa solução para conectar-se à sua nuvem de IoT personalizada, caso sua plataforma possa enviar solicitações de HTTP POST ao aplicativo de funções.
O aplicativo de funções transforma os dados em um formato aceito pelo IoT Central e os encaminha via APIs de DPS.

![Captura de tela do Azure Functions](media/howto-build-iotc-device-bridge/azfunctions.png)

Se o aplicativo IoT Central reconhecer o dispositivo por ID de dispositivo na mensagem encaminhada, uma nova medida será exibida para esse dispositivo. Se a ID do dispositivo nunca for verificada pelo aplicativo IoT Central, o aplicativo de funções tentará registrar um novo dispositivo com essa ID do dispositivo e ele aparecerá como um "Dispositivo não associado" no aplicativo IoT Central. 

## <a name="how-do-i-set-it-up"></a>Como fazer para configurar?
As instruções estão listadas em detalhes no arquivo LEIAME no repositório do GitHub. 

## <a name="pricing"></a>Preços
Os recursos do Azure serão hospedados em sua assinatura do Azure. Obtenha mais informações sobre preços no [aquivo LEIAME](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como compilar a ponte de dispositivo do IoT Central, a seguir está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Gerenciar seus dispositivos](howto-manage-devices.md)
