---
title: Central de segurança do Azure para IoT pré-requisitos de versão prévia | Microsoft Docs
description: Detalhes de todos os componentes necessários para começar a usar a Central de segurança do Azure para pré-requisitos de serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 198459887ff19b16e897b2a8dde55bca1217c8ac
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616443"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Central de segurança do Azure para pré-requisitos de IoT

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação sobre os diferentes blocos de construção das Azure segurança ASC (Central) para IoT serviço, o que você precisa para começar e conceitos básicos para ajudar a entender o serviço. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Camada Standard de Hub IoT
    - Função RBAC **proprietário** privilégios de nível 
- [O espaço de trabalho do log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Central de segurança do Azure (recomendado)
    - Embora o uso da Central de segurança do Azure é apenas uma recomendação e não um requisito, sem ele, não será capaz de exibir os recursos do Azure dentro do IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Trabalhar com o ASC para o serviço de IoT

ASC para relatórios e insights de IoT estão disponíveis usando o IoT Hub do Azure e a Central de segurança do Azure. Para habilitar o ASC para IoT em seu Hub do IoT do Azure, uma conta com **proprietário** privilégios no nível é necessária. Depois de habilitar o ASC para IoT em seu IoT Hub, ASC para insights de IoT são exibidos como o **segurança** recurso no IoT Hub do Azure e como **IoT** na Central de segurança do Azure. 

## <a name="supported-service-regions"></a>Regiões de serviço com suporte 

ASC para IoT tem suporte atualmente para os Hubs de IoT nas seguintes regiões do Azure:
  - Centro dos EUA
  - Norte da Europa
  - Sudeste Asiático

## <a name="wheres-my-iot-hub"></a>Onde está meu Hub IoT?

Verifique seu local de IoT Hub para verificar a disponibilidade de serviço antes de começar. 

1. Abra seu Hub IoT. 
2. Clique em **Visão Geral**. 
3. Verifique se o local indicado corresponde a um dos [regiões de serviço com suporte](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas com suporte para agentes 

ASC para agentes do IoT dá suporte a uma lista crescente de dispositivos e plataformas. Consulte a [suporte para a lista de plataformas](how-to-deploy-agent.md) para verificar sua biblioteca de dispositivo existente ou planejada.  

## <a name="next-steps"></a>Próximas etapas
- [Visão geral](overview.md)
- [Habilitar o serviço](quickstart-onboard-iot-hub.md)
- [ASC para perguntas Frequentes de IoT](resources-frequently-asked-questions.md)
- [Noções básicas sobre o ASC para alertas de IoT](concept-security-alerts.md)
