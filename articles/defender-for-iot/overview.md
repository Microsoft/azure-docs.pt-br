---
title: Visão geral do serviço
description: Saiba mais sobre os recursos e os serviços do Defender para IoT e entenda como ele fornece segurança da IoT abrangente.
ms.topic: overview
ms.date: 12/09/2020
ms.openlocfilehash: 15772ee21587ed2bc010e31174af6daac71dfc12
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786831"
---
# <a name="welcome-to-azure-defender-for-iot"></a>Bem-vindo(a) ao Azure Defender para IoT

As redes OT (tecnologia operacional) ativam muitos dos aspectos mais críticos da nossa sociedade. Porém, muitas dessas tecnologias não foram projetadas tendo em mente a segurança e não podem ser protegidas com os controles de segurança de TI tradicionais. Enquanto isso, a IoT (Internet das Coisas) está possibilitando um novo ciclo de inovação com bilhões de dispositivos conectados, aumentando a superfície de ataque e os riscos.  

O Azure Defender para IoT é uma solução de segurança unificada usada para identificar dispositivos IoT/OT, vulnerabilidades e ameaças. Ele permite que você proteja todo o seu ambiente de IoT/OT, independentemente de você precisar proteger os dispositivos IoT/OT existentes ou incorporar a segurança em inovações de IoT.  

O Azure Defender para IoT oferece dois conjuntos de funcionalidades para atender às necessidades do seu ambiente.

Para organizações de usuários finais com ambientes IoT/OT, o Azure Defender para IoT fornece monitoramento sem agente e de camada de rede que:

- Pode ser implantado rapidamente.
- Integra-se com facilidade a diferentes equipamentos industriais e ferramentas de SOC.
- Tem impacto zero sobre o desempenho ou a estabilidade da rede IoT/OT. 

A plataforma pode ser implantada totalmente no local ou em ambientes híbridos e conectados ao Azure.  

Para criadores de dispositivos IoT, o Azure Defender para IoT também oferece um microagente leve que dá suporte a sistemas operacionais de IoT padrão, como o Linux e o RTOS. Esse agente leve ajuda a garantir que a segurança seja incorporada às suas iniciativas de IoT/OT da borda para a nuvem. Ele inclui o código-fonte para implantação flexível e personalizável. 

## <a name="agentless-solution-for-organizations"></a>Solução sem agente para organizações 

Os dispositivos IoT e OT mais antigos não dão suporte a agentes e costumam não ter patches, ser configurados incorretamente e estar invisíveis para as equipes de TI. Essas qualidades fazem deles um alvo fácil para os atores de ameaças que desejam entrar mais profundamente nas redes corporativas. 

As ferramentas tradicionais de monitoramento de segurança de rede desenvolvidas para redes de TI corporativas não podem lidar com esses ambientes, porque não têm uma compreensão profunda dos protocolos especializados, dos dispositivos e dos comportamentos M2M (entre computadores) encontrados nos ambientes de IoT e OT. 

As funcionalidades de monitoramento sem agente do Azure Defender para IoT oferecem visibilidade e segurança para essas redes. Em seguida, você pode abordar as principais preocupações desses ambientes. 

### <a name="automatic-device-discovery"></a>Descoberta automática de dispositivo  

Use o monitoramento de rede passivo e sem agente para obter um inventário completo de todos os seus dispositivos IoT/OT, os detalhes e como eles se comunicam, com impacto zero na rede IoT/OT.  

### <a name="proactive-visibility-into-risk-and-vulnerabilities"></a>Visibilidade proativa de riscos e vulnerabilidades
 
Identifique riscos e vulnerabilidades no seu ambiente de IoT/OT. Por exemplo, identifique dispositivos sem patch, portas abertas, aplicativos não autorizados e conexões não autorizadas. Identifique também alterações nas configurações do dispositivo, no código PLC e no firmware. 

### <a name="iotot-threat-detection"></a>Detecção de ameaças de IoT/OT  

Detecte atividades anormais ou não autorizadas com a inteligência contra ameaças e a análise comportamental com reconhecimento de IoT/OT. Você pode, até mesmo, detectar ameaças avançadas não detectadas por IOCs estáticos, como malware de dia zero, malware sem arquivos e táticas living-off-the-land. 

### <a name="unified-security-management-across-iotot"></a>Gerenciamento de segurança unificado em IoT/OT

Integre-se ao Azure Sentinel para obter uma visão geral de toda a sua organização. Implemente a governança de segurança da IoT/OT unificada com integração aos fluxos de trabalho existentes, incluindo ferramentas de terceiros como o Splunk, o IBM QRadar e o ServiceNow. 

## <a name="agent-based-solution-for-device-builders"></a>Solução baseada em agente para construtores de dispositivos 

A segurança é uma preocupação quase universal para os implementadores de IoT. Os dispositivos IoT têm necessidades exclusivas de monitoramento de ponto de extremidade, gerenciamento da postura de segurança e detecção de ameaças, tudo isso com requisitos de desempenho altamente específicos. 

Os agentes de segurança do Azure Defender para IoT permitem que você incorpore a segurança diretamente nos seus novos dispositivos IoT e projetos de IoT do Azure. O microagente tem opções de implantação flexíveis, incluindo a capacidade de implantação como um pacote binário ou modificação do código-fonte. Além disso, o microagente está disponível para sistemas operacionais de IoT padrão, como o Linux e o Azure RTOS.  

O microagente do Azure Defender para IoT fornece visibilidade de ponto de extremidade do gerenciamento da postura de segurança, da detecção de ameaças e da integração a outras ferramentas de segurança da Microsoft para o gerenciamento de segurança unificado. 

### <a name="security-posture-management"></a>Gerenciamento da postura de segurança

Monitore de maneira proativa a postura de segurança dos seus dispositivos IoT. O Azure Defender para IoT fornece recomendações de postura de segurança com base no parâmetro de comparação do CIS, acompanhadas de recomendações específicas do dispositivo. Obtenha visibilidade da segurança do sistema operacional, incluindo configuração do sistema operacional, configuração de firewall e permissões. 

### <a name="endpoint-iotot-threat-detection"></a>Detecção de ameaças de IoT/OT do ponto de extremidade

Detecte ameaças como botnets, tentativas de ataques de força bruta, criptomineradores e atividades de rede suspeitas. Crie alertas personalizados direcionados às ameaças mais importantes na sua organização exclusiva. 

### <a name="flexible-distribution-and-deployment-models"></a>Modelos de distribuição e implantação flexíveis 

O microagente do Azure Defender para IoT inclui o código-fonte, de modo que você possa incorporar o microagente no firmware ou personalizá-lo para incluir apenas o necessário. Ele também está disponível como um pacote binário ou é integrado diretamente a outras soluções de IoT do Azure. 

## <a name="see-also"></a>Veja também

[Arquitetura do Azure Defender para IoT](architecture.md)