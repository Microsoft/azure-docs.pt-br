---
title: Visão geral do serviço
description: Saiba mais sobre os recursos e serviços do defender para IoT e entenda como o defender para IoT fornece segurança de IoT abrangente.
services: defender-for-iot
ms.service: azure
documentationcenter: na
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2020
ms.openlocfilehash: 5b4ab207462955be3876dc1a25fae491e48a9cd2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621703"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Bem-vindo ao Azure defender para IoT

As redes da tecnologia operacional (OT) conforçam muitos dos aspectos mais críticos de nossa sociedade. Mas muitas dessas tecnologias não foram projetadas tendo em mente a segurança e não podem ser protegidas com controles de segurança de ti tradicionais. Enquanto isso, o Internet das Coisas (IoT) está permitindo uma nova onda de inovação com bilhões de dispositivos conectados, aumentando a superfície de ataque e o risco.  

O Azure defender para IoT é uma solução de segurança unificada para identificar dispositivos, vulnerabilidades e ameaças de IoT/OT. Ele permite que você proteja todo o seu ambiente IoT/OT, independentemente de você precisar proteger os dispositivos IoT/OT existentes ou criar segurança em novas inovações de IoT.  

O Azure defender para IoT oferece dois conjuntos de recursos para atender às necessidades do seu ambiente.

Para organizações de usuários finais com ambientes IoT/OT, o Azure defender para IoT fornece monitoramento sem agente e camada de rede que:

- Pode ser implantado rapidamente.
- Integra-se facilmente com diferentes equipamentos industriais e ferramentas SOC.
- Tem impacto zero sobre o desempenho ou a estabilidade da rede IoT/OT. 

A plataforma pode ser implantada totalmente localmente ou em ambientes híbridos e conectados ao Azure.  

Para criadores de dispositivos IoT, o Azure defender para IoT também oferece um micro Agent leve que dá suporte a sistemas operacionais IoT padrão, como Linux e RTOS. Esse agente leve ajuda a garantir que a segurança seja incorporada às suas iniciativas de IoT/OT da borda para a nuvem. Ele inclui o código-fonte para implantação flexível e personalizável. 

## <a name="agentless-solution-for-organizations"></a>Solução sem agente para organizações 

Dispositivos IoT e OT mais antigos não dão suporte a agentes e geralmente são sem patches, configurados incorretamente e invisíveis para as equipes de ti. Essas qualidades fazem com que as metas suaves para os atores de ameaças que desejam se dinamizar mais profundamente em redes corporativas. 

As ferramentas tradicionais de monitoramento de segurança de rede desenvolvidas para redes de ti corporativas não podem resolver esses ambientes porque eles não têm uma compreensão profunda dos comportamentos especializados, dispositivos e M2M (máquina a máquina) encontrados nos ambientes IoT e OT. 

Os recursos de monitoramento sem agente no Azure defender para IoT oferecem visibilidade e segurança para essas redes. Em seguida, você pode abordar as principais preocupações desses ambientes. 

### <a name="automatic-device-discovery"></a>Descoberta automática de dispositivo  

Use o monitoramento de rede sem agente e passivo para obter um inventário completo de todos os seus dispositivos IoT/se, seus detalhes e como eles se comunicam, com impacto zero na rede IoT/OT.  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>Visibilidade proativa de riscos e vulnerabilidades
 
Identifique riscos e vulnerabilidades em seu ambiente IoT/OT. Por exemplo, identifique dispositivos sem patch, abra portas, aplicativos não autorizados e conexões não autorizadas. Você também pode identificar alterações nas configurações do dispositivo, no código PLC e no firmware. 

### <a name="iotot-threat-detection"></a>Detecção de ameaças de IoT/OT  

Detecte atividades anômalas ou não autorizadas com inteligência de ameaças e análise comportamental especializada em IoT/OT. Você pode até mesmo detectar ameaças avançadas perdidas por IOCs estáticos, como malware de dia zero, malware sem arquivo e táticas de vida. 

### <a name="unified-security-management-across-iotot"></a>Gerenciamento de segurança unificado no IoT/OT

Integre-se ao Azure Sentinel para obter uma visão geral de toda a sua organização. Implemente o controle de segurança de IoT/ficar unificado com integração em seus fluxos de trabalho existentes, incluindo ferramentas de terceiros como Splunk, IBM QRadar e ServiceNow. 

## <a name="agent-based-solution-for-device-builders"></a>Solução baseada em agente para construtores de dispositivos 

A segurança é uma preocupação quase universal para implementadores de IoT. Os dispositivos IoT têm necessidades exclusivas de monitoramento de ponto de extremidade, gerenciamento de postura de segurança e detecção de ameaças – tudo com requisitos de desempenho altamente específicos. 

Os agentes de segurança do Azure defender para IoT permitem que você crie segurança diretamente em seus novos dispositivos IoT e projetos de IoT do Azure. O micro Agent tem opções de implantação flexíveis, incluindo a capacidade de implantar como um pacote binário ou modificar o código-fonte. E o micro Agent está disponível para sistemas operacionais de IoT padrão, como o Linux e os RTOS do Azure.  

O Azure defender para IoT micro Agent fornece visibilidade de ponto de extremidade sobre gerenciamento de postura de segurança, detecção de ameaças e integração nas outras ferramentas de segurança da Microsoft para o gerenciamento de segurança unificado. 

### <a name="security-posture-management"></a>Gerenciamento de postura de segurança

Monitore proativamente a postura de segurança de seus dispositivos IoT. O Azure defender para IoT fornece recomendações de postura de segurança com base no parâmetro de comparação de CIS, juntamente com recomendações específicas do dispositivo. Obtenha visibilidade da segurança do sistema operacional, incluindo configuração do so, configuração de firewall e permissões. 

### <a name="endpoint-iotot-threat-detection"></a>Detecção de ameaças do ponto de extremidade IoT/OT

Detecte ameaças como botnets, tentativas de força bruta, criptografia mineradores e atividade de rede suspeita. Crie alertas personalizados para direcionar as ameaças mais importantes em sua organização exclusiva. 

### <a name="flexible-distribution-and-deployment-models"></a>Modelos de distribuição e implantação flexíveis 

O Azure defender para IoT micro Agent inclui o código-fonte, para que você possa incorporar o micro Agent ao firmware ou personalizá-lo para incluir apenas o que você precisa. Ele também está disponível como um pacote binário ou integrado diretamente a outras soluções de IoT do Azure. 

## <a name="see-also"></a>Veja também

[Arquitetura do Azure defender para IoT](architecture.md)