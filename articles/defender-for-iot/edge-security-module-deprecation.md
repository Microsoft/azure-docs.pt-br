---
title: Suporte e desativação de recursos
description: O defender para IoT continuará a dar suporte a C, C# e Edge até 1º de março de 2022.
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 782e2e8ab0c54e21da643ca73f647a7ea21e4223
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784519"
---
# <a name="feature-support-and-retirement"></a>Suporte e desativação de recursos

Este artigo descreve os recursos do Azure defender para IoT e o suporte para diferentes recursos no defender para IoT.

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Defender para IoT C, C# e Edge defender-reprovando o micro-agente

O novo micro Agent substituirá o atual C, C# e Edge defender-IoT-micro-Agent.  

O novo micro Agent baseia-se no conhecimento e na experiência obtida com os comentários de desenvolvimento do defender-IoT-micro-Agent, clientes e parceiros de saída com quatro aprimoramentos importantes: 

- **Valor de segurança de profundidade**: o novo agente será executado no nível do host, o que fornecerá mais visibilidade para as operações subjacentes do dispositivo e para permitir uma melhor cobertura de segurança.

- **Melhoria do desempenho do dispositivo e redução do espaço**: obtido por uma RAM pequena e pelo volume de memória da ROM, bem como baixo consumo de CPU.  

- **Plug and Play**: o novo micro Agent não tem mais dependências de nível de kernel, e todas as suas dependências de software são fornecidas como parte de seu pacote. O micro Agent dá suporte à arquitetura de CPU comum.

- **Fácil de implantar**: o micro Agent dá suporte a modelos de distribuição diferentes, por meio do código-fonte e como um pacote binário. 

### <a name="timeline"></a>Linha do tempo 

O defender para IoT continuará a dar suporte a C, C# e Edge até 1º de março de 2022. 

## <a name="micro-agent-preview-support"></a>Suporte à versão prévia do micro Agent

Durante a visualização, o micro Agent pode enfrentar alterações significativas sem aviso prévio.

## <a name="next-steps"></a>Próximas etapas

Confira as [APIs do console de gerenciamento e do sensor do defender para IOT](references-work-with-defender-for-iot-apis.md).