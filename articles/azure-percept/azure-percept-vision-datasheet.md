---
title: Folha de dados da pesquisa visual do Azure Percept
description: Confira a folha de detalhes do Azure Percept Vision para obter especificações detalhadas do dispositivo
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 7bbb3a88bbc3011ec5dd917cdb0c1e49f7556aab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177157"
---
# <a name="azure-percept-vision-datasheet"></a>Folha de dados da pesquisa visual do Azure Percept

As especificações listadas abaixo são para o dispositivo Azure Percept Vision, incluído no [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Especificação do produto           |Valor     |
|--------------------------------|---------------------|
|Setores-alvo               |Produção <br> Prédios inteligentes <br> Automático <br> Varejo |
|Cenários de Heroes                  |Análise do comprador <br> Disponibilidade na prateleira <br> Reduzir redução <br> Monitoramento do local de trabalho|
|Dimensões                      |42 mm x 42 mm x 40mm (assembly de SoM do Azure Percept Vision com hospedagem) <br> 42 mm x 42 mm x 6mm (chip de SoM de visão)|
|Plano de controle de gerenciamento        |ADU (atualização de dispositivo do Azure)          |
|Software e serviços com suporte |[Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Atualização de dispositivo do Azure |
|Aceleração de ia                 |Unidade de processamento de visão (VPU) Intel Movidius infinidade X (MA2085) com o ISP de câmera Intel integrado, 0,7 TOPS |
|Sensores e indicadores visuais   |Sensor de câmera Sony IMX219 com lentes 6P<br>Resolução: 8MP em 30FPS, Distance: 50cm-Infinity<br>FoV: 120 graus diagonal, cor: amplo intervalo dinâmico, obturador com foco fixo|
|Suporte à câmera                  |RGB <br> 2 câmeras podem ser executadas simultaneamente |
|Crypto-Controller de segurança      |ST-Micro STM32L462CE      |
|Componente de ID/versão       |EEPROM de 64 KB |
|Memória                          |LPDDR4 DE 2GB     |
|Energia                           |3,5 W     |
|Portas                           |1x USB 3,0 tipo C <br> 2x MIPI 4 Lane (até 1,5 Gbps por Lane)     |
|Interfaces de controle              |2x I2C <br> SPI 2x <br> 6 vezes PWM (GPIOs: 2x Clock, 2x quadro Sync, 2x não utilizado) <br> GPIO de reposição de 2x |
|Certificação                   |NORMAS <br> IC <br> RoHS <br> IMPOSSÍVEL <br> UL   |
|Temperatura operacional           |0 a 27 graus C (assembly de SoM do Azure Percept Vision com hospedagem) <br> -10 a 70 graus C (chip de SoM de visão) |
|Temperatura do toque               |<= 48 graus C |
|Umidade relativa               |8% a 90%    |
