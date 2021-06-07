---
title: Saiba mais sobre as opções de conexão para desenvolvedores de dispositivos IoT do Azure
description: Saiba mais sobre as opções e as ferramentas de conexão de dispositivo mais usadas para desenvolvedores de dispositivos IoT do Azure.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589543"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Visão Geral: opções de conexão para desenvolvedores de dispositivos IoT do Azure
Como desenvolvedor que trabalha com dispositivos, você tem várias opções para conectar e gerenciar dispositivos IoT do Azure. Este artigo apresenta uma visão geral das opções e ferramentas mais usadas para ajudar você a conectar e gerenciar dispositivos.

Ao avaliar as opções de conexão do IoT do Azure para seus dispositivos, é útil comparar os seguintes itens:
- Plataformas de aplicativos do dispositivo IoT do Azure
- Ferramentas para conectar e gerenciar dispositivos

## <a name="application-platforms-iot-central-and-iot-hub"></a>Plataformas de aplicativos: IoT Central e Hub IoT
O IoT do Azure contém dois serviços que são plataformas para aplicativos de nuvem habilitados para dispositivos: Azure IoT Central e Hub IoT do Azure. Você pode usar qualquer um deles para hospedar um aplicativo IoT e conectar dispositivos.
- O [IoT Central](../iot-central/core/overview-iot-central.md) foi projetado para reduzir a complexidade e os custos ao trabalhar com soluções IoT. Trata-se de um aplicativo SaaS (software como serviço) que fornece uma plataforma completa para hospedagem de aplicativos IoT. Você pode usar a interface do usuário da Web do IoT Central para simplificar todo o ciclo de vida de criação e gerenciamento de aplicativos IoT. A interface do usuário da Web simplifica as tarefas de criação de aplicativos e a conexão e o gerenciamento de alguns ou até milhões de dispositivos. O IoT Central usa o Hub IoT para criar e gerenciar aplicativos, mas mantém os detalhes transparentes ao usuário. Em geral, o IoT Central oferece menor complexidade e esforços reduzidos de desenvolvimento, bem como gerenciamento simplificado de dispositivos por meio da interface do usuário da Web.
- O [Hub IoT](../iot-hub/about-iot-hub.md) atua como um hub central de mensagens para comunicação bidirecional entre aplicativos IoT e os dispositivos conectados. Trata-se de um aplicativo PaaS (plataforma como serviço) que também fornece uma plataforma para hospedar aplicativos IoT. Assim como o IoT Central, ele pode ser escalado para dar suporte a milhões de dispositivos. Em geral, o Hub IoT oferece maior controle e capacidade de personalização sobre o design do seu aplicativo e mais opções de ferramentas para desenvolvedores para trabalhar com o serviço, às custas de algum aumento na complexidade de desenvolvimento e de gerenciamento.

## <a name="tools-to-connect-and-manage-devices"></a>Ferramentas para conectar e gerenciar dispositivos
Depois de selecionar o Hub IoT ou o IoT Central para hospedar seu aplicativo IoT, você tem várias opções de ferramentas para desenvolvedores. Você pode usar essas ferramentas para se conectar à plataforma de aplicativos IoT selecionada e para criar e gerenciar aplicativos e dispositivos. A tabela a seguir resume as opções de ferramentas mais comuns. 

> [!NOTE]
> Além das ferramentas a seguir, você pode criar e gerenciar programaticamente aplicativos IoT usando APIs REST, SDKs do Azure ou modelos do Azure Resource Manager. Você pode aprender mais na documentação dos serviços [Hub IoT](../iot-hub/about-iot-hub.md) e [IoT Central](../iot-central/core/overview-iot-central.md).

|Ferramenta  |Dá suporte à plataforma IoT &nbsp; &nbsp; &nbsp; &nbsp; |Documentação  |Descrição  |
|---------|---------|---------|---------|
|Interface do usuário da Web central     | Central | [Início rápido central](../iot-central/core/quick-deploy-iot-central.md) | Portal baseado em navegador para o IoT Central. |
|Portal do Azure     | Hub, central      | [Criar um Hub IoT com o portal do Azure](../iot-hub/iot-hub-create-through-portal.md), [Gerenciar o IoT Central por meio do portal do Azure](../iot-central/core/howto-manage-iot-central-from-portal.md)| Portal baseado em navegador para o Hub IoT e os dispositivos. Também funciona com outros recursos do Azure, inclusive o IoT Central. |
|Azure IoT Explorer     | Hub | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | Não é possível criar Hubs IoT. Conecta-se a um Hub IoT existente para gerenciar dispositivos. Frequentemente usado com a CLI ou o portal.|
|CLI do Azure     | Hub, central          | [Criar um Hub IoT com a CLI](../iot-hub/iot-hub-create-using-cli.md), [Gerenciar o IoT Central por meio da CLI do Azure](../iot-central/core/howto-manage-iot-central-from-cli.md) | Interface de linha de comando para criar e gerenciar aplicativos IoT. |
|Azure PowerShell     | Hub, central   | [Criar um Hub IoT com o PowerShell](../iot-hub/iot-hub-create-using-powershell.md), [Gerenciar o IoT Central por meio do Azure PowerShell](../iot-central/core/howto-manage-iot-central-from-powershell.md) | Interface do PowerShell para criar e gerenciar aplicativos IoT |
|Azure IoT Tools para VS Code  | Hub | [Criar um Hub IoT com ferramentas para VS Code](../iot-hub/iot-hub-create-use-iot-toolkit.md) | Extensão do VS Code para aplicativos do Hub IoT. |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de conexão de dispositivos com o IoT do Azure, explore os seguintes inícios rápidos:
- IoT Central: [criar um aplicativo do Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- Hub IoT: [enviar telemetria de um dispositivo para o Hub IoT e monitorá-lo com a CLI do Azure](../iot-hub/quickstart-send-telemetry-cli.md)