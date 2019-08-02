---
title: Noções básicas sobre Central de segurança do Azure para a arquitetura da solução IoT Preview | Microsoft Docs
description: Saiba mais sobre o fluxo de informações na Central de segurança do Azure para o serviço de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 5cab57343f9675df0702cf4281b3d74ca96d86ae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618540"
---
# <a name="azure-security-center-for-iot-architecture"></a>Central de segurança do Azure para a arquitetura da IoT

Este artigo explica a arquitetura funcional do sistema do Azure segurança ASC (Central) para a solução de IoT. 

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC para componentes de IoT

ASC para IoT é composto dos seguintes componentes:
- Agentes de dispositivo
- Enviar mensagem de segurança do SDK
- Integração do IoT Hub
- Pipeline de análise
 
### <a name="asc-for-iot-workflow"></a>ASC para fluxo de trabalho de IoT

ASC para agentes do dispositivo IoT permite que você colete facilmente os eventos de segurança bruto de seus dispositivos. Eventos de segurança brutas podem incluir conexões, criação de processo, os logons de usuário e outras informações relevantes de segurança IP. ASC para agentes do dispositivo IoT também lidar com a agregação de eventos para ajudar a evitar a taxa de transferência de rede alta. Os agentes são altamente personalizáveis, permitindo que você usá-los para tarefas específicas, como enviar somente informações importantes no SLA mais rápido, ou para agregar informações de segurança abrangente e o contexto em segmentos maiores, evitando os custos mais altos de serviço.
 
Agentes de dispositivo e outro aplicativos usam o **ASC Azure enviar mensagem de segurança SDK** para enviar informações de segurança para o IoT Hub do Azure. O IoT Hub obtém essas informações e os encaminha para o ASC para o serviço de IoT.

Depois que o ASC para o serviço IoT estiver habilitada, além dos dados encaminhados, o IoT Hub também envia todos os seus dados internos para análise pelo ASC para IoT. Esses dados incluem logs de operação de nuvem de dispositivo, identidades de dispositivo e a configuração de Hub. Todas essas informações ajuda a criar o ASC para o pipeline de análise de IoT.
 
ASC para o pipeline de análise de IoT também recebe fluxos de inteligência de ameaças adicionais de várias fontes da Microsoft e Microsoft parceiros. O ASC para o pipeline de análise inteira IoT funciona com todas as configurações de cliente feita no serviço (como alertas personalizados e o uso da mensagem de segurança de envio do SDK).
 
Usando o pipeline de análise, ASC para IoT combina todos os fluxos de informações para gerar alertas e recomendações viáveis. O pipeline contém ambas as regras personalizadas criadas pelos pesquisadores de segurança e especialistas, bem como modelos procurando desvio da análise de comportamento e o risco de dispositivo padrão do machine learning.
 
ASC para IoT recomendações e alertas (saída do pipeline de análise) é gravado no espaço de trabalho do Log Analytics de cada cliente. Incluindo os eventos brutos no espaço de trabalho, bem como os alertas e recomendações permite que consultas usando os detalhes exatos de atividades suspeitas detectadas e investigações de aprofundamento.  

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura básica e o fluxo de trabalho do ASC para solução de IoT. Para saber mais sobre os pré-requisitos, como começar e permitir que sua solução de segurança no IoT Hub, consulte os seguintes artigos:

- [Pré-requisitos do serviço](service-prerequisites.md)
- [Guia de Introdução](getting-started.md)
- [Configurar sua solução](quickstart-configure-your-solution.md)
- [Habilitar a segurança no IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC para perguntas Frequentes de IoT](resources-frequently-asked-questions.md)
- [ASC para alertas de segurança de IoT](concept-security-alerts.md)

