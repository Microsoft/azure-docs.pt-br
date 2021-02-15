---
title: Suporte e desativação de recursos
titleSuffix: Azure Defender for IoT
description: O defender para IoT continuará a dar suporte a C, C# e Edge até 1º de março de 2022.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809697"
---
# <a name="feature-support-and-retirement"></a>Suporte e desativação de recursos

Este artigo descreve os recursos do Azure defender para IoT e o suporte para diferentes recursos no defender para IoT.

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>Defender para IoT C, C# e borda do módulo de segurança do Edge

O novo micro Agent substituirá o módulo de segurança atual do C, do C# e do Edge.  

O novo micro Agent baseia-se no conhecimento e na experiência coletada dos comentários de desenvolvimento, clientes e parceiros do módulo de segurança com quatro aprimoramentos importantes: 

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