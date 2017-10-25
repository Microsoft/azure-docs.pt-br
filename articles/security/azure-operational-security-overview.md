---
title: "Visão geral de segurança operacional do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral da segurança operacional do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: f2153e783adb955cf9055b09ba9aa2592f51e4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-overview"></a>Visão geral de segurança operacional do Azure
A Segurança Operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros recursos no Microsoft Azure. A [Segurança Operacional do Azure](https://docs.microsoft.com/azure/security/azure-operational-security) é uma estrutura que incorpora o conhecimento adquirido por meio de várias funcionalidades que são exclusivas à Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma conscientização profunda do cenário de ameaças à segurança cibernética.

Este artigo de Visão geral da segurança operacional do Azure tem como foco as seguintes áreas:

- Azure Operations Management Suite
-   Central de Segurança do Azure
-   Azure Monitor
-   Observador de Rede do Azure
-   Análise do Armazenamento do Azure
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
As Operações de TI são responsáveis por gerenciar a infraestrutura de datacenter, os aplicativos e os dados, incluindo a estabilidade e a segurança desses sistemas. No entanto, a obtenção de informações de segurança em ambientes de TI cada vez mais complexos geralmente exige que as empresas reúnam dados de vários sistemas de gerenciamento e segurança.

O [OMS (Microsoft Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem.

O OMS é uma solução de gerenciamento de TI baseada em nuvem com muitas ofertas, como a Automação de TI, Segurança e Conformidade, o Log Analytics e Backup e Recuperação. Como tal, é o auxílio perfeito para gerenciar e proteger sua infraestrutura de TI, localmente e na nuvem.

A principal funcionalidade do OMS é oferecida por um conjunto de serviços que são executados no Azure. Cada serviço fornece uma função de gerenciamento específico, e você pode combinar serviços para obter cenários de gerenciamento diferentes. Que inclui:

-   Log Analytics
-   Automação
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitoramento para o OMS coletando dados de recursos gerenciados em um repositório central. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação. Esse método permite consolidar dados de várias fontes para que você pode combinar dados de serviços do Azure com o ambiente local. Ele também separa a coleta dos dados da ação executada neles para que todas as ações fiquem disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automação
A [Automação do Microsoft Azure](https://docs.microsoft.com/azure/automation/automation-intro) fornece uma maneira para os usuários automatizarem tarefas manuais, longas, sujeitas a erros e repetidas com frequência que normalmente são executadas em um ambiente de nuvem e corporativo. Ele economiza tempo e aumenta a confiabilidade das tarefas administrativas regulares e até mesmo agenda a sua execução automática em intervalos regulares. Você pode automatizar processos usando runbooks ou automatizar o gerenciamento de configuração usando Configuração de Estado Desejado.

### <a name="backup"></a>Backup
O [Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é o serviço baseado no Azure que você pode usar para fazer backup (ou proteger) e restaurar os dados na nuvem da Microsoft. Ele substitui a solução de backup local ou externa existente por uma solução confiável, segura e econômica baseada em nuvem. O Backup do Azure oferece vários componentes que você pode baixar e implantar em um computador, servidor, ou na nuvem. O componente ou o agente que você implanta depende daquilo que deseja proteger. Todos os componentes do Backup do Azure (independentemente de você estar protegendo dados localmente ou na nuvem) podem ser usados para fazer backup de dados em um cofre dos Serviços de Recuperação no Azure. Confira a [tabela de componentes de Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Recuperação de site
[O Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) fornece continuidade de negócios pela orquestração de replicação de máquinas virtuais e físicas locais no Azure ou em um site secundário. Se seu site primário não estiver disponível, faça failover para o local secundário para que os usuários possam continuar trabalhando e faça failback quando os sistemas voltarem a funcionar corretamente. detecção de ameaças inteligente e eficiente.

## <a name="azure-active-directory"></a>Azure Active Directory
O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) é a solução IDaaS (Identidade como Serviço) abrangente da Microsoft que:

-   Habilita o IAM como um serviço de nuvem
-   Fornece gerenciamento de acesso central, logon único (SSO) e relatórios
-   Oferece suporte ao gerenciamento de acesso integrado para [milhares de aplicativos](https://azure.microsoft.com/marketplace/active-directory/) na galeria de aplicativos, incluindo Salesforce, Google Apps, Box, Concur e outros.

O Azure AD também inclui um pacote completo de [funcionalidades de gerenciamento de identidade](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), incluindo [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [registro de dispositivos]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [gerenciamento de senhas de autoatendimento](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [gerenciamento de grupos de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [gerenciamento de contas com privilégios](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [controle de acesso baseado em função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is), [monitoramento de uso de aplicativos](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [auditoria avançada](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e [alertas e monitoramento de segurança](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Com o Azure Active Directory, os aplicativos que você publica para seus parceiros e clientes (comerciais ou consumidores) têm os mesmos recursos de gerenciamento de identidades e de acesso. Isso permite que você reduza significativamente os custos operacionais.

## <a name="azure-security-center"></a>Central de Segurança do Azure
A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started) ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A [Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) ajuda a proteger os dados de máquinas virtuais no Azure, fornecendo visibilidade para configurações de segurança da máquina virtual e monitoramento de ameaças. A Central de Segurança pode monitorar as máquinas virtuais para:

-   Configurações de segurança do SO (Sistema Operacional) com as regras de configuração recomendadas
-   Segurança do sistema e atualizações críticas que estão ausentes
-   Recomendações de proteção de ponto de extremidade
-   Validação de criptografia de disco
-   Ataques de rede

A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure), que fornece [funções internas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

>[!Note]
>Confira [Permissões na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

A Central de Segurança do Azure usa o Microsoft Monitoring Agent; ele é o mesmo agente usado pelos serviços Operations Management Suite e Log Analytics. Os dados coletados desse agente são armazenados no [espaço de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) do Log Analytics existente associado à sua assinatura do Azure ou a novos espaços de trabalho, levando em conta a localização geográfica da VM.

## <a name="azure-monitor"></a>Azure Monitor
Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você precisa tomar conhecimento dessas questões imediatamente e precisa ter ferramentas para diagnosticar e corrigir problemas.

O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) é uma ferramenta básica para monitorar serviços em execução no Azure. Ele fornece dados de nível da infraestrutura sobre a taxa de transferência de um serviço e o ambiente em redor. Se você estiver gerenciando todos os seus aplicativos no Azure ou decidindo se deseja expandir ou reduzir recursos, o Azure Monitor oferece o que você usa para iniciar.

Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual. Ele inclui:

-   Log de Atividades do Azure
-   Logs de Diagnóstico do Azure
-   Métricas
-   Diagnóstico do Azure

### <a name="azure-activity-log"></a>Log de Atividades do Azure
É um log que fornece informações sobre as operações que foram executadas em recursos de sua assinatura. O [Log de Atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) era conhecido como "Logs de Auditoria" ou "Logs Operacionais", pois ele relata eventos de plano de controle de suas assinaturas.

### <a name="azure-diagnostic-logs"></a>Logs de Diagnóstico do Azure
Os [Logs de Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso.

Por exemplo, os logs de eventos do sistema Windows são uma categoria de Log de Diagnóstico para VMs, e logs de blobs, tabelas e filas são categorias de Logs de Diagnóstico para contas de armazenamento.

Os logs de diagnóstico diferem do [Log de Atividades (anteriormente conhecido como Log de Auditoria ou Log Operacional)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou por conta própria.

### <a name="metrics"></a>Métricas
O Azure Monitor permite consumir a telemetria para ter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. Os tipos de dados de telemetria do Azure mais importantes são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para monitoramento e solução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure
É a capacidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as [Funções de Trabalho ou Web do Serviço de Nuvem do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), as [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) que executam o Microsoft Windows e o [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Observador de Rede
Os clientes criam uma rede de ponta a ponta no Azure administrando e compondo vários recursos de rede individuais, como VNet, ExpressRoute, Gateway de Aplicativo, Balanceadores de carga e mais. O monitoramento está disponível em cada um dos recursos da rede.

A rede de ponta a ponta pode ter configurações complexas e interações entre os recursos, criando cenários complexos que precisam de monitoramento baseado em cenários por meio do Observador de Rede.

O [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) simplifica a monitoramento e o diagnóstico da rede do Azure. As ferramentas de diagnóstico e de visualização disponíveis com o Observador de Rede permitem que você use capturas de pacote remotas de uma máquina virtual do Azure, obtenha informações sobre seu tráfego de rede usando logs de fluxo e diagnostique conexões e gateway de VPN.

O Observador de Rede tem atualmente os seguintes recursos:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) – fornece uma exibição no nível da rede que mostra as diversas interconexões e associações entre os recursos de rede em um grupo de recursos.
-   [Captura de Pacote Variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) – captura dados do pacote dentro e fora de uma máquina virtual. As opções avançadas de filtragem e os controles ajustados, como ser capaz de definir o tempo e as limitações de tamanho, fornecem versatilidade. Os dados do pacote podem ser armazenados em um armazenamento de blobs ou no disco local no formato .cap.
-   [Verificação do fluxos de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) - verifica se um pacote é permitido ou negado com base nos parâmetros do pacote com cinco tuplas das informações do fluxo (IP de Destino, IP de Origem, Porta de Destino, Porta de Origem e Protocolo). Se o pacote for negado por um grupo de segurança, a regra e o grupo que negaram o pacote serão retornados.
-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) – determina o próximo salto para os pacotes encaminhados no Azure Network Fabric, permitindo diagnosticar as rotas definidas pelo usuário configuradas incorretamente.
-   [Exibição do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) – obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM.
-   [Log de fluxo do NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) – os logs de fluxo dos Grupos de Segurança de Rede permitem capturar logs relacionados ao tráfego que são permitidos ou negados pelas regras de segurança no grupo. O fluxo é definido por informações com cinco tuplas – IP de Origem, IP de Destino, Porta de Origem, Porta de Destino e Protocolo.
-   [Solução de problemas de Gateway de Rede Virtual e Conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) – fornece a capacidade de resolver problemas de Gateways de Rede Virtual e Conexões.
-   [Limites de assinatura da rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – permite exibir o uso de recursos de rede em relação aos limites.
-   [Configurar o Log de Diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – fornece um painel único para habilitar ou desabilitar os logs de Diagnóstico para os recursos de rede em um grupo de recursos.

Para saber mais sobre como configurar o observador de rede, confira [Configurar o observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>DevOps (Operações de Desenvolvedor)
Antes do desenvolvimento de aplicativos de DevOps, as equipes eram responsáveis por reunir os requisitos comerciais para um programa de software e escrever código. Em seguida, outra equipe de controle de qualidade testava o programa em um ambiente de desenvolvimento isolado e, se os requisitos fosse atendidos, liberavam o código para implantação pela equipe de operações. As equipes de implantação são fragmentadas também em grupos em silos, como banco de dados e rede. Cada vez que um programa de software é “passado” para uma equipe independente, afunilamentos são adicionados.

O [DevOps](https://www.visualstudio.com/learn/what-is-devops/) permite que as equipes forneçam soluções mais seguras, de alta qualidade, mais rápidas e mais baratas. Os clientes esperam uma experiência dinâmica e confiável ao consumir serviços e software.  As equipes devem iterar as atualizações de software rapidamente, medir o impacto das atualizações e responder rapidamente com novas iterações de desenvolvimento para resolver problemas ou agregar mais valor.  AS plataformas de nuvem, como o Microsoft Azure, removem afunilamentos tradicionais e ajudam a massificar a infraestrutura. Softwares estão presentes em todas as empresas como o principal diferencial nos resultados dos negócios. Nenhuma organização, desenvolvedor ou trabalhador de TI pode ou deve evitar o movimento do DevOps.

Os profissionais de DevOps experientes adotam várias das práticas a seguir. Essas práticas [envolvem pessoas](https://www.visualstudio.com/learn/what-is-devops-culture/) para formar estratégias com base em cenários de negócios.  As ferramentas podem ajudar a automatizar várias práticas:

-   As técnicas de [Gerenciamento de projeto e planejamento Agile](https://www.visualstudio.com/learn/what-is-agile/) são usadas para planejar e isolar o trabalho em sprints, gerenciar a capacidade de equipe e ajuda as equipes a se adaptarem rapidamente às necessidades dos negócios.
-   O [controle de versão, geralmente com Git](https://www.visualstudio.com/learn/what-is-git/), permite que equipes localizadas em qualquer lugar do mundo compartilhem fontes e integrem a ferramentas de desenvolvimento de software para automatizar o pipeline da versão.
-   A [Integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/) conduz a mesclagem contínua e teste do código, o que permite localizar defeitos antecipadamente.  Outros benefícios incluem menos tempo gasto no combate a problemas de mesclagem e o recebimento de comentários rapidamente as para equipes de desenvolvimento.
-   O [fornecimento contínuo](https://www.visualstudio.com/learn/what-is-continuous-delivery/) de soluções de software para ambientes de produção e teste ajudam a corrigir bugs e a atender rapidamente aos requisitos de negócios em constante mudança.
-   O [Monitoramento](https://www.visualstudio.com/learn/what-is-monitoring/) dos aplicativos em execução, incluindo ambientes de produção para a integridade do aplicativo, assim como o uso do cliente ajudam as organizações a formular uma hipótese e validar ou refutar rapidamente as estratégias.  Dados avançados são capturados e armazenados em vários formatos de log.
-   A [IaC (Infraestrutura como Código)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) é uma prática que permite a automação e validação da criação e desmontagem de redes e máquinas virtuais para ajudar a fornecer plataformas de hospedagem de aplicativos seguras e estáveis.
-   A arquitetura de[microsserviços](https://www.visualstudio.com/learn/what-are-microservices/) é aproveitada para separar casos de uso de negócios em serviços menores reutilizáveis.  Essa arquitetura oferece eficiência e escalabilidade.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a solução de Segurança e Auditoria do OMS, confira os seguintes artigos:

- [Operations Management Suite | Segurança e Conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Monitorando e respondendo a alertas de segurança na solução de Segurança e Auditoria do Operations Management Suite](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts).
- [Monitorando recursos na solução de Segurança e Auditoria do Operations Management Suite](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources).
