---
title: Usar Plug and Play de IoT em dispositivos restritos | Microsoft Docs
description: Saiba como você pode implementar o Plug and Play IoT em dispositivos restritos usando o SDK para os RTOS inseridos do C ou do Azure.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d61ca10612a0935f8483745d164835d7498280c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92042806"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementar Plug and Play IoT em dispositivos restritos

Se você estiver desenvolvendo para *dispositivos restritos*, poderá usar o plug and Play de IOT com o [SDK do Azure para bibliotecas de cliente IOT C](https://aka.ms/embeddedcsdk) ou com os [RTOs do Azure](/azure/rtos/overview-rtos). Este artigo inclui links e recursos para esses cenários restritos.

## <a name="use-the-sdk-for-embedded-c"></a>Usar o SDK para C inserido

O SDK do C inserido oferece uma solução leve para conectar dispositivos restritos aos serviços de IoT do Azure, incluindo o uso das [convenções de plug and Play de IOT](concepts-convention.md). Os links a seguir incluem exemplos de dispositivo real e para fins educacionais e de depuração.

### <a name="use-a-real-device"></a>Usar um dispositivo real

Para obter um tutorial completo de ponta a ponta usando o SDK para C inserido, o serviço de provisionamento de dispositivos e a Plug and Play de IoT em um dispositivo real, consulte [repropósito PIC-IoT placa de desenvolvimento do WX para se conectar ao Azure por meio do serviço de provisionamento de dispositivos no Hub IOT](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Exemplos introdutórios

O SDK do repositório C inserido contém [vários exemplos](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) que mostram como usar o plug and Play de IOT:

> [!NOTE]
> Esses exemplos são mostrados em execução no Windows e no Linux para fins educacionais e de depuração. Em um cenário de produção, os exemplos destinam-se apenas a dispositivos restritos.

- [Exemplo de termostato com o SDK para C inserido](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Exemplo de controlador de temperatura com o SDK para C inserido](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Usando os RTOS do Azure

Os RTOS do Azure incluem uma camada leve que adiciona conectividade nativa aos serviços de nuvem do Azure IoT. Essa camada fornece um mecanismo simples para conectar dispositivos restritos ao IoT do Azure ao usar os recursos avançados dos RTOS do Azure. Para saber mais, confira o [que é Microsoft Azure RTOs](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Cadeias

Os exemplos de RTOS do Azure são fornecidos com tipos diferentes de combinações de IDE e ferramentas, como:

- IAR: IDE de [Workbench inserido](https://www.iar.com/iar-embedded-workbench/) da Iar
- GCC/CMake: Crie com base no sistema de Build [CMake](https://cmake.org/) de software livre e no [ferramentas de ARM do GNU inserido](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: NXP [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronic [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) do microchip

### <a name="samples"></a>Exemplos

Para obter exemplos que mostram como começar em dispositivos diferentes com os RTOS do Azure e Plug and Play de IoT, consulte a tabela a seguir:

Fabricante | Dispositivo | Exemplos |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [Gcc/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [Iar](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [Gcc/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [Iar](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [Iar](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [Gcc/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [Iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [Gcc/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [Iar](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre as opções para implementar o IoT Plug and Play em dispositivos restritos, uma próxima etapa sugerida é aprender sobre as [convenções de plug and Play de IOT](concepts-convention.md).