---
title: Guia do operador de IoT Central do Azure
description: O Azure IoT Central é uma plataforma de aplicativo IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral da função operador no IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669926"
---
# <a name="iot-central-operator-guide"></a>Guia do operador de IoT Central

*Este artigo se aplica aos operadores.*

Um aplicativo do IoT Central permite que você monitore e gerencie milhões de dispositivos ao longo de seu ciclo de vida. Este guia destina-se a operadores que usam um aplicativo IoT Central para gerenciar dispositivos IoT.

Um operador:

- Monitora e gerencia os dispositivos conectados ao aplicativo.
- Soluciona problemas e corrige problemas com dispositivos.
- Provisiona novos dispositivos.

## <a name="monitor-and-manage-devices"></a>Monitorar e gerenciar dispositivos

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Captura de tela que mostra uma exibição de dispositivo":::

Para monitorar dispositivos, um operador pode usar as exibições de dispositivo definidas pelo Solution Builder como parte do modelo de dispositivo. Essas exibições podem mostrar a telemetria do dispositivo e os valores de propriedade. Um exemplo é a exibição de **visão geral** mostrada na captura de tela anterior.

Para obter informações mais detalhadas, um operador pode usar grupos de dispositivos e os recursos de análise internos. Para saber mais, consulte [como usar a análise para analisar dados do dispositivo](howto-create-analytics.md).

Para gerenciar dispositivos individuais, um operador pode usar exibições de dispositivo para definir propriedades de dispositivo e de nuvem e chamar comandos de dispositivo. Exemplos incluem os modos de exibição **gerenciar dispositivos** e **comandos** na captura de tela anterior.

Para gerenciar dispositivos em massa, um operador pode criar e agendar trabalhos. Os trabalhos podem atualizar as propriedades e executar comandos em vários dispositivos. Para saber mais, consulte [criar e executar um trabalho em seu aplicativo de IOT central do Azure](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Solucionar e corrigir problemas

O operador é responsável pela integridade do aplicativo e seus dispositivos. O [Guia de solução de problemas](troubleshoot-connection.md) ajuda os operadores a diagnosticar e corrigir problemas comuns. Um operador pode usar a página **dispositivos** para bloquear dispositivos que parecem estar com problemas de funcionamento até que o problema seja resolvido.

## <a name="add-and-remove-devices"></a>Adicionar e remover dispositivos

O operador pode adicionar e remover dispositivos em seu aplicativo IoT Central individualmente ou em massa. Para saber mais, confira [gerenciar dispositivos no aplicativo IOT central do Azure](howto-manage-devices.md).

## <a name="personalize"></a>Personalize

Os operadores podem criar painéis personalizados em um aplicativo IoT Central que contêm links para os recursos que eles usam com mais frequência. Para saber mais, consulte [gerenciar painéis](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Próximas etapas

Se quiser saber mais sobre como usar o IoT Central, as próximas etapas sugeridas são experimentar os guias de início rápido, começando em [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md).
