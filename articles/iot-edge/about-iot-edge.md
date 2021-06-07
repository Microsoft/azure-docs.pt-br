---
title: O que é Azure IoT Edge | Microsoft Docs
description: Visão geral do serviço Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: b9b768d7af276b8bff0db2acd00d27eca03023c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026727"
---
# <a name="what-is-azure-iot-edge"></a>O que é o Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge move análises de nuvem e lógica de negócios personalizada para dispositivos, de modo que sua organização pode se concentrar em ideias de negócios em vez de gerenciamento de dados. Expanda sua solução de IoT ao empacotar sua lógica de negócios em contêineres padrão, então você pode implantar esses contêineres para qualquer um dos seus dispositivos e monitorar tudo na nuvem.

A análise impulsiona o valor comercial em soluções de IoT, mas nem todas as análise precisam estar na nuvem. Se você quiser responder a emergências assim que possível, poderá executar cargas de trabalho de detecção de anomalias na borda. Se você deseja reduzir os custos de largura de banda e evitar a transferência de terabytes de dados brutos, pode limpar e agregar os dados localmente e só enviar as informações para a nuvem para análise.

O Azure IoT Edge é composto de três componentes:

* Os **módulos do IoT Edge** são contêineres que executam serviços do Azure, serviços de terceiros ou o seu próprio código. Os módulos são implantados em dispositivos do IoT Edge e executados localmente nesses dispositivos.
* O **runtime do IoT Edge** é executado em cada dispositivo IoT Edge e gerencia os módulos implantados em cada dispositivo.
* Uma **interface baseada em nuvem** permite monitorar e gerenciar dispositivos do IoT Edge remotamente.

>[!NOTE]
>O Azure IoT Edge só está disponível na camada Standard do Hub IoT. A camada gratuita destina-se a testes e avaliação apenas. Para saber mais sobre as camadas Básica e Standard, confira [Como escolher a camada certa do Hub IoT](../iot-hub/iot-hub-scaling.md).

## <a name="iot-edge-modules"></a>Módulos do IoT Edge

Os módulos do IoT Edge são unidades de execução, implementados como contêineres compatíveis com o Docker, que executam a lógica de negócios na borda. Vários módulos podem ser configurados para se comunicar entre si, criando um pipeline de processamento de dados. Você pode desenvolver módulos personalizados ou empacotar determinados serviços do Azure em módulos que fornecem informações offline e na borda.

### <a name="artificial-intelligence-at-the-edge"></a>Inteligência artificial na borda

O Azure IoT Edge permite implantar processamento de eventos complexos, aprendizado de máquina, reconhecimento de imagem e outros tipos de IA de alto valor sem gravá-la internamente. Serviços do Azure, como Azure Functions, Azure Stream Analytics e Azure Machine Learning, podem ser executados localmente por meio do Azure IoT Edge. Porém, você não está limitado aos serviços do Azure. Qualquer pessoa pode criar os módulos de IA e disponibilizá-los para uso pela comunidade por meio do Azure Marketplace.

### <a name="bring-your-own-code"></a>Traga o seu próprio código

Quando você deseja implantar seu próprio código para seus dispositivos, o Azure IoT Edge também oferece suporte para essa situação. O Azure IoT Edge mantém o mesmo modelo de programação de outros serviços Azure IoT. É possível executar o mesmo código em um dispositivo ou na nuvem. O Azure IoT Edge oferece suporte para Linux e Windows, portanto você pode codificar para a plataforma de sua escolha. Ele dá suporte para Java, .NET Core 2.0, Node.js, C e Python, de modo que os desenvolvedores possam codificar em uma linguagem que já conhecem e usar a lógica de negócios existente.

## <a name="iot-edge-runtime"></a>runtime do IoT Edge

O runtime do Azure IoT Edge permite lógica personalizada e de nuvem em dispositivos IoT Edge. O runtime se encontra no dispositivo IoT Edge e executa operações de gerenciamento e comunicação. O runtime executa várias funções:

* Instala e atualiza cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Garante que os módulos do IoT Edge sempre estão em execução.
* Fornece um relatório sobre a integridade do módulo para a nuvem para o monitoramento remoto.
* Gerencia a comunicação entre dispositivos de folha downstream e um dispositivo do IoT Edge, entre módulos em um dispositivo do IoT Edge e entre um dispositivo do IoT Edge e a nuvem.

![O runtime do IoT Edge envia informações e relatórios para o Hub IoT](./media/about-iot-edge/runtime.png)

A maneira como você usa um dispositivo do Azure IoT Edge é uma decisão inteiramente sua. O runtime costuma ser usado para implantar a IA em dispositivos de gateway que agregam e processam dados de outros dispositivos locais; no entanto, esse modelo de implantação é apenas uma opção.

O runtime do Azure IoT Edge é executado em um grande conjunto de dispositivos IoT, o que permite que seja usado de várias maneiras. Ele dá suporte a sistemas operacionais Windows e Linux e abstrai os detalhes de hardware. Use um dispositivo anterior ao Raspberry Pi 3 caso não esteja processando muitos dados ou um servidor industrial para executar cargas de trabalho com uso intensivo de recursos.

## <a name="iot-edge-cloud-interface"></a>Interface de nuvem do IoT Edge

É difícil gerenciar o ciclo de vida de software em milhões de dispositivos de IoT que geralmente são de modelos e marcas diferentes ou estão geograficamente dispersos. Cargas de trabalho são criadas e configuradas para um determinado tipo de dispositivo, implantadas em todos os seus dispositivos e monitoradas para detectar todos os dispositivos com comportamento inadequado. Essas atividades não podem ser feitas por dispositivo e devem ser feitas em escala.

O Azure IoT Edge integra-se perfeitamente aos aceleradores da solução Azure IoT para fornecer um plano de controle de acordo com as necessidades da sua solução. Os serviços de nuvem permitem:

* Criar e configurar uma carga de trabalho a ser executado em um tipo específico de dispositivo.
* Enviar uma carga de trabalho para um conjunto de dispositivos.
* Monitorar cargas de trabalho que estão sendo executadas em dispositivos em campo.

![Telemetria e ações do dispositivo são coordenadas com a nuvem](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Próximas etapas

Experimente estes conceitos implantando seu primeiro módulo do IoT Edge em um dispositivo:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* [Implantar módulos em um dispositivo IoT Edge do Linux](quickstart-linux.md)
* [Implantar módulos em um dispositivo IoT Edge do Windows](quickstart.md)

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

[Implantar módulos em um dispositivo do IoT Edge](quickstart-linux.md)

:::moniker-end
