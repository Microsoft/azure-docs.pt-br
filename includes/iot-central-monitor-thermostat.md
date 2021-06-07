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
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491011"
---
<!-- All needs updating -->
Como operador no seu aplicativo do Azure IoT Central, você pode:

* Exibir a telemetria enviada pelos dois componentes termostatos na página **Visão geral**:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Exibir telemetria de dispositivo":::

* Exibir as propriedades do dispositivo na página **Sobre**. Esta página mostra as propriedades do componente de informações do dispositivo e dos dois componentes termostatos:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Exibir propriedades do dispositivo":::

## <a name="customize-the-device-template"></a>Personalizar o modelo de dispositivo

Como desenvolvedor de soluções, você pode personalizar o modelo de dispositivo criado automaticamente pelo IoT Central quando o dispositivo controlador de temperatura foi conectado.

Para adicionar uma propriedade de nuvem para armazenar o nome do cliente associado ao dispositivo:

1. No aplicativo do IoT Central, acesse o modelo de dispositivo do **Controlador de temperatura** na página **Modelos de dispositivos**.

1. No modelo de dispositivo do **Controlador de temperatura**, selecione **Propriedades de nuvem**.

1. Selecione **Adicionar propriedade de nuvem**. Insira *Nome do cliente* como o **Nome de exibição** e escolha **Cadeia de caracteres** como **Esquema**. Depois, selecione **Salvar**.

Para personalizar como os comandos **Obter relatório de Máx.-Mín.** são exibidos no aplicativo do IoT Central:

1. Selecione **Personalizar** no menu de modelo de dispositivo.

1. Para **getMaxMinReport (thermostat1)** , substitua *Obter relatório de Máx.-Mín.* com *Obter relatório de status do thermostat1*.

1. Para **getMaxMinReport (thermostat2)** , substitua *Obter relatório de Máx.-Mín.* com *Obter relatório de status do thermostat2*.

1. Clique em **Salvar**.

Para personalizar como as propriedades graváveis da **temperatura de destino** são exibidas no aplicativo do IoT Central:

1. Selecione **Personalizar** no menu de modelo de dispositivo.

1. Para **targetTemperature (thermostat1)** , substitua a *Temperatura de destino* pela *Temperatura de destino (1)* .

1. Para **targetTemperature (thermostat2)** , substitua a *Temperatura de destino* pela *Temperatura de destino (2)* .

1. Clique em **Salvar**.

Os componentes termostato no modelo do **Controlador de temperatura** incluem a propriedade gravável da **Temperatura de destino** e o modelo de dispositivo inclui a propriedade de nuvem **Nome do cliente**. Crie uma exibição que um operador possa usar para editar estas propriedades:

1. Selecione **Exibições** e, em seguida, o bloco **Editando dados do dispositivo e da nuvem**.

1. Insira _Propriedades_ como o nome do formulário.

1. Selecione as propriedades **Temperatura de destino (1)** , **Temperatura de destino (2)** e **Nome do cliente**. Em seguida, selecione **Adicionar seção**.

1. Salve suas alterações.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Exibição para atualizar os valores de propriedade":::

## <a name="publish-the-device-template"></a>Publicar o modelo de dispositivo

Para que um operador possa ver e usar as personalizações feitas por você, publique o modelo de dispositivo.

No modelo de dispositivo de **Termostato**, selecione **Publicar**. No painel **Publicar este modelo de dispositivo no aplicativo**, selecione **Publicar**.

Agora, um operador poderá usar a exibição **Propriedades** para atualizar os valores da propriedade e chamar os comandos **Obter relatório de status do thermostat1** e **Obter relatório de status do thermostat2** na página de comandos do dispositivo:

* Atualizar os valores de propriedades graváveis na página **Propriedades**:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Atualizar as propriedades do dispositivo":::

* Chamar os comandos na página **Comandos**:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Chamar o comando":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Exibir a resposta do comando":::
