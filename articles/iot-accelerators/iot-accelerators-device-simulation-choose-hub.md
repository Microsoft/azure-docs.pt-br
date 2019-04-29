---
title: Usar um Hub IoT existente com a solução de simulação de dispositivo - Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de Simulação de Dispositivo para usar um Hub IoT existente.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123651"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Usar um Hub IoT existente com o acelerador de solução de simulação de dispositivo

Ao implantar o Device Simulation, você também pode optar por implantar um hub IoT para usar em sua simulação. Essa opção implanta um [S2 hub IoT de nível 1 com uma única unidade de escala](../iot-hub/iot-hub-scaling.md). Se você implantar esse hub IoT opcional, ainda poderá optar por segmentar outro Hub IoT para uma execução de simulação.

Se você optar por não implantar o Hub IoT opcional, deverá usar seu próprio hub para quaisquer simulações executadas.

Se você não tiver um Hub IoT, será possível criar um novo por meio do [portal do Azure](https://portal.azure.com).

Para usar um hub IoT já existente, você precisa da cadeia de conexão para o **iothubowner** política de acesso compartilhado. Você pode obter essa cadeia de conexão no [portal do Azure](https://portal.azure.com):

1. Na página de configuração do hub no portal, clique em **Políticas de acesso compartilhado**.

1. Clique em **iothubowner**.

1. Copie a cadeia de conexão primária ou secundária.

[![Obter a cadeia de conexão](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Use a cadeia de conexão de conexão que você copiou ao configurar a simulação:

![Configurar a simulação](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Próximas etapas

Neste guia de instruções, você aprendeu a usar um hub IoT existente em uma simulação. Em seguida, você talvez queira saber como [criar um modelo de dispositivo avançados](iot-accelerators-device-simulation-advanced-device.md) para uma simulação.
