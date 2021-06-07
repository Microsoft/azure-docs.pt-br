---
title: 'Início Rápido: Criar um módulo gêmeo de segurança'
description: Neste guia de início rápido, saiba como criar um módulo gêmeo do Defender para IoT para uso com o Azure Defender para IoT.
ms.topic: quickstart
ms.date: 1/21/2021
ms.openlocfilehash: 27447f00546ab0d01a33b286f4bb1fbc810c260b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384554"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>Início Rápido: Criar um módulo gêmeo azureiotsecurity

Este Início Rápido explica como criar módulos gêmeos individuais _azureiotsecurity_ para novos dispositivos ou criar módulos gêmeos em lote para todos os dispositivos em um Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

- Nenhum

## <a name="understanding-azureiotsecurity-module-twins"></a>Noções básicas sobre módulos gêmeos azureiotsecurity

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo.

O Defender para IoT oferece integração completa à plataforma de gerenciamento de dispositivos IoT que você já tem, permitindo gerenciar seu status de segurança de dispositivo e fazer uso das funcionalidades existentes de controle de dispositivo.
A integração do Defender para IoT é obtida por meio do uso do mecanismo gêmeo do Hub IoT.

Veja [módulos gêmeos do Hub IoT](../iot-hub/iot-hub-devguide-module-twins.md) para saber mais sobre o conceito geral dos módulos gêmeos no Hub IoT do Azure.

O Defender para IoT usa o mecanismo de módulo gêmeo e mantém um módulo gêmeo de segurança chamado _azureiotsecurity_ para cada um dos seus dispositivos.

O gêmeo do microagente do Defender para IoT contém todas as informações relevantes para a segurança de dispositivo para cada um de seus dispositivos.

Para aproveitar por completo os recursos do Defender para IoT, você precisará criar, configurar e usar esses gêmeos do microagente do Defender para IoT para cada dispositivo no serviço.

## <a name="create-azureiotsecurity-module-twin"></a>Criar módulo gêmeo azureiotsecurity

Os módulos gêmeos _azureiotsecurity_ podem ser criados de duas maneiras:

1. [Script em lotes do módulo](https://aka.ms/iot-security-github-create-module) – cria automaticamente o módulo gêmeo para novos dispositivos ou dispositivos sem um módulo gêmeo usando a configuração padrão.
1. Editando manualmente cada módulo gêmeo individualmente com configurações específicas para cada dispositivo.

>[!NOTE]
> Usar o método de lote não substituirá os módulos gêmeos azureiotsecurity existentes. Usar o método de lote cria APENAS módulos gêmeos para dispositivos que ainda não têm um módulo gêmeo de segurança.

Confira a [configuração do agente](how-to-agent-configuration.md) para saber como modificar ou alterar a configuração de um módulo gêmeo existente.

Para criar manualmente um módulo gêmeo _azureiotsecurity_ para um dispositivo:

1. No Hub IoT, localize e selecione o dispositivo para o qual deseja criar um módulo gêmeo de segurança.

1. Escolha o seu dispositivo e em **Adicionar identidade do módulo**.

1. No campo **Nome da Identidade do Módulo**, insira **azureiotsecurity**.

1. Clique em **Salvar**.

## <a name="verify-creation-of-a-module-twin"></a>Verificar a criação de um módulo gêmeo

Para verificar a existência de um módulo gêmeo de segurança para um dispositivo específico:

1. No seu Hub IoT do Azure, selecione **Dispositivos IoT** no menu **Explorers**.

1. Insira a identificação do dispositivo ou selecione uma opção no **Campo de dispositivo de consulta** e escolha **Dispositivos de consulta**.

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="Dispositivos de consulta":::

1. Escolha o dispositivo ou faça uma seleção dupla nele para abrir a página Detalhes do Dispositivo.

1. Selecione o menu **Identidades do módulo** e confirme a existência do módulo **ascforiotsecurity** na lista de identidades de módulo associadas ao dispositivo.

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="Módulos associados a um dispositivo":::

Para saber mais sobre como personalizar propriedades de módulos gêmeos do Defender para IoT, confira [Configuração do agente](how-to-agent-configuration.md).

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como investigar as recomendações de segurança...

> [!div class="nextstepaction"]
> [Investigar recomendações de segurança](quickstart-investigate-security-recommendations.md)