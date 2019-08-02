---
title: Noções básicas sobre Central de segurança do Azure para o módulo de segurança de IoT para o IoT Edge | Microsoft Docs
description: Compreenda a arquitetura e os recursos da Central de segurança do Azure para o módulo de segurança de IoT para o IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4581f66a3401764237621bee86228aac724ec0af
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616448"
---
# <a name="azure-iot-edge-security-module"></a>Módulo de segurança do Azure IoT Edge

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para produção worklo§1ads. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[O Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) fornece recursos poderosos para gerenciar e executar fluxos de trabalho de negócios na borda.
A parte de chave do IoT Edge desempenha em ambientes de IoT torná-lo particularmente atraente de atores mal-intencionados.

Azure segurança ASC (Central) para o módulo de segurança de IoT fornece uma solução abrangente de segurança para o IoT Edge dispositivos.
ASC para o módulo do IoT coleta, agrega e analisa os dados de segurança brutas do seu sistema operacional e do sistema do contêiner em alertas e recomendações de práticas de segurança.

Semelhante ao ASC para agentes de segurança de IoT para dispositivos de IoT, o ASC para o módulo do IoT Edge é altamente personalizável por meio de seu módulo gêmeo.
Ver [configurar o agente](how-to-agent-configuration.md) para saber mais.

ASC para o módulo de segurança de IoT para o IoT Edge oferece os seguintes recursos:

- Coleta eventos de segurança brutas do sistema operacional subjacente (Linux) e os sistemas de contêiner do IoT Edge.
  
  Ver [ASC para configuração do agente IoT](how-to-agent-configuration.md) para saber mais sobre os coletores de dados de segurança disponíveis.

- Análise de manifestos de implantação do IoT Edge.

- Agrega eventos de segurança brutos em mensagens enviadas por meio [Hub do IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Remova configuração por meio do uso do gêmeo do módulo de segurança.

  Ver [configurar um ASC para agente do IoT](how-to-agent-configuration.md) para saber mais.

ASC para o módulo de segurança de IoT para o IoT Edge é executado em modo privilegiado no IoT Edge.
Modo privilegiado é necessária para permitir que o módulo para monitorar o sistema operacional e outros módulos do IoT Edge.

## <a name="agent-supported-platforms"></a>Plataformas com suporte do agente

ASC para o módulo de segurança de IoT para o IoT Edge só está disponível para Linux no momento.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura e os recursos do ASC para módulo de segurança de IoT para o IoT Edge.

Para continuar a introdução ao ASC para implantação de IoT, use os seguintes artigos:

- Implantar [módulo de segurança do IoT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu módulo de segurança](how-to-agent-configuration.md)
- Examine o ASC para IoT [pré-requisitos de serviço](service-prerequisites.md)
- Saiba como [ASC habilitar para o serviço de IoT em seu IoT Hub](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [ASC para perguntas frequentes sobre o IoT](resources-frequently-asked-questions.md)