---
title: Explicação conceitual dos conceitos básicos do módulo de segurança para os RTOS do Azure
description: Conheça as noções básicas sobre o módulo de segurança para os conceitos e o fluxo de trabalho dos RTOS do Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 8f521bd593369509fd520831f90ce9c601227f09
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340043"
---
# <a name="security-module-for-azure-rtos-preview"></a>Módulo de segurança para RTOS do Azure (versão prévia)

Use este artigo para entender melhor o módulo de segurança para os RTOS do Azure, incluindo recursos e benefícios, bem como links para recursos relevantes de configuração e referência. 

## <a name="azure-rtos-iot-security-module"></a>Módulo de segurança de IoT dos RTOS do Azure

O módulo de segurança para os RTOS do Azure fornece uma solução de segurança abrangente para dispositivos do Azure RTOS como parte da oferta do NetX Duo. Na oferta do NetX Duo, os RTOS do Azure são fornecidos com o módulo de segurança do Azure IoT integrado e fornecem cobertura para ameaças comuns em seus dispositivos de sistema operacional em tempo real, uma vez ativados. 

O módulo de segurança para os RTOS do Azure é executado em segundo plano e fornece uma experiência de usuário tranqüila, ao mesmo tempo que envia mensagens de segurança usando as conexões exclusivas de cada cliente para o Hub IoT. O módulo de segurança para os RTOS do Azure está habilitado por padrão.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

O Azure RTOS NetX Duo é uma pilha de rede TCP/IP avançada, de nível industrial, projetada especificamente para aplicativos de IoT e em tempo real incorporados. O Azure RTOS NetX Duo é uma pilha de rede IPv4 e IPv6 dupla que fornece um conjunto avançado de protocolos, incluindo segurança e nuvem. Saiba mais sobre as soluções [do Azure RTOs NETX Duo](/azure/rtos/netx-duo/) .

O módulo oferece os seguintes recursos:

- **Detectar atividades de rede maliciosas**
- **Linhas de base de comportamento do dispositivo com base em alertas personalizados**
- **Melhorar a higiene de segurança do dispositivo**

## <a name="security-module-for-azure-rtos-architecture"></a>Módulo de segurança para a arquitetura de RTOS do Azure

O módulo de segurança para os RTOS do Azure é inicializado pela plataforma de middleware do Azure IoT e usa clientes do Hub IoT para enviar telemetria de segurança para o Hub.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Diagrama de estado do módulo de segurança IoT do Azure e fluxo de informações":::

O módulo de segurança para os RTOS do Azure monitora as seguintes atividades e informações do dispositivo usando três coletores:
- Atividade de rede do dispositivo **TCP**, **UDP** e **ICM**
- Informações do sistema como versões **ThreadX** e **NETX Duo**
- Eventos de pulsação

Cada coletor é vinculado a um grupo de prioridade e cada grupo de prioridade tem seu próprio intervalo com os valores possíveis de **baixo**, **médio** e **alto**. Os intervalos afetam o intervalo de tempo no qual os dados são coletados e enviados.

Cada intervalo de tempo é configurável e os conectores IoT podem ser habilitados e desabilitados para personalizar ainda mais [sua solução](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Recomendações e alertas de segurança com suporte

O módulo de segurança para os RTOS do Azure dá suporte a alertas e recomendações de segurança específicos. Certifique-se de [examinar e personalizar os valores relevantes de alerta e recomendação](concept-rtos-security-alerts-recommendations.md) para seu serviço depois de concluir a configuração inicial.

## <a name="ready-to-begin"></a>Pronto para começar?

O módulo de segurança para os RTOS do Azure é fornecido como um download gratuito para seus dispositivos IoT. O serviço de nuvem do defender para IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. [Baixe o módulo de segurança agora](https://github.com/azure-rtos/azure-iot-preview/releases) e vamos começar. 

## <a name="next-steps"></a>Próximas etapas

- Introdução ao módulo de segurança para os [pré-requisitos e a configuração](quickstart-azure-rtos-security-module.md)dos RTOs do Azure.
- Saiba mais sobre o módulo de segurança para [alertas de segurança e suporte de recomendação](concept-rtos-security-alerts-recommendations.md)dos RTOs do Azure. 
- Use o módulo de segurança para a [API de referência](azure-rtos-security-module-api.md)dos RTOs do Azure.