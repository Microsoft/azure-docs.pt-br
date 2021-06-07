---
title: Início Rápido – Monitorar seus dispositivos no Azure IoT Central
description: Início Rápido – Como operador, aprenda a usar o aplicativo do Azure IoT Central para monitorar os dispositivos.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833874"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Início Rápido: Use o Azure IoT Central para monitorar seus dispositivos

*Este artigo aplica-se a operadores, construtores e administradores.*

Este início rápido mostra a você, como um operador, como usar o seu aplicativo do Microsoft Azure IoT Central para monitorar seus dispositivos e alterar as configurações.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve concluir os três inícios rápidos anteriores [Criar um aplicativo do Azure IoT Central](./quick-deploy-iot-central.md), [Adicionar um dispositivo simulado ao seu aplicativo do IoT Central](./quick-create-simulated-device.md) e [Configurar regras e ações para seu dispositivo](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Receber uma notificação

O Azure IoT Central envia notificações sobre dispositivos como mensagens de email. Como construtor, você adicionou uma regra para enviar uma notificação a um operador quando a umidade em um sensor de dispositivo conectado excedesse um limite. Como operador, você verifica seus emails para ver se há notificações.

Abra a mensagem de email recebida no final do início rápido [Configurar regras e ações para o seu dispositivo](quick-configure-rules.md). No email, selecione o link para o dispositivo:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Captura de tela que mostra o email de notificação":::

A exibição **Visão geral** do dispositivo simulado que você criou nos inícios rápidos anteriores é aberta no navegador:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Captura de tela que mostra a visão geral do dispositivo que disparou a notificação":::

## <a name="investigate-an-issue"></a>Investigar um problema

Como um operador, você pode exibir informações sobre o dispositivo nas exibições **Visão geral**, **Sobre** e **Comandos**. O construtor criou uma exibição **Gerenciar dispositivos** para que você edite informações do dispositivo e defina as propriedades do dispositivo.

O gráfico no painel mostra um gráfico da umidade do dispositivo. Você decide se a umidade do dispositivo está muito alta.

## <a name="remediate-an-issue"></a>Resolver um problema

Para fazer uma alteração no dispositivo, use a página **Gerenciar dispositivo**.

Altere a **Temperatura de destino** para 80 para aquecer o dispositivo e reduzir a umidade. Escolha **Salvar** para atualizar o dispositivo. Quando o dispositivo confirma as alterações de configuração, o status da propriedade muda para **sincronizado**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Captura de tela que mostra a configuração da temperatura de destino atualizada para o dispositivo":::

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Receber uma notificação
* Investigar um problema
* Resolver um problema

Agora que você sabe como monitorar o dispositivo, a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Criar e gerenciar um modelo de dispositivo](howto-set-up-template.md).
