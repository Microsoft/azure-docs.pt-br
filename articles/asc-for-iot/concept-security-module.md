---
title: Entender a Central de segurança do Azure para o módulo de segurança de IoT gêmeos Preview | Microsoft Docs
description: Saiba mais sobre o conceito de Gêmeos de módulo de segurança e como eles são usados na Central de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358807"
---
# <a name="security-module"></a>Módulo de segurança

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como o Azure Security Center (ASC) para IoT usa dispositivos gêmeos e módulos. 

## <a name="device-twins"></a>Dispositivos gêmeos

Para soluções de IoT criadas no Azure, dispositivos gêmeos desempenham um papel fundamental tanto no gerenciamento do dispositivo quanto na automação do processo.  

A ASC para IoT oferece uma integração completa com sua plataforma de gerenciamento de dispositivo IoT existente, permitindo que você gerencie seu status de segurança de dispositivo, bem como faça o uso de recursos de controle de dispositivo existente. Integração é obtida por fazendo uso do IoT Hub de mecanismo de gêmeos.  

Saiba mais sobre o conceito de [gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) no IoT Hub do Azure. 

## <a name="security-module-twins"></a>Gêmeos de módulo de segurança

ASC para IoT mantém um gêmeo de módulo de segurança para cada dispositivo no serviço.
O gêmeo do módulo de segurança contém todas as informações relevantes para a segurança de dispositivo para cada dispositivo específico em sua solução.
Propriedades de segurança de dispositivo são mantidas em um gêmeo de módulo de segurança dedicado para comunicação mais segura e para habilitar as atualizações e manutenção que exigem menos recursos.  

Ver [gêmeo do módulo de segurança de criação](quickstart-create-security-twin.md) e [configurar agentes de segurança](how-to-agent-configuration.md) para aprender a criar, personalizar e configurar o gêmeo. Ver [Gêmeos de módulo de compreensão](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) para saber mais sobre o conceito de módulos gêmeos no IoT Hub. 
 

## <a name="see-also"></a>Consulte também
- [ASC para versão prévia do IoT](overview.md)
- [Implantar agentes de segurança](how-to-deploy-agent.md)
- [Métodos de autenticação do agente de segurança](concept-security-agent-authentication-methods.md)