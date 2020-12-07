---
title: incluir arquivo
description: incluir arquivo
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017451"
---
Como operador no seu aplicativo do Azure IoT Central, você pode:

* Ver a telemetria enviada pelo dispositivo na página **Visão geral**:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Exibir telemetria de dispositivo":::

* Ver as propriedades do dispositivo na página **Sobre**:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Exibir propriedades do dispositivo":::

## <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Como desenvolvedor de soluções, você pode personalizar o modelo de dispositivo criado automaticamente pelo IoT Central quando o dispositivo de termostato estiver foi conectado.

Para adicionar uma propriedade de nuvem para armazenar o nome do cliente associado ao dispositivo:

1. No aplicativo do IoT Central, acesse o modelo de dispositivo de **Termostato** na página **Modelos de dispositivos**.

1. No modelo de dispositivo de **Termostato**, selecione **Propriedades de nuvem**.

1. Selecione **Adicionar propriedade de nuvem**. Insira *Nome do cliente* como o **Nome de exibição** e escolha **Cadeia de caracteres** como **Esquema**. Depois, selecione **Salvar**.

Para personalizar como o comando **Obter relatório de Máx.-Mín.** é exibido no aplicativo do IoT Central, selecione **Personalizar** no modelo de dispositivo. Substitua **Obter relatório de Máx.-Mín.** por *Obter relatório de status*. Depois, selecione **Salvar**.

O modelo de **Termostato** inclui a propriedade gravável **Temperatura de Destino**, e o modelo de dispositivo inclui a propriedade de nuvem **Nome do Cliente**. Crie uma exibição que um operador possa usar para editar estas propriedades:

1. Selecione **Exibições** e, em seguida, o bloco **Editando dados do dispositivo e da nuvem**.

1. Insira _Propriedades_ como o nome do formulário.

1. Selecione as propriedades **Temperatura de Destino** e **Nome do Cliente**. Em seguida, selecione **Adicionar seção**.

1. Salve suas alterações.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Exibição para atualizar os valores de propriedade":::

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para que um operador possa ver e usar as personalizações feitas por você, publique o modelo de dispositivo.

No modelo de dispositivo de **Termostato**, selecione **Publicar**. No painel **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

Agora, um operador poderá usar a exibição **Propriedades** para atualizar os valores de propriedade e chamar o comando **Obter relatório de status** na página de comandos do dispositivo:

* Atualizar os valores de propriedades graváveis na página **Propriedades**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Atualizar as propriedades do dispositivo":::

* Chamar os comandos na página **Comandos**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chamar o comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Exibir a resposta do comando":::
