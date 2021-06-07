---
title: Conectar um dispositivo Azure Sphere no Azure IoT Central | Microsoft Docs
description: Saiba como conectar um dispositivo do Azure Sphere (DevKit) a um aplicativo do Azure IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92123416"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo do Azure Sphere ao aplicativo do Azure IoT Central

*Este artigo se aplica a desenvolvedores de dispositivos.*

Este artigo mostra como conectar um dispositivo do Azure Sphere (DevKit) a um aplicativo do Azure IoT Central.

O Azure Sphere é uma plataforma de aplicativos segura e de alto nível com recursos internos de comunicação e segurança para dispositivos conectados à Internet. Ele inclui uma unidade de microcontrolador de crossover segura e conectada, (MCU), sistema operacional (SO) baseado em Linux de alto nível e um serviço de segurança baseado em nuvem que fornece segurança contínua e renovável. Para obter mais informações, consulte [O que é o Azure Sphere?](/azure-sphere/product-overview/what-is-azure-sphere)

[Os kits de desenvolvimento do Azure Sphere](https://azure.microsoft.com/services/azure-sphere/get-started/) fornecem tudo o que você precisa para iniciar a criação de protótipos e o desenvolvimento de aplicativos do Azure Sphere. O Azure IoT Central com Azure Sphere permite uma pilha de ponta a ponta de solução de IoT. O Azure Sphere fornece o suporte ao dispositivo e o IoT Central como uma plataforma de aplicativo IoT gerenciada e de código zero.

Neste artigo de instruções você vai:

- Criar um dispositivo Azure Sphere no IoT Central usando o modelo de dispositivo do Azure Sphere DevKit da biblioteca.
- Preparar o dispositivo Azure Sphere para o Azure IoT.
- Conectar o Azure Sphere DevKit ao Azure IoT Central.
- Exibir a telemetria do dispositivo no IoT Central.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa dos seguintes recursos:

- Um aplicativo Azure IoT Central.
- Visual Studio 2019 versão 16.4 ou posterior.
- Um [kit de desenvolvimento do Azure Sphere MT3620 da Seeed Studios](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Se você não tiver um dispositivo físico, depois da primeira etapa, pule para a última seção para tentar um dispositivo simulado.

## <a name="create-the-device-in-iot-central"></a>Criar o dispositivo no IoT Central

Para criar um dispositivo do Azure Sphere no IoT Central:

1. No aplicativo do Azure IoT Central, selecione a guia **Modelos de dispositivo** e escolha **+ Novo**. Na seção **Usar um modelo de dispositivo em destaque**, selecione **Dispositivo de exemplo do Azure Sphere**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Modelo de dispositivo para Azure Sphere DevKit":::

1. No modelo de dispositivo, edite a exibição chamada **Visão geral** para mostrar **Temperatura** e **Pressionamento de botão**.

1. Selecione o tipo de exibição **Edição de dispositivo e dados na nuvem** para adicionar outra exibição que mostre a propriedade de leitura/gravação **Status LED**. Arraste a propriedade **Status LED** para o retângulo pontilhado vazio no lado direito do formulário. Clique em **Salvar**.

## <a name="prepare-the-device"></a>Preparar o dispositivo

Para poder conectar o dispositivo Azure Sphere DevKit ao IoT Central, você precisa [configurar o dispositivo e o ambiente de desenvolvimento](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Conectar o dispositivo

Para habilitar o exemplo para se conectar ao IoT Central, você deve [configurar um aplicativo do Azure IoT Central do Azure e, em seguida, modificar o manifesto do aplicativo do exemplo](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Exibir a telemetria no dispositivo

Quando o dispositivo está conectado ao IoT Central, você pode ver a telemetria no painel.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Painel para Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Criar um dispositivo simulado

Se você não tiver um dispositivo físico do Azure Sphere DevKit, poderá criar um dispositivo simulado para experimentar o aplicativo do Azure IoT Central.

Para criar um dispositivo simulado:

- Selecione **Dispositivos > Azure IoT Sphere**
- Selecione **+ Novo**.
- Insira um **ID do dispositivo** exclusivo e um **nome do dispositivo** amigável.
- Habilite a configuração **Simulado**.
- Selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor de dispositivos, algumas próximas etapas sugeridas são:

- Leia sobre [Conectividade de dispositivo no Azure IoT Central](./concepts-get-connected.md)
- Saiba como [Monitorar a conectividade do dispositivo usando o CLI do Azure](./howto-monitor-devices-azure-cli.md)