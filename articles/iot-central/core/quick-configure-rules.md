---
title: Início rápido – Configurar regras e ações no Azure IoT Central
description: Este início rápido mostra a você, como um construtor, como configurar regras e ações baseadas em telemetria em seu aplicativo do Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588999"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Início Rápido: Configurar regras e ações para o seu dispositivo no Azure IoT Central

Neste início rápido, você criará uma regra que envia um email quando a umidade informada por um sensor do dispositivo exceder 55%.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os dois inícios rápidos anteriores [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md) e [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-simulated-device.md) para criar o modelo de dispositivo **Controlador de Sensor** com o qual trabalhar.

## <a name="create-a-telemetry-based-rule"></a>Criar uma regra baseada em telemetria

1. Para adicionar uma nova regra baseada em telemetria ao aplicativo, no painel esquerdo, selecione **Regras**.

1. Para criar uma regra, selecione **+Novo**.

1. Insira **Umidade ambiental** como o nome da regra.

1. Na seção **Dispositivos de destino**, selecione o **Controlador de Sensor** como o modelo de dispositivo. Essa opção filtra os dispositivos aos quais a regra se aplica por tipo de modelo de dispositivo. Você pode adicionar mais critérios de filtro selecionando **+ Filtro**.

1. Na seção **Condições**, defina o que dispara a regra. Use as seguintes informações para definir uma condição com base na telemetria de temperatura:

    | Campo        | Valor            |
    | ------------ | ---------------- |
    | Medida  | SensorHumid      |
    | Operador     | é maior que  |
    | Valor        | 55               |

    Para adicionar mais condições, selecione **+Condição**.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Captura de tela que mostra a condição da regra":::

1. Para adicionar uma ação de email a ser executada quando a regra for disparada, selecione **+ Email**.

1. Use as informações da seguinte tabela para definir a ação e, em seguida, selecione **Concluído**:

    | Configuração   | Valor                                             |
    | --------- | ------------------------------------------------- |
    | Nome de exibição | Ação de email do operador                          |
    | Para        | Seu endereço de email                                |
    | Observações     | A umidade ambiental excedeu o limite. |

    > [!NOTE]
    > Para receber uma notificação por email, o endereço de email deve ser uma [ID de usuário no aplicativo](howto-administer.md), e esse usuário deve entrar no aplicativo pelo menos uma vez.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="Captura de tela que mostra uma ação do email adicionada à regra":::

1. Clique em **Salvar**. A regra é listada na página **Regras**.

## <a name="test-the-rule"></a>Teste a regra

Logo depois de salvar a regra, ela passa a valer. Quando as condições definidas na regra forem atendidas, seu aplicativo enviará um email para o endereço que você especificou na ação.

> [!NOTE]
> Depois que os testes forem concluídos, desligue a regra para interromper o recebimento de alertas na caixa de entrada.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Criar uma regra baseada em telemetria
* Adicionar uma ação

Para saber mais sobre como monitorar dispositivos conectados ao seu aplicativo, passe para o início rápido:

> [!div class="nextstepaction"]
> [Use o Azure IoT Central para monitorar seus dispositivos](quick-monitor-devices.md).
