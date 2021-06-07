---
title: Defender-IoT-micro-Agent e dispositivo gêmeos
description: Saiba mais sobre o conceito de defender-IoT-micro-Agent gêmeos e como eles são usados no defender para IoT.
ms.topic: conceptual
ms.date: 07/24/2019
ms.openlocfilehash: 1ed6faf03d168ed7a2a2f07733cb7e238d234915
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779164"
---
# <a name="defender-iot-micro-agent"></a>Defender-IoT-micro-Agent

Este artigo explica como o defender para IoT usa dispositivos gêmeos e módulos.

## <a name="device-twins"></a>Dispositivos gêmeos

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo.

O Defender para IoT oferece integração completa à plataforma de gerenciamento de dispositivos IoT que você já tem, permitindo gerenciar seu status de segurança de dispositivo e fazer uso das funcionalidades existentes de controle de dispositivo. A integração é obtida usando o mecanismo de gêmeo do Hub IoT.

Saiba mais sobre o conceito de [dispositivo gêmeos](../iot-hub/iot-hub-devguide-device-twins.md) no Hub IOT do Azure.

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-micro-Agent gêmeos

O defender para IoT mantém um defender-IoT-micro-Agent para cada dispositivo no serviço.
O defender-IoT-micro-Agent "/" e retém todas as informações relevantes à segurança do dispositivo para cada dispositivo específico em sua solução.
As propriedades de segurança do dispositivo são mantidas em um defender-IoT-micro-Agent para comunicação mais segura e para habilitar atualizações e manutenção que exigem menos recursos.

Confira [criar defender-IOT-micro-Agent](quickstart-create-security-twin.md) e [configurar os agentes de segurança](how-to-agent-configuration.md) para saber como criar, personalizar e configurar o entrelaçamento. Consulte [entendendo o módulo gêmeos](../iot-hub/iot-hub-devguide-module-twins.md) para saber mais sobre o conceito de módulo gêmeos no Hub IOT.

## <a name="see-also"></a>Veja também

- [Visão geral do defender for IoT](overview.md)
- [Implantar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)