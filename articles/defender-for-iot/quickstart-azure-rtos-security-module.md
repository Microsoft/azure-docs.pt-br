---
title: 'Início Rápido: Configurar e habilitar microagente do Defender para IoT para o Azure RTOS'
description: Saiba como integrar e habilitar o microagente do Defender para IoT para o serviço do Azure RTOS em seu Hub IoT do Azure.
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: db0fc0be3cd10bc57a46cb5a056ca5e057ae255a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781153"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Início Rápido: microagente do Defender para IoT para o Azure RTOS (versão prévia)

Este artigo fornece uma explicação dos pré-requisitos antes de começar e explica como habilitar o microagente do Defender para IoT para o serviço do Azure RTOS em um Hub IoT. Se você não tiver, no momento, um Hub IoT, confira [Criar um Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md) para começar a usar.

## <a name="prerequisites"></a>Pré-requisitos 

### <a name="supported-devices"></a>Dispositivos com suporte

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Baixe, compile e execute um dos arquivos .zip referentes à placa e à ferramenta específicas (IAR, IDE do semi ou PC) de sua escolha no [recurso do microagente do Defender para IoT para o Azure RTOS no GitHub](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Recursos do Azure

A próxima fase para começar é preparar seus recursos do Azure. Você precisará de um Hub IoT, e sugerimos um workspace do Log Analytics. Para o Hub IoT, você precisará da cadeia de conexão do Hub IoT para se conectar ao seu dispositivo. 
  
### <a name="iot-hub-connection"></a>Cadeia Hub IoT

Uma conexão do Hub IoT é necessária para começar. 

1. Abra o **Hub IoT** no portal do Azure.

1. Procure **Dispositivos IoT**.

1. Selecione **Criar**.

1. Copie a cadeia de conexão do IoT no [arquivo de configuração](how-to-azure-rtos-security-module.md).

As credenciais de conexão são obtidas da configuração do aplicativo do usuário **HOST_NAME**, **DEVICE_ID** e **DEVICE_SYMMETRIC_KEY**.

O microagente do Defender para IoT para o Azure RTOS usa conexões de middleware do Azure IoT com base no protocolo **MQTT**.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para concluir a configuração e a personalização da solução.

> [!div class="nextstepaction"]
> [Configurar e personalizar o microagente do Defender para IoT para o Azure RTOS (versão prévia)](how-to-azure-rtos-security-module.md)
