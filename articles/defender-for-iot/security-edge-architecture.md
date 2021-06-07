---
title: Defender para IoT defender-IoT-micro-Agent para IoT Edge
description: Entenda a arquitetura e os recursos do Azure defender para IoT defender-IoT-micro-Agent para IoT Edge.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 81eb8816e1bcf74a9e27e34d14465102599c7d5d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782649"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure defender para IoT Edge defender-IoT-micro-Agent

[Azure IOT Edge](../iot-edge/index.yml) fornece recursos poderosos para gerenciar e executar fluxos de trabalho de negócios na borda.
A parte importante que IoT Edge desempenha em ambientes de IoT o torna particularmente atraente para atores mal-intencionados.

O defender para IoT defender-IoT-micro-Agent fornece uma solução de segurança abrangente para seus dispositivos IoT Edge.
O módulo defender para IoT coleta, agrega e analisa dados de segurança brutos do sistema operacional e do sistema de contêiner em alertas e recomendações de segurança acionáveis.

Semelhante ao defender para agentes de segurança de IoT para dispositivos IoT, o módulo defender for IoT Edge é altamente personalizável por meio de seu módulo.
Consulte [configurar seu agente](how-to-agent-configuration.md) para saber mais.

O defender para IoT defender-IoT-micro-Agent para IoT Edge oferece os seguintes recursos:

- Coleta eventos de segurança brutos do sistema operacional subjacente (Linux) e os sistemas de contêineres IoT Edge.

  Confira [configuração do agente do defender para IOT](how-to-agent-configuration.md) para saber mais sobre os coletores de dados de segurança disponíveis.

- Análise de manifestos de implantação do IoT Edge.

- Agrega eventos de segurança brutos em mensagens enviadas por meio do [Hub IOT Edge](../iot-edge/iot-edge-runtime.md#iot-edge-hub).

- Remova a configuração por meio do uso do defender-IoT-micro-Agent.

  Consulte [configurar um agente do defender para IOT](how-to-agent-configuration.md) para saber mais.

O defender para IoT defender-IoT-micro-Agent para IoT Edge é executado em um modo privilegiado em IoT Edge.
O modo privilegiado é necessário para permitir que o módulo monitore o sistema operacional e outros módulos IoT Edge.

## <a name="module-supported-platforms"></a>Plataformas com suporte do módulo

O defender para IoT defender-IoT-micro-Agent para IoT Edge está disponível no momento apenas para Linux.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre a arquitetura e os recursos do defender para IoT defender-IoT-micro-Agent para IoT Edge.

Para continuar com a introdução à implantação do Defender para IoT, use os seguintes artigos:

- Implantar o [defender-IOT-micro-Agent para IOT Edge](how-to-deploy-edge.md)
- Saiba como [configurar seu defender-IOT-micro-Agent](how-to-agent-configuration.md)
- Examinar o defender para IoT [defender para](resources-manage-proprietary-protocols.md) IOT
- Saiba como [habilitar o defender para o serviço de IOT em seu hub IOT](quickstart-onboard-iot-hub.md)
- Saiba mais sobre o serviço do [defender for IOT FAQ](resources-frequently-asked-questions.md)