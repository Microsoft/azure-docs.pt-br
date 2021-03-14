---
title: 'Início Rápido: Configurar e habilitar o Módulo de Segurança para o Azure RTOS'
description: Saiba como integrar e habilitar o Módulo de Segurança para o serviço do Azure RTOS em seu Hub IoT do Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: a11a8ec2d8eb82950cf0ab55eb6ca1913b41c84a
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522930"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Início Rápido: Módulo de Segurança para o Azure RTOS (versão prévia)

Este artigo fornece uma explicação dos pré-requisitos antes de começar e explica como habilitar o Módulo de Segurança para o serviço do Azure RTOS em um Hub IoT. Se você não tiver, no momento, um Hub IoT, confira [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md) para começar a usar.

## <a name="prerequisites"></a>Pré-requisitos 

### <a name="supported-devices"></a>Dispositivos com suporte

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Baixe, compile e execute um dos arquivos .zip referentes à placa e à ferramenta específicas (IAR, IDE do semi ou PC) de sua escolha no [recurso do Módulo de Segurança para o Azure RTOS no GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Recursos do Azure

A próxima fase para começar é preparar seus recursos do Azure. Você precisará de um Hub IoT, e sugerimos um workspace do Log Analytics. Para o Hub IoT, você precisará da cadeia de conexão do Hub IoT para se conectar ao seu dispositivo. 
  
### <a name="iot-hub-connection"></a>Cadeia Hub IoT

Uma conexão do Hub IoT é necessária para começar. 

1. Abra o **Hub IoT** no portal do Azure.

1. Procure **Dispositivos IoT**.

1. Selecione **Criar**.

1. Copie a cadeia de conexão do IoT no [arquivo de configuração](how-to-azure-rtos-security-module.md).

As credenciais de conexão são obtidas da configuração do aplicativo do usuário **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

O Módulo de Segurança para os Azure RTOS usa conexões de middleware do Azure IoT com base no protocolo **MQTT**.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para concluir a configuração e a personalização da solução.

> [!div class="nextstepaction"]
> [Configurar o Módulo de Segurança para o Azure RTOS](how-to-azure-rtos-security-module.md)
