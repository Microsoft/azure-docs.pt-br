---
title: 'Início rápido: integrar o Defender para IoT a uma solução baseada em agente'
description: Neste guia de início rápido, você aprenderá a integrar e habilitar o serviço de segurança do Defender para IoT no Hub IoT do Azure.
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: 2704f8989ab39825cef052ca5556a2e6461efe75
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384486"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>Início rápido: integrar o Defender para IoT a uma solução baseada em agente

Este artigo explica como habilitar o serviço Defender para IoT no Hub IoT existente. Se você não tiver, no momento, um Hub IoT, confira [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md) para começar a usar.

Você pode gerenciar a segurança da IoT por meio do Hub IoT no Defender para IoT. O portal de gerenciamento localizado no Hub IoT permite que você faça o seguinte: 

- Gerenciar a segurança do Hub IoT.

- Gerenciamento básico da segurança de um dispositivo IoT sem instalar um agente com base na telemetria do Hub IoT. 

- Gerenciamento avançado da segurança de um dispositivo IoT com base no microagente.

> [!NOTE]
> O Defender para IoT atualmente dá suporte apenas ao nível standard dos Hubs IoT.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>Integrar o Defender para IoT a um Hub IoT

Para todos os novos hubs IoT, o Defender para IoT é definido como **Ativado** por padrão. Verifique se o Defender para IoT foi alternado para **Ativado** durante o processo de criação do Hub IoT.

Para verificar se a alternância está definida como **Ativado**:

1. Navegue até o portal do Azure.

1. Selecione **Hub IoT** na lista de serviços do Azure.

1. Selecione **Criar**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Selecione o botão Criar na barra de ferramentas superior." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Escolha a guia **Gerenciamento** e verifique se a opção **Defender para IoT** está definida como **Ativado**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Verifique se a alternância do Defender para IoT está definida como ativada.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Integrar o Defender para IoT a um Hub IoT existente

Você pode integrar o Defender para IoT a um Hub IoT existente, no qual você pode monitorar os padrões de comunicação de gerenciamento de identidade do dispositivo, dispositivo para nuvem e nuvem para dispositivo.

Para Integrar o Defender para IoT a um Hub IoT existente:

1. Navegue até o Hub IoT. 

1. Selecione o Hub IoT a ser integrado.

1. Selecione qualquer opção na seção **Segurança**.

1. Clique em **Proteger sua solução de IoT** e preencha o formulário de integração. 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="Selecione o botão Proteger solução de IoT para proteger a solução.":::

O botão **Proteger solução de IoT** só aparecerá se o Hub IoT ainda não tiver sido integrado ou se ao realizar a integração você tiver deixado o botão de alternância Defender para IoT como **Desl**.

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="Se o botão de alternância tiver sido definido como desligado durante a integração.":::

## <a name="next-steps"></a>Próximas etapas

Passe para o próximo artigo para configurar sua solução...

> [!div class="nextstepaction"]
> [Criar um módulo gêmeo do microagente do Defender para IoT (versão prévia)](quickstart-create-micro-agent-module-twin.md)
