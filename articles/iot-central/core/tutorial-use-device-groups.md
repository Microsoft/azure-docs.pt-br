---
title: Tutorial – usar grupos de dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Tutorial – como um operador, saiba como usar grupos de dispositivos para analisar a telemetria de dispositivos no aplicativo do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a7d26eebb24662a448d8ccb44d037e7706fe776b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832837"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Usar grupos de dispositivos para analisar a telemetria do dispositivo

Este artigo descreve como um operador pode usar grupos de dispositivos para analisar a telemetria do dispositivo no aplicativo do Azure IoT Central.

Um grupo de dispositivos é uma lista de dispositivos agrupados devido à correspondência com alguns critérios especificados. Os grupos de dispositivos ajudam a gerenciar, visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar-condicionado em Seattle e permitir que um técnico encontre os dispositivos pelos quais é responsável.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de dispositivos
> * Usar um grupo de dispositivos para analisar a telemetria do dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os inícios rápidos [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo **Controlador de Sensor** com o qual trabalhar.

## <a name="create-simulated-devices"></a>Criar dispositivos simulados

Antes de criar um grupo de dispositivos, adicione pelo menos cinco dispositivos simulados com base no modelo de dispositivo **Controlador de Sensor** a ser usado neste tutorial:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Captura de tela mostrando cinco dispositivos simulados do controlador de sensor":::

Para quatro dos dispositivos de sensor simulados, use a exibição **Gerenciar dispositivo** para definir o nome do cliente como *Contoso*:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Captura de tela que mostra como definir a propriedade de nuvem Nome do Cliente":::

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **Grupos de dispositivos** no painel esquerdo.

1. Selecione **+ Novo**.

1. Dê ao seu grupo de dispositivos o nome *Dispositivos Contoso*. Também é possível adicionar uma descrição. Um grupo de dispositivos somente pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo **Controlador de Sensor** a ser usado para esse grupo.

1. Para personalizar o grupo de dispositivos e incluir somente os dispositivos pertencentes ao **Contoso**, selecione **+ Filtrar**. Selecione a propriedade **Nome do Cliente**, o operador de comparação **Igual** e **Contoso** como o valor. É possível adicionar vários filtros e dispositivos que atendam a **todos** os critérios de filtro colocados no grupo de dispositivos. O grupo de dispositivos que você criou fica acessível a qualquer pessoa que tenha acesso ao aplicativo, portanto qualquer pessoa pode exibir, modificar ou excluir o grupo de dispositivos.

    > [!TIP]
    > O grupo de dispositivos é uma consulta dinâmica. Cada vez que você visualizar a lista de dispositivos, poderá haver diferentes dispositivos na lista. A lista depende de quais dispositivos atualmente atendem aos critérios da consulta.

1. Selecione **Salvar**.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Captura de tela que mostra a configuração de consulta do grupo de dispositivos":::

> [!NOTE]
> Para dispositivos do Azure IoT Edge, selecione modelos do Azure IoT Edge para criar um grupo de dispositivos.

## <a name="analytics"></a>Análise

Você pode usar a **Análise** com um grupo de dispositivos para analisar a telemetria dos dispositivos do grupo. Por exemplo, você pode plotar a temperatura média relatada por todos os sensores ambientais da Contoso.

Para analisar a telemetria de um grupo de dispositivos:

1. Escolha **Análise** no painel esquerdo.

1. Selecione o grupo de dispositivos **Dispositivos Contoso** que você criou. Em seguida, adicione os tipos de telemetria **Temperatura** e **Umidade**:

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Captura de tela que mostra os tipos de telemetria selecionados para análise":::

    Use os ícones de engrenagem ao lado dos tipos de telemetria para selecionar um tipo de agregação. O padrão é **Média**. Use **Agrupar por** para alterar a forma como os dados agregados são mostrados. Por exemplo, se você dividir por ID do dispositivo, verá uma plotagem para cada dispositivo ao selecionar **Analisar**.

1. Selecione **Analisar** para exibir os valores médios da telemetria:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Captura de tela que mostra os valores médios para todos os dispositivos Contoso":::

    Você pode personalizar a exibição, alterar o período de tempo mostrado e exportar os dados.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar grupos de dispositivos no aplicativo do Azure IoT Central, veja a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](tutorial-create-telemetry-rules.md)
