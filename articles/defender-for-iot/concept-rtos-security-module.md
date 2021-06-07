---
title: Explicação conceitual dos conceitos básicos do defender-IoT-micro-Agent para os RTOS do Azure
description: Conheça as noções básicas sobre os conceitos e o fluxo de trabalho do defender-IoT-micro-Agent para Azure RTOS.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 83557930aeeccbb557382583e4d6666a000ce52c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779266"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Defender-IoT-micro-Agent para RTOS do Azure (versão prévia)

Use este artigo para entender melhor o defender-IoT-micro-Agent para os RTOS do Azure, incluindo recursos e benefícios, bem como links para recursos de referência e configuração relevantes. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>RTOS do Azure IoT defender-IoT-micro-agente

O defender-IoT-micro-Agent para os RTOS do Azure fornece uma solução de segurança abrangente para dispositivos do Azure RTOS como parte da oferta do NetX Duo. Na oferta do NetX Duo, os RTOS do Azure são fornecidos com o Azure IoT defender-IoT-micro-Agent interno e fornece cobertura para ameaças comuns em seus dispositivos de sistema operacional em tempo real, uma vez ativados. 

O defender-IoT-micro-Agent para RTOS do Azure é executado em segundo plano e fornece uma experiência de usuário tranqüila, ao mesmo tempo que envia mensagens de segurança usando as conexões exclusivas de cada cliente para o Hub IoT. O defender-IoT-micro-Agent para os RTOS do Azure está habilitado por padrão.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

O Azure RTOS NetX Duo é uma pilha de rede TCP/IP avançada, de nível industrial, projetada especificamente para aplicativos de IoT e em tempo real incorporados. O Azure RTOS NetX Duo é uma pilha de rede IPv4 e IPv6 dupla que fornece um conjunto avançado de protocolos, incluindo segurança e nuvem. Saiba mais sobre as soluções [do Azure RTOs NETX Duo](/azure/rtos/netx-duo/) .

O módulo oferece os seguintes recursos:

- **Detectar atividades de rede maliciosas**
- **Linhas de base de comportamento do dispositivo com base em alertas personalizados**
- **Melhorar a higiene de segurança do dispositivo**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Defender-IoT-micro-Agent para arquitetura de RTOS do Azure

O defender-IoT-micro-Agent para RTOS do Azure é inicializado pela plataforma de middleware do Azure IoT e usa clientes do Hub IoT para enviar telemetria de segurança para o Hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagrama de estado e fluxo de informações do Azure IoT defender-IoT-micro-Agent":::

O defender-IoT-micro-Agent para Azure RTOS monitora as seguintes atividades e informações do dispositivo usando três coletores:
- Atividade de rede do dispositivo **TCP**, **UDP** e **ICM**
- Informações do sistema como versões **ThreadX** e **NETX Duo**
- Eventos de pulsação

Cada coletor é vinculado a um grupo de prioridade e cada grupo de prioridade tem seu próprio intervalo com os valores possíveis de **baixo**, **médio** e **alto**. Os intervalos afetam o intervalo de tempo no qual os dados são coletados e enviados.

Cada intervalo de tempo é configurável e os conectores IoT podem ser habilitados e desabilitados para personalizar ainda mais [sua solução](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Recomendações e alertas de segurança com suporte

O defender-IoT-micro-Agent para RTOS do Azure dá suporte a alertas e recomendações de segurança específicos. Certifique-se de [examinar e personalizar os valores relevantes de alerta e recomendação](concept-rtos-security-alerts-recommendations.md) para seu serviço depois de concluir a configuração inicial.

## <a name="ready-to-begin"></a>Pronto para começar?

O defender-IoT-micro-Agent para RTOS do Azure é fornecido como um download gratuito para seus dispositivos IoT. O serviço de nuvem do defender para IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. [Baixe o defender-IOT-micro-Agent agora](https://github.com/azure-rtos/azure-iot-preview/releases) e vamos começar. 

## <a name="next-steps"></a>Próximas etapas

- Introdução ao defender-IoT-micro-Agent para os [pré-requisitos e a configuração](quickstart-azure-rtos-security-module.md)dos RTOs do Azure.
- Saiba mais sobre o defender-IoT-micro-Agent para [alertas de segurança e suporte de recomendação](concept-rtos-security-alerts-recommendations.md)dos RTOs do Azure. 
- Use o defender-IoT-micro-Agent para a [API de referência](azure-rtos-security-module-api.md)dos RTOs do Azure.