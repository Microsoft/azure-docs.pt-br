---
title: Saiba mais sobre as opções de conexão para desenvolvedores de dispositivos IoT do Azure
description: Saiba mais sobre as opções e ferramentas de conexão de dispositivo usadas para desenvolvedores de dispositivos IoT do Azure.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654002"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Visão geral: opções de conexão para desenvolvedores de dispositivos IoT do Azure
Como desenvolvedor que trabalha com dispositivos, você tem várias opções para conectar e gerenciar dispositivos IoT do Azure. Este artigo exibe a visão geral das opções e ferramentas mais usadas para ajudá-lo a conectar e gerenciar dispositivos.

Ao avaliar as opções de conexão do Azure IoT para seus dispositivos, é útil comparar os seguintes itens:
- Plataformas de aplicativo do dispositivo IoT do Azure
- Ferramentas para conectar e gerenciar dispositivos

## <a name="application-platforms-iot-central-and-iot-hub"></a>Plataformas de aplicativos: IoT Central e Hub IoT
O Azure IoT contém dois serviços que são plataformas para aplicativos de nuvem habilitados para dispositivos: Azure IoT Central e Hub IoT do Azure. Você pode usar qualquer um para hospedar um aplicativo IoT e conectar dispositivos.
- O [IOT central](../iot-central/core/overview-iot-central.md) foi projetado para reduzir a complexidade e o custo de trabalhar com soluções de IOT. É um aplicativo SaaS (software como serviço) que fornece uma plataforma completa para hospedar aplicativos de IoT. Você pode usar a interface do usuário da Web do IoT Central para simplificar todo o ciclo de vida de criação e gerenciamento de aplicativos de IoT. A interface do usuário da Web simplifica as tarefas de criação de aplicativos e a conexão e o gerenciamento de algumas de até milhões de dispositivos. IoT Central usa o Hub IoT para criar e gerenciar aplicativos, mas mantém os detalhes transparentes para o usuário. Em geral, IoT Central fornece complexidade reduzida e esforço de desenvolvimento e gerenciamento simplificado de dispositivos por meio da interface do usuário da Web.
- O [Hub IOT](../iot-hub/about-iot-hub.md) atua como um hub de mensagens central para comunicação bidirecional entre aplicativos IOT e dispositivos conectados. É um aplicativo de PaaS (plataforma como serviço) que também fornece uma plataforma para hospedar aplicativos de IoT. Assim como IoT Central, ele pode ser dimensionado para dar suporte a milhões de dispositivos. Em geral, o Hub IoT oferece maior controle e personalização sobre o design do aplicativo e mais opções da ferramenta para desenvolvedores para trabalhar com o serviço, ao custo de algum aumento na complexidade de desenvolvimento e gerenciamento.

## <a name="tools-to-connect-and-manage-devices"></a>Ferramentas para conectar e gerenciar dispositivos
Depois de selecionar o Hub IoT ou IoT Central para hospedar seu aplicativo IoT, você tem várias opções de ferramentas de desenvolvedor. Você pode usar essas ferramentas para se conectar à plataforma de aplicativo IoT selecionada e para criar e gerenciar aplicativos e dispositivos. A tabela a seguir resume as opções de ferramenta comuns. 

> [!NOTE]
> Além das ferramentas a seguir, você pode criar e gerenciar aplicativos de IoT programaticamente usando APIs REST, SDKs do Azure ou modelos de Azure Resource Manager. Você pode saber mais na documentação do [Hub IOT](../iot-hub/about-iot-hub.md) e do serviço de [IOT central](../iot-central/core/overview-iot-central.md) .

|Ferramenta  |Dá suporte à plataforma &nbsp; &nbsp; &nbsp; IOT &nbsp; |Documentação  |Descrição  |
|---------|---------|---------|---------|
|Interface do usuário da Web central     | Central | [Início rápido central](../iot-central/core/quick-deploy-iot-central.md) | Portal baseado em navegador para IoT Central. |
|Portal do Azure     | Hub, central      | [Criar um hub IOT com portal do Azure](../iot-hub/iot-hub-create-through-portal.md), [gerenciar IOT central do portal do Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal baseado em navegador para dispositivos e Hub IoT. Também funciona com outros recursos do Azure, incluindo IoT Central. |
|CLI do Azure     | Hub, central          | [Criar um hub IOT com a CLI](../iot-hub/iot-hub-create-using-cli.md), [gerenciar IOT central de CLI do Azure](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interface de linha de comando para criar e gerenciar aplicativos de IoT. |
|Azure PowerShell     | Hub, central   | [Criar um hub IOT com o PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [gerenciar IOT central de Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interface do PowerShell para criar e gerenciar aplicativos de IoT |
|Azure IoT Tools para VS Code  | Hub | [Criar um hub IoT com ferramentas para VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | Extensão de VS Code para aplicativos de Hub IoT. |
|Gerenciador de IoT do Azure     | Hub | [Gerenciador de IoT do Azure](https://github.com/Azure/azure-iot-explorer) | Não é possível criar hubs IoT. Conecta-se a um hub IoT existente para gerenciar dispositivos. Frequentemente usado com a CLI ou o Portal.|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de conexão de dispositivos com o Azure IoT, explore os guias de início rápido a seguir:
- IoT Central: [criar um aplicativo de IOT central do Azure](../iot-central/core/quick-deploy-iot-central.md)
- Hub IoT: [Enviar telemetria de um dispositivo para um hub IOT e monitorá-lo com o CLI do Azure](../iot-hub/quickstart-send-telemetry-cli.md)