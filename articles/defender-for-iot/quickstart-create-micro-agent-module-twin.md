---
title: Criar um módulo gêmeo do microagente do Defender para IoT
titleSuffix: Azure Defender for IoT
description: Saiba como criar módulos gêmeos individuais DefenderIotMicroAgent para novos dispositivos.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ea82fea89a9e81e66be6b3060aad067d3ceb8f5f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122996"
---
# <a name="create-a-defender-iot-micro-agent-module-twin"></a>Criar um módulo gêmeo do microagente do Defender para IoT 

Você pode criar módulos gêmeos individuais  **DefenderIotMicroAgent** para novos dispositivos. Você também pode criar módulos gêmeos em lote para todos os dispositivos de um Hub IoT. 

## <a name="device-twins"></a>Dispositivos gêmeos 

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo. 

O Defender para IoT tem a capacidade de ser totalmente integrado à sua plataforma existente de gerenciamento de dispositivos IoT. A integração total permite que você gerencie o status de segurança do seu dispositivo e permite que você use todas as funcionalidades existentes de controle do dispositivo. A integração é obtida usando o mecanismo de gêmeo do Hub IoT. 

Saiba mais sobre o conceito de [dispositivos gêmeos](../iot-hub/iot-hub-devguide-device-twins.md) no Hub IoT do Azure. 

## <a name="security-module-twins"></a>Módulos gêmeos de segurança 

O Defender para IoT usa um módulo gêmeo de segurança para cada dispositivo. O módulo gêmeo de segurança contém todas as informações relevantes para a segurança do dispositivo, para cada dispositivo específico na sua solução. As propriedades de segurança do dispositivo são configuradas por meio de um módulo gêmeo de segurança dedicado para uma comunicação mais segura, a fim de permitir atualizações e manutenção que exige menos recursos. 

## <a name="understanding-defenderiotmicroagent-module-twins"></a>Noções básicas dos módulos gêmeos DefenderIotMicroAgent 

Os dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento de dispositivo quanto na automação do processo nas soluções de IoT criadas no Azure.

O Defender para IoT oferece a funcionalidade de integração completa à plataforma de gerenciamento de dispositivos IoT que você já tem, permitindo gerenciar seu status de segurança de dispositivo e fazer uso das funcionalidades existentes de controle de dispositivo. Você pode integrar o Defender para IoT usando o mecanismo de gêmeo do Hub IoT.  

Para saber mais sobre o conceito geral de módulos gêmeos no Hub IoT do Azure, confira  [Módulos gêmeos do Hub IoT](../iot-hub/iot-hub-devguide-module-twins.md).

O Defender para IoT usa o mecanismo de módulo gêmeo e mantém um módulo gêmeo de segurança chamado `DefenderIotMicroAgent` para cada um dos seus dispositivos. 

Para aproveitar ao máximo todos os recursos do Defender para IoT, você precisará criar, configurar e usar os módulos gêmeos de segurança para cada dispositivo no serviço. 

## <a name="create-defenderiotmicroagent-module-twin"></a>Criar o módulo gêmeo DefenderIotMicroAgent 

Os módulos gêmeos **DefenderIotMicroAgent** podem ser criados pela edição manual de cada módulo gêmeo para incluir configurações específicas para cada dispositivo. 

Para criar manualmente um módulo gêmeo  **DefenderIotMicroAgent** para um dispositivo: 

1. No Hub IoT, localize e selecione o dispositivo para o qual um módulo gêmeo de segurança será criado. 

1. Selecione  **Adicionar identidade do módulo**. 

1. No campo  **Nome da Identidade do Módulo** , insira `DefenderIotMicroAgent`. 

1. Selecione **Salvar**. 

## <a name="verify-the-creation-of-a-module-twin"></a>Verificar a criação de um módulo gêmeo 

Para verificar a existência de um módulo gêmeo de segurança para um dispositivo específico: 

1. No Hub IoT do Azure, selecione  **Dispositivos IoT** no menu  **Gerenciadores** . 

1. Insira a identificação do dispositivo ou selecione uma opção no campo  **Dispositivo de consulta** e escolha  **Dispositivos de consulta**.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/iot-devices.png" alt-text="Selecionar os dispositivos de consulta para obter uma lista dos dispositivos.":::

1. Escolha o dispositivo e abra a página **Detalhes do dispositivo**. 

1. Selecione o menu  **Identidades do módulo**  e confirme a existência do módulo  **DefenderIotMicroAgent** na lista de identidades do módulo associadas ao dispositivo.  

    :::image type="content" source="media/quickstart-create-micro-agent-module-twin/device-details-module.png" alt-text="Selecionar as identidades do módulo na guia.":::

## <a name="next-steps"></a>Próximas etapas 

Avance para o próximo artigo para saber como [investigar as recomendações de segurança](quickstart-investigate-security-recommendations.md).
