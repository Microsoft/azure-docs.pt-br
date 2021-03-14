---
title: Integrar à solução baseada em agente do Defender para IoT
description: Saiba como integrar e habilitar o serviço de segurança Defender para IoT no seu Hub IoT do Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809109"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Integrar à solução baseada em agente do Defender para IoT

Este artigo explica como habilitar o serviço Defender para IoT no Hub IoT existente. Se você não tiver, no momento, um Hub IoT, confira [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md) para começar a usar.

Você pode gerenciar a segurança da IoT por meio do Hub IoT no Defender para IoT. O portal de gerenciamento localizado no Hub IoT permite que você faça o seguinte: 

- Gerenciar a segurança do Hub IoT.

- Gerenciamento básico da segurança de um dispositivo IoT sem instalar um agente com base na telemetria do Hub IoT. 

- Gerenciamento avançado da segurança de um dispositivo IoT com base no microagente.

> [!NOTE]
> O Defender para IoT atualmente dá suporte apenas ao nível standard dos Hubs IoT.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Integração ao Defender para IoT no Hub IoT

Para todos os novos hubs IoT, o Defender para IoT é definido como **Ativado** por padrão. Verifique se o Defender para IoT foi alternado para **Ativado** durante o processo de criação do Hub IoT.

Para verificar se a alternância está definida como **Ativado**:

1. Navegue até o portal do Azure.

1. Selecione **Hub IoT** na lista de serviços do Azure.

1. Selecione **Criar**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecione o botão Criar na barra de ferramentas superior." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Escolha a guia **Gerenciamento** e verifique se a opção **Defender para IoT** está definida como **Ativado**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Verifique se a alternância do Defender para IoT está definida como ativada.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Integrar o Defender para IoT a um Hub IoT existente

Monitore o gerenciamento de identidades do dispositivo e os padrões de comunicação da nuvem para o dispositivo e do dispositivo para a nuvem. Faça o seguinte para iniciar o serviço: 

1. Procure o Hub IoT. 

1. Selecione o menu  **Visão geral de segurança** . 

1. Clique em Proteger sua solução de IoT e conclua o formulário de integração. 


## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para configurar sua solução...

> [!div class="nextstepaction"]
> [Criar um módulo gêmeo do microagente do Defender para IoT (versão prévia)](quickstart-create-micro-agent-module-twin.md)
