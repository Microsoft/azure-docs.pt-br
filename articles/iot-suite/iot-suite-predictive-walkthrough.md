---
title: "Passo a passo da manutenção preditiva | Microsoft Docs"
description: "Um passo a passo da solução pré-configurada de manutenção preditiva do Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: a68a8fdc3976ade0d1036d5ed58c8b2eb6d32a5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Passo a passo da solução pré-configurada de manutenção preditiva

A solução pré-configurada de manutenção preditiva é uma solução de ponta a ponta para um cenário comercial e prevê o ponto no qual há a probabilidade de ocorrer uma falha. Você pode usar essa solução pré-configurada de forma pró-ativa para atividades como a manutenção de otimização. A solução combina os principais serviços do Azure IoT Suite, como o IoT Hub, Stream Analytics e um espaço de trabalho do[Azure Machine Learning][lnk-machine-learning]. Esse espaço de trabalho contém um modelo, com base em um conjunto de dados de exemplo público, para prever a RUL (Vida Útil Restante) de um motor de aeronave. A solução implementa totalmente o cenário de negócios IoT como um ponto de partida para você planejar e implementar uma solução que atenda aos seus requisitos de negócios específicos.

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![][img-architecture]

Os itens azuis são serviços do Azure provisionados na região em que você implantou a solução pré-configurada. Exibe a lista de regiões em que você pode implantar a solução pré-configurado no [página provisionamento][lnk-azureiotsuite].

O item em verde é um dispositivo simulado que representa um motor de aeronave. Você pode aprender mais sobre esses dispositivos simulados na seção a seguir.

Os itens em cinza representam os componentes que implementam os recursos de *gerenciamento do dispositivo*. A versão atual da solução pré-configurada de manutenção preditiva não provisiona esses recursos. Para saber mais sobre o gerenciamento do dispositivo, consulte a [solução pré-configurada de monitoramento remoto][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurada, um dispositivo simulado representa um motor de aeronave. A solução é provisionada com dois motores que mapeiam uma única aeronave. Cada motor emite quatro tipos de telemetria: o Sensor 9, Sensor 11, Sensor 14 e Sensor 15 fornecem os dados necessários para o modelo de Machine Learning calcular a RUL do motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao Hub IoT:

*Contagem de ciclos*. Um ciclo representa um voo concluído com uma duração entre duas e dez horas. Durante o voo, dados de telemetria são capturados a cada meia hora.

*Telemetria*. Há quatro sensores que representam os atributos do motor. Os sensores são rotulados genericamente de Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Esses quatro sensores representam telemetria suficiente para obter resultados úteis do modelo de regra. O modelo usado na solução pré-configurada é criado de um conjunto de dados público que inclui dados de sensor do mecanismo real. Para saber mais sobre como o modelo foi criado no conjunto de dados original, confira o [Modelo de manutenção preditiva da Galeria do Cortana Intelligence][lnk-cortana-analytics].

Os dispositivos simulados podem lidar com os seguintes comandos enviados do hub IoT na solução:

| Command | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O Hub IoT fornece reconhecimento de comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabalho do Stream Analytics do Azure

**Trabalho: Telemetria** funciona no fluxo de entrada da telemetria do dispositivo usando duas instruções:

* O primeiro seleciona toda a telemetria dos dispositivos e envia os dados para o armazenamento de blobs. Aqui, ele é visualizado no aplicativo Web.
* A segunda calcula os valores médios do sensor em uma janela deslizante de dois minutos e envia esses dados por meio do Hub de Eventos para um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **host do processador de eventos** é executado em um trabalho de Web do Azure. O **processador de eventos** obtém os valores do sensor médios para um ciclo completo. Ele passa esses valores para uma API que expõe o modelo treinado para calcular a RUL para um motor. A API é exposta por um espaço de trabalho do Machine Learning é provisionado como parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning usa um modelo derivado dos dados coletados de mecanismos de aeronave real. Você pode navegar até o Espaço de Trabalho do Machine Learning do bloco na página [azureiotsuite.com][lnk-azureiotsuite] para sua solução provisionada. O bloco fica disponível quando a solução fica no estado **Pronto**.


## <a name="next-steps"></a>Próximas etapas
Agora você já viu os principais componentes da solução de previsão manutenção pré-configurado, convém personalizá-lo. Veja [Orientação sobre como personalizar soluções pré-configuradas][lnk-customize].

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Perguntas frequentes sobre o IoT Suite][lnk-faq]
* [Segurança IoT desde o início][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/