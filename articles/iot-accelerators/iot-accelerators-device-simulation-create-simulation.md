---
title: Criar uma nova simulação IoT – Azure | Microsoft Docs
description: Neste tutorial, você usará a Simulação de Dispositivo para criar e executar uma nova simulação.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: df658a95069f39026daab7afb14c5f1d0f47c5a5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713886"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Tutorial: Criar e executar uma simulação de dispositivo IoT

Neste tutorial, você usará a Simulação de Dispositivo para criar e executar uma nova simulação de IoT usando um ou mais dispositivos simulados.

Neste tutorial, você:

>[!div class="checklist"]
> * Exibir todas as simulações ativas e executadas
> * Criar e executar uma nova simulação
> * Exibir métrica de uma simulação
> * Parar uma simulação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para seguir este tutorial, você precisará de uma instância implantada da Simulação de Dispositivo em sua assinatura do Azure.

Se você ainda não implantou a Simulação de Dispositivo, confira [Implantação da Simulação de Dispositivo](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) no GitHub.

## <a name="view-simulations"></a>Exibir simulações

Selecione **Simulações** na barra de menus. A página **Simulações** exibe informações sobre todas as simulações disponíveis. Nessa página, você pode ver as simulações em execução e as que já foram executadas. Para executar novamente uma simulação anterior, clique no bloco da simulação para abrir seus detalhes:

![Simulações](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Criar uma simulação

Para criar uma simulação, clique em **+ Nova Simulação** no canto superior direito.

Uma simulação é formada por um ou mais modelos de dispositivo. O modelo de dispositivo define o comportamento, a telemetria e o formato de mensagem do dispositivo a ser simulado.

Para adicionar um modelo de dispositivo, clique em **+ Adicionar um tipo de dispositivo** e selecione o modelo do dispositivo na lista. A simulação pode ter mais de um modelo de dispositivo. Cada modelo de dispositivo pode ter uma contagem de dispositivos e frequência de mensagens diferentes.

Quando o novo formulário de simulação for concluído, clique em **Iniciar Simulação** para iniciar a simulação.

Dependendo do número de dispositivos simulados, talvez demore alguns minutos para que a simulação seja configurada e iniciada:

![Nova simulação](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Parar uma simulação

Ao clicar em **Iniciar Simulação**, você vê a página de detalhes da simulação. Essa página de detalhes mostra estatísticas da simulação em tempo real e métricas do Hub IoT. Você também pode navegar até essa página de detalhes clicando no bloco da simulação na página **Simulações**.

Para interromper uma simulação, clique em **Parar Simulação** na barra de ações no canto superior direito.

![Parar a simulação](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar, executar e parar uma simulação. Você também aprendeu a exibir os detalhes da simulação. Para saber mais sobre como executar simulações, continue no próximo tutorial:

> [!div class="nextstepaction"]
> [Criar um dispositivo simulado personalizado](iot-accelerators-device-simulation-create-custom-device.md)
