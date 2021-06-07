---
title: Visão geral dos RTOS do defender-IoT-micro-Agent para Azure
description: Saiba mais sobre o suporte e a implementação do defender-IoT-micro-Agent para os RTOS do Azure como parte do Azure defender para IoT.
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785896"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Visão geral: defender para IoT defender-IoT-micro-Agent para RTOS do Azure (versão prévia)

O módulo Azure defender para IoT micro fornece uma solução de segurança abrangente para dispositivos que usam os RTOS do Azure. Ele fornece cobertura para ameaças comuns e possíveis atividades mal-intencionadas em dispositivos de RTOS (sistema operacional em tempo real). Os RTOS do Azure agora são fornecidos com o Azure IoT defender-IoT-micro-Agent interno.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Visualização do defender do Azure para os RTOS de IoT.":::


O micro Module para RTOS do Azure oferece os seguintes recursos:

- Detecção de atividade de rede mal-intencionada
- Comportamento de dispositivo baseado em alerta personalizado de linha de base
- Higiene de segurança de dispositivo aprimorada

## <a name="detect-malicious-network-activities"></a>Detectar atividades de rede maliciosas

A atividade de rede de entrada e saída de cada dispositivo é monitorada. Os protocolos com suporte são TCP, UDP e ICMP em IPv4 e IPv6. O defender para IoT inspeciona cada uma dessas atividades de rede no feed do Microsoft Threat Intelligence. O feed é atualizado em tempo real com milhões de indicadores de ameaça exclusivos coletados em todo o mundo.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>A linha de base do comportamento do dispositivo baseado em alertas personalizados

A linha de base permite o clustering de dispositivos em grupos de segurança e a definição do comportamento esperado de cada grupo. Como os dispositivos IoT normalmente são projetados para operar em cenários bem definidos e limitados, é fácil criar uma linha de base que defina o comportamento esperado usando um conjunto de parâmetros. Qualquer desvio da linha de base dispara um alerta.

## <a name="improve-your-device-security-hygiene"></a>Melhorar a higiene de segurança do dispositivo

Ao usar a infraestrutura recomendada do defender para IoT, você pode obter conhecimento e informações sobre problemas em seu ambiente que afetam e danificam a postura de segurança de seus dispositivos. Uma postura de segurança de dispositivo IoT fraca pode permitir que possíveis ataques sejam bem-sucedidos se eles forem deixados inalterados. A segurança é sempre medida pelo link mais fraco em qualquer organização.

## <a name="get-started-protecting-azure-rtos-devices"></a>Introdução à proteção de dispositivos do Azure RTOS

O defender-IoT-micro-Agent para RTOS do Azure é fornecido como um download gratuito para seus dispositivos. O serviço de nuvem do defender para IoT está disponível com uma avaliação de 30 dias por assinatura do Azure. Para começar, baixe o [defender-IOT-micro-Agent para os RTOs do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o defender-IoT-micro-Agent para os RTOS do Azure. Para saber mais sobre o defender-IoT-micro-Agent e introdução, consulte os seguintes artigos:

- [Conceitos de IoT defender do Azure para os RTOS](concept-rtos-security-module.md)
- [Início rápido: RTOS do Azure IoT defender-IoT-micro-Agent](quickstart-azure-rtos-security-module.md)
