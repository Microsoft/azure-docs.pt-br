---
title: "Hub IoT do Azure – introdução à conexão de dispositivos IoT à nuvem | Microsoft Docs"
description: "Saiba como conectar seus quadros IoT e kits de início ao Hub IoT do Azure. Seus dispositivos podem enviar telemetria ao Hub IoT e o Hub IoT pode monitorar e gerenciar seus dispositivos."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: tutorial do hub iot do azure
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 45016e6383761ffe78f13ccef1112ab3d9753498
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriais de introdução ao Hub IoT do Azure

Você pode usar o Hub IoT do Azure e os SDKs do dispositivo IoT do Azure para criar soluções de IoT (Internet das Coisas):

* O Hub IoT do Azure é um serviço completamente gerenciado na nuvem que se conecta com segurança aos seus dispositivos IoT, monitorando-os e gerenciando-os. Use os SDKs do dispositivo IoT do Azure para implementar os dispositivos IoT.
* Use um gateway IoT em cenários de IoT mais complexos. Por exemplo, quando você precisa considerar fatores como dispositivos herdados, custos de largura de banda, políticas de segurança e privacidade ou processamento de dados de borda. Nesses cenários, você pode usar o Azure IoT Edge para implementar um gateway que conecte dispositivos ao seu hub IoT.

## <a name="what-the-tutorials-cover"></a>O que os tutoriais abordam

Estes tutoriais apresentam a você o Hub IoT do Azure e os SDKs do dispositivo. Os tutoriais abordam os cenários comuns de IoT para demonstrar os recursos do Hub IoT. Os tutoriais também ilustram como combinar o Hub IoT com outros serviços e ferramentas do Azure para criar soluções de IoT mais eficazes. Nos tutoriais, você pode optar por usar dispositivos IoT simulados ou reais. Além disso, você pode aprender como usar um gateway de modo a habilitar dispositivos para conectar ao seu Hub IoT.

## <a name="set-up-your-device"></a>Configurar seu dispositivo

Conecte um dispositivo IoT ou gateway ao Hub IoT do Azure. Você pode escolher um dispositivo real ou simulado para começar:

| Dispositivo IoT                       | Linguagem de programação |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| Kit de Desenvolvimento da IoT                       | [Arduino no VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Dispositivo simulado no computador           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
| Simulador de dispositivo online         | [Raspberry Pi (Node.js)][Ol_Sim] |

Além disso, você pode usar um gateway IoT Edge para habilitar os dispositivos a conectarem-se ao seu Hub IoT:

| Dispositivos de gateway               | Linguagem de programação | Plataforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modelo DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Gateway simulado            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
