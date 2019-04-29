---
title: Visão geral do acelerador da solução de Manutenção Preditiva - Azure | Microsoft Docs
description: Uma visão geral do acelerador da solução de Manutenção Preditiva do Azure IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3387996dc0e1953eaafee9c4c61eb8faa865b654
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447484"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Visão geral do acelerador de solução de Manutenção Preditiva

O acelerador de solução de Manutenção Preditiva é uma solução de ponta a ponta para um cenário comercial e prevê o ponto no qual há a probabilidade de ocorrer uma falha. Você pode usar esse acelerador de solução de forma pró-ativa para atividades como a manutenção de otimização. A solução combina os principais serviços de aceleradores de solução de IoT do Azure, como Hub IoT e um espaço de trabalho do [Aprendizado de Máquina do Azure][lnk-machine-learning]. Esse workspace contém um modelo, com base em um conjunto de dados de exemplo público, para prever a RUL (Vida Útil Restante) de um motor de aeronave. A solução implementa totalmente o cenário de negócios IoT como um ponto de partida para você planejar e implementar uma solução que atenda aos seus requisitos de negócios específicos.

O código do acelerador da solução de Manutenção Preditiva [está disponível no GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos do acelerador de solução:

![Arquitetura lógica][img-architecture]

Os itens azuis são serviços do Azure provisionados na região em que você implantou o acelerador de solução. Exibe a lista de regiões em que você pode implantar o acelerador de solução no [página provisionamento][lnk-azureiotsolutions].

O item em verde é um mecanismo de aeronave simulada. Você pode aprender mais sobre esses dispositivos simulados na seção [Dispositivos simulados](#simulated-devices).

Os itens cinzas são componentes que implementam os recursos de *gerenciamento de dispositivos*. A versão atual do acelerador de solução de Manutenção Preditiva não provisiona esses recursos. Para saber mais sobre o gerenciamento do dispositivo, confira o [acelerador da solução de monitoramento remoto][lnk-remote-monitoring].

## <a name="azure-resources"></a>Recursos do Azure

No portal do Azure, navegue até o grupo de recursos com o nome da solução escolhido para exibir os recursos provisionados.

![Recursos do acelerador][img-resource-group]

Quando você provisiona o acelerador da solução, recebe um email com um link para o workspace de Machine Learning. Você também pode navegar até o espaço de trabalho Aprendizado de Máquina da página [Aceleradores de Solução IoT do Microsoft Azure][lnk-azureiotsolutions]. Um bloco fica disponível nessa página quando a solução entra no estado **pronto**.

![Modelo de Machine Learning][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivos simulados

No solution accelerator, um dispositivo simulado é um mecanismo de aeronave. A solução é provisionada com dois motores que mapeiam uma única aeronave. Cada motor emite quatro tipos de telemetria: O Sensor 9, Sensor 11, Sensor 14 e Sensor 15 fornecem os dados necessários para o modelo de Machine Learning calcular a RUL do motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao Hub IoT:

*Contagem de ciclos*. Um ciclo é um voo concluído com uma duração entre duas e dez horas. Durante o voo, dados de telemetria são capturados a cada meia hora.

*Telemetria*. Existem quatro sensores que registram os atributos do mecanismo. Os sensores são rotulados genericamente de Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Esses quatro sensores enviam a telemetria suficiente para obter resultados úteis do modelo RUL. O modelo usado no acelerador de solução é criado de um conjunto de dados público que inclui dados de sensor do mecanismo real. Para saber mais sobre como o modelo foi criado no conjunto de dados original, confira o [Modelo de manutenção preditiva da Galeria do Cortana Intelligence][lnk-cortana-analytics].

Os dispositivos simulados podem lidar com os seguintes comandos enviados do hub IoT na solução:

| Comando | DESCRIÇÃO |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O Hub IoT fornece reconhecimento de comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Trabalho do Stream Analytics do Azure

**Trabalho: Telemetria** funciona no fluxo de entrada da telemetria do dispositivo usando duas instruções:

* O primeiro seleciona toda a telemetria dos dispositivos e envia os dados para o armazenamento de blobs. A partir daqui, ele é visualizado no aplicativo web.
* A segunda calcula os valores médios do sensor em uma janela deslizante de dois minutos e envia esses dados por meio do Hub de Eventos para um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **host do processador de eventos** é executado em um trabalho de Web do Azure. O **processador de eventos** obtém os valores do sensor médios para um ciclo completo. Em seguida, ele passa esses valores para um modelo treinado que calcula o RUL para um mecanismo. Uma API fornece acesso ao modelo em um espaço de trabalho de Aprendizado de Máquina que faz parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning usa um modelo derivado dos dados coletados de mecanismos de aeronave real. Você pode navegar até o espaço de trabalho do Machine Learning do bloco da sua solução na [azureiotsolutions.com] [ lnk-azureiotsolutions] página. O bloco fica disponível quando a solução fica no estado **Pronto**.

O modelo Aprendizado de Máquina está disponível como um modelo que mostra como trabalhar com a telemetria coletada por meio dos serviços aceleradores de solução de IoT. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um mecanismo de aeronave baseado em dados<sup>\[1\]</sup> disponíveis ao público e diretrizes passo a passo sobre como usar o modelo.

O acelerador da solução de Manutenção Preditiva de IoT do Azure usa o modelo de regressão criado com base neste modelo. O modelo é implantado em sua assinatura do Azure e disponibilizado por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste para 4 (de um total de 100) motores e os 4 (de um total de 21) fluxos de dados de sensores. Esses dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). “Turbofan Engine Degradation Simulation Data Set” (Conjunto de dados da simulação de degradação do turbofan), Repositório de dados de prognóstico da NASA Ames (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Próximas etapas
Agora você já viu os principais componentes do acelerador de solução de Manutenção Preditiva, convém personalizá-lo.

Você também pode explorar alguns dos outros recursos de aceleradores de solução IoT:

* [Perguntas frequentes sobre os aceleradores de solução do IoT][lnk-faq]
* [Segurança IoT desde o início][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
