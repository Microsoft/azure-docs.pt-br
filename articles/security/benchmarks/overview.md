---
title: Visão geral do benchmark de segurança do Azure v2
description: Visão geral do benchmark de segurança do Azure v2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369150"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Visão geral do benchmark de segurança do Azure (v2)

O ASB (benchmark de segurança do Azure) fornece recomendações e práticas recomendadas prescritivas para ajudar a melhorar a segurança de cargas de trabalho, dados e serviços no Azure.

Esse parâmetro de comparação faz parte de um conjunto de diretrizes de segurança holísticas que também incluem:

- **Estrutura de adoção de nuvem** – orientação sobre segurança, incluindo [estratégia](/azure/cloud-adoption-framework/strategy/define-security-strategy), [funções e responsabilidades](/azure/cloud-adoption-framework/organize/cloud-security), [as 10 melhores práticas de segurança](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)e implementação de [referência](/azure/cloud-adoption-framework/ready/enterprise-scale/)do Azure.
- **Estrutura de Well-Architected do Azure** – diretrizes sobre como [proteger suas cargas de trabalho](/assessments/?mode=pre-assessment&session=local) no Azure.
- **Práticas recomendadas de segurança da Microsoft** – [recomendações](/security/compass/microsoft-security-compass-introduction) com exemplos no Azure.

 O benchmark de segurança do Azure concentra-se em áreas de controle centradas na nuvem. Esses controles são consistentes com benchmarks de segurança conhecidos, como os descritos pelos controles do CIS (Center for Internet Security) versão 7,1 e do NIST (National Institute of Standards and Technology) SP 800-53.
Os seguintes controles estão incluídos no benchmark de segurança do Azure:

| Domínios de controle ASB | Descrição 
|--|--|
| [&nbsp;Segurança &nbsp; de rede (ns)](security-controls-v2-network-security.md) | A segurança de rede abrange controles para proteger e proteger redes do Azure, incluindo a proteção de redes virtuais, o estabelecimento de conexões privadas, a prevenção e a mitigação de ataques externos e a proteção do DNS. |
| [&nbsp;Gerenciamento &nbsp; de identidade (IM)](security-controls-v2-identity-management.md) | O gerenciamento de identidades abrange controles para estabelecer uma identidade segura e controles de acesso usando Azure Active Directory, incluindo o uso de logon único, autenticações fortes, identidades gerenciadas (e princípios de serviço) para aplicativos, acesso condicional e monitoramento de anomalias de conta. |
| [Acesso privilegiado &nbsp; &nbsp; (PA)](security-controls-v2-privileged-access.md) | O acesso privilegiado abrange controles para proteger o acesso privilegiado ao seu locatário e recursos do Azure, incluindo uma variedade de controles para proteger seu modelo administrativo, contas administrativas e estações de trabalho de acesso privilegiado contra risco proposital e inadvertido. |
| [&nbsp;Proteção &nbsp; de dados (DP)](security-controls-v2-data-protection.md) | A proteção de dados aborda o controle da proteção de dados em repouso, em trânsito e por meio de mecanismos de acesso autorizado, incluindo descobrir, classificar, proteger e monitorar ativos de dados confidenciais usando o controle de acesso, a criptografia e o registro em log no Azure. |
| [&nbsp;Gerenciamento &nbsp; de ativos (AM)](security-controls-v2-asset-management.md) | O gerenciamento de ativos abrange controles para garantir a visibilidade e a governança da segurança sobre os recursos do Azure, incluindo recomendações sobre permissões para a equipe de segurança, acesso de segurança ao inventário de ativos e gerenciamento de aprovações para serviços e recursos (inventário, acompanhamento e correto). |
| [Registro &nbsp; em log e &nbsp; &nbsp; detecção de ameaças (lt)](security-controls-v2-logging-threat-detection.md) | O registro em log e a detecção de ameaças abrangem controles para detectar ameaças no Azure e habilitar, coletar e armazenar logs de auditoria para serviços do Azure, incluindo a habilitação de processos de detecção, investigação e correção com controles para gerar alertas de alta qualidade com detecção de ameaças nativas nos serviços do Azure; Ele também inclui a coleta de logs com Azure Monitor, centralização da análise de segurança com o Azure Sentinel, sincronização de tempo e retenção de log. |
| [Resposta a incidentes &nbsp; &nbsp; (ir)](security-controls-v2-incident-response.md) | A resposta a incidentes aborda controles no ciclo de vida de resposta a incidentes – preparação, detecção e análise, contenção e atividades pós-incidente, incluindo o uso de serviços do Azure, como a central de segurança do Azure e o Sentinel para automatizar o processo de resposta a incidentes. |
| [De &nbsp; postura &nbsp; e &nbsp; gerenciamento &nbsp; de vulnerabilidades (VP)](security-controls-v2-posture-vulnerability-management.md) | A postura e o gerenciamento de vulnerabilidades concentram-se em controles para avaliar e melhorar a postura de segurança do Azure, incluindo verificação de vulnerabilidade, teste de penetração e correção, bem como controle de configuração de segurança, geração de relatórios e correção nos recursos do Azure. |
| [&nbsp;Segurança &nbsp; (es) do ponto de extremidade](security-controls-v2-endpoint-security.md) | O Endpoint Security aborda controles em resposta e detecção de ponto de extremidade, incluindo o uso de EDR (detecção de ponto de extremidade e resposta) e serviço antimalware para pontos de extremidade em ambientes do Azure. |
| [Backup &nbsp; e &nbsp; recuperação &nbsp; (br)](security-controls-v2-backup-recovery.md) | O backup e a recuperação abrangem controles para garantir que os backups de dados e de configuração em diferentes camadas de serviço sejam executados, validados e protegidos. |
| [Governança &nbsp; e &nbsp; estratégia &nbsp; (GS)](security-controls-v2-governance-strategy.md) | Governança e estratégia fornece orientação para garantir uma estratégia de segurança coerente e uma abordagem de governança documentada para orientar e sustentar a garantia de segurança, incluindo o estabelecimento de funções e responsabilidades para as diferentes funções de segurança de nuvem, estratégia técnica unificada e políticas e padrões de suporte. |

## <a name="azure-security-benchmark-recommendations"></a>Recomendações de benchmark de segurança do Azure

Cada recomendação inclui as seguintes informações:

- **ID do Azure**: a ID de benchmark de segurança do Azure que corresponde à recomendação.
- Os **controles do CIS v 7.1 ID (s)**: o cis controla os controles do v 7.1 que correspondem a essa recomendação.
- **ID (s) do NIST sp 800-53 R4**: os controles nist SP 800-53 R4 (moderado) que correspondem a essa recomendação.
- **Detalhes**: a lógica para a recomendação e links para orientação sobre como implementá-la. Se a recomendação for suportada pela central de segurança do Azure, essas informações também serão listadas.
- **Responsabilidade**: se o cliente, o provedor de serviços ou ambos são responsáveis por implementar essa recomendação. As responsabilidades de segurança são compartilhadas na nuvem pública. Alguns controles de segurança só estão disponíveis para o provedor de serviços de nuvem e, portanto, o provedor é responsável por lidar com eles. Essas são observações gerais – para alguns serviços individuais, a responsabilidade será diferente do que está listado no benchmark de segurança do Azure. Essas diferenças são descritas nas recomendações de linha de base para o serviço individual.
- **Participantes de segurança do cliente**: [as funções de segurança](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) na organização cliente que podem ser responsáveis, responsáveis ou consultados pelo respectivo controle. Ele pode ser diferente da organização para a organização, dependendo da estrutura da organização de segurança de sua empresa, e das funções e responsabilidades que você configurou relacionadas à segurança do Azure.

> [!NOTE]
> Os mapeamentos de controle entre o ASB e os benchmarks do setor (como NIST e CIS) indicam apenas que um recurso específico do Azure pode ser usado para resolver de forma completa ou parcial um requisito de controle definido no NIST ou no CIS. Você deve estar ciente de que essa implementação não é necessariamente traduzida para a conformidade total do controle correspondente no CIS ou NIST.

Agradecemos seus comentários detalhados e sua participação ativa no esforço de benchmark de segurança do Azure. Se você quiser fornecer a entrada direta da equipe de benchmark de segurança do Azure, preencha o formulário em https://aka.ms/AzSecBenchmark

## <a name="download"></a>Baixar

Você pode baixar o benchmark de segurança do Azure no [formato de planilha](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="next-steps"></a>Próximas etapas 
- Consulte o primeiro controle de segurança: [segurança de rede](security-control-network-security.md)
- Leia a [introdução ao benchmark de segurança do Azure](introduction.md)
- Conheça os [conceitos básicos de segurança do Azure](../fundamentals/index.yml)