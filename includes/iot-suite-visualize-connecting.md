---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172356"
---
## <a name="view-device-telemetry"></a>Exibir telemetria de dispositivo

Você pode exibir a telemetria enviada do seu dispositivo **Device Explorer** página na solução.

1. Selecione o dispositivo provisionado na lista de dispositivos na **Device Explorer** página. Um painel exibe informações sobre seu dispositivo, incluindo um gráfico de telemetria do dispositivo:

    ![Consultar detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **Pressão** para alterar a exibição de telemetria:

    ![Exibir telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para exibir informações de diagnóstico sobre o dispositivo, role para baixo até **Diagnóstico**:

    ![Exibir diagnóstico do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no dispositivo

Para invocar métodos em seus dispositivos, use o **Device Explorer** página na solução de monitoramento remoto. Por exemplo, na solução de monitoramento remoto **Resfriador** dispositivos implementam um **reinicializar** método.

1. Escolher **dispositivos** para navegar até a **Device Explorer** página na solução.

1. Selecione o dispositivo provisionado na lista de dispositivos na **Device Explorer** página:

    ![Selecione seu dispositivo real](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para exibir uma lista dos métodos que você pode chamar em seu dispositivo, escolha **trabalhos**, em seguida, **métodos**. Para agendar um método a ser executado em vários dispositivos, você pode selecionar vários dispositivos na lista. O painel **Trabalhos** mostra os tipos de método comuns a todos os dispositivos selecionados.

1. Escolher **reinicializar**, defina o nome do trabalho **RebootPhysicalChiller** e, em seguida, escolha **aplicar**:

    ![Agendar a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens é exibida no console executando o código de dispositivo, enquanto o dispositivo simulado manipula o método.

> [!NOTE]
> Para acompanhar o status do trabalho na solução, escolha **exibir o Status do trabalho**.

## <a name="next-steps"></a>Próximas etapas

O artigo [Personalizar o acelerador de solução de Monitoramento Remoto pré-configurado](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas maneiras de personalizar o acelerador da solução.