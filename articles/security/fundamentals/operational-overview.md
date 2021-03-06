---
title: Visão geral de segurança operacional do Azure | Microsoft Docs
description: Saiba mais sobre a segurança operacional do Azure nesta visão geral. A segurança operacional refere-se aos serviços, controles e recursos de proteção de ativos.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595502"
---
# <a name="azure-operational-security-overview"></a>Visão geral de segurança operacional do Azure

A [segurança operacional do Azure](./operational-security.md) refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure. É uma estrutura que incorpora o conhecimento adquirido por uma variedade de funcionalidades que são exclusivas à Microsoft. Essas funcionalidades incluem o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e um entendimento profundo do cenário de ameaças à segurança cibernética.

## <a name="azure-management-services"></a>Serviços de gerenciamento do Azure

Uma equipe de operações de TI é responsável por gerenciar a infraestrutura do datacenter, os aplicativos e os dados, incluindo a estabilidade e a segurança desses sistemas. No entanto, a obtenção de informações de segurança em ambientes de TI cada vez mais complexos geralmente exige que as empresas reúnam dados de vários sistemas de gerenciamento e segurança.

Os [logs do Microsoft Azure Monitor](../../azure-monitor/overview.md) são a solução de gerenciamento de TI baseada em nuvem da Microsoft que o ajudará a gerenciar e proteger sua infraestrutura local e de nuvem. Sua funcionalidade central é proporcionada pelos serviços executados no Azure descritos a seguir. O Azure inclui vários serviços que ajudam a gerenciar e proteger a infraestrutura de nuvem e local. Cada serviço fornece uma função de gerenciamento específica. Você pode combinar serviços para obter diferentes cenários de gerenciamento. 

### <a name="azure-monitor"></a>Azure Monitor

O [Azure Monitor](../../azure-monitor/overview.md) coleta dados de fontes gerenciadas em armazenamentos de dados centrais. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos por meio da API. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação.

Consolide os dados de uma variedade de fontes e combine os dados de seus serviços do Azure com o ambiente local existente. Os logs do Azure Monitor também separam claramente a coleta dos dados da ação executada neles, de modo que todas as ações fiquem disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automação

A [Automação do Azure](../../automation/automation-intro.md) fornece uma maneira de automatizar as tarefas manuais, de execução longa, propensas a erros e repetidas com frequência que normalmente são executadas em um ambiente empresarial e de nuvem. Ela poupa tempo e aumenta a confiabilidade de tarefas administrativas. Até mesmo agenda essas tarefas para que sejam executadas automaticamente em intervalos regulares. Você pode automatizar processos usando runbooks ou automatizar o gerenciamento de configuração usando o Desired State Configuration.

### <a name="backup"></a>Backup

O [Backup do Azure](../../backup/backup-overview.md) é o serviço baseado no Azure que você pode usar para fazer backup (ou proteger) e restaurar seus dados no Microsoft Cloud. O Backup do Azure substitui a solução de backup local ou externa existente por uma solução confiável, segura e econômica baseada em nuvem.

O Backup do Azure oferece componentes que você pode baixar e implantar no computador ou servidor apropriado, ou na nuvem. O componente ou o agente que você implanta depende daquilo que deseja proteger. Todos os componentes do Backup do Azure (independentemente de você estar protegendo os dados localmente ou na nuvem) podem ser usados para fazer backup de dados em um cofre dos Serviços de Recuperação do Azure no Azure.

Para obter mais informações, veja a [tabela de componentes do Backup do Azure](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

O [Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) fornece continuidade dos negócios por meio da orquestração da replicação de máquinas virtuais e computadores físicos locais no Azure ou em um site secundário. Caso o site primário não esteja disponível, faça failover para o local secundário, de modo que os usuários possam continuar trabalhando. Faça failback quando os sistemas voltarem a funcionar corretamente. Use a Central de Segurança do Azure para executar uma detecção de ameaças mais inteligente e eficaz.

## <a name="azure-active-directory"></a>Azure Active Directory

O [Azure AD (Azure Active Directory)](../../active-directory/manage-apps/what-is-application-management.md) é um serviço de identidade abrangente que:

-   Possibilita o IAM (gerenciamento de identidades e acesso) como um serviço de nuvem.
-   Fornece gerenciamento de acesso central, SSO (logon único) e relatórios.
-   Dá suporte ao gerenciamento de acesso integrado para [milhares de aplicativos](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) no Azure Marketplace, incluindo Salesforce, Google Apps, Box e Concur.

O Azure AD também inclui um pacote completo de [funcionalidades de gerenciamento de identidades](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports), incluindo as seguintes:

- [Autenticação multifator](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Gerenciamento de senhas de autoatendimento](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gerenciamento de grupo de autoatendimento](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Gerenciamento de contas com privilégios](../../active-directory/privileged-identity-management/pim-configure.md)
- [RBAC do Azure (controle de acesso baseado em função do Azure)](../../role-based-access-control/overview.md)
- [Monitoramento de uso do aplicativo](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Auditoria avançada](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Monitoramento e alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md)

Com o Azure Active Directory, os aplicativos que você publica para seus parceiros e clientes (comerciais ou consumidores) têm as mesmas funcionalidades de gerenciamento de identidades e de acesso. Isso permite que você reduza significativamente os custos operacionais.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](../../security-center/security-center-introduction.md) ajuda você a prevenir, detectar e responder a ameaças com maior visibilidade e controle da segurança de seus recursos do Azure. Fornece monitoramento de segurança integrada e gerenciamento de política em suas assinaturas. Ela ajuda a detectar ameaças que poderiam não ser notadas de outra forma e funciona com um amplo ecossistema de soluções de segurança.

[Proteja os dados de VM (máquina virtual)](../../security-center/security-center-introduction.md) no Azure proporcionando visibilidade das configurações de segurança da máquina virtual e monitorando as ameaças. A Central de Segurança pode monitorar as máquinas virtuais para:

- Configurações de segurança do sistema operacional com as regras de configuração recomendadas.
- Atualizações críticas e de segurança do sistema que estão ausentes.
- Recomendações de proteção de ponto de extremidade.
- Validação de criptografia de disco.
- Ataques baseados em rede.

A central de segurança usa o [Azure RBAC (controle de acesso baseado em função)](../../role-based-access-control/role-assignments-portal.md). O RBAC do Azure fornece [funções internas](../../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê informações relacionadas a um recurso apenas quando recebe a função de proprietário, colaborador ou leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

>[!Note]
>Confira [Permissões na Central de Segurança do Azure](../../security-center/security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.

A Central de Segurança usa o Microsoft Monitoring Agent. Esse é o mesmo agente usado pelo serviço de Azure Monitor. Os dados coletados desse agente são armazenados em um [workspace](../../azure-monitor/logs/manage-access.md) existente do Log Analytics associado à sua assinatura do Azure ou a um novo workspace, levando em conta a geolocalização da VM.

## <a name="azure-monitor"></a>Azure Monitor

Problemas de desempenho em seu aplicativo de nuvem podem afetar seus negócios. Com diversos componentes interconectados e frequentes lançamentos, degradações podem ocorrer a qualquer momento. E se você estiver desenvolvendo um aplicativo, seus usuários normalmente descobrirão problemas que você não encontrou durante os testes. Você deve tomar conhecimento dessas questões imediatamente e ter ferramentas para diagnosticar e corrigir os problemas.

O [Azure Monitor](../../azure-monitor/overview.md) é uma ferramenta básica para monitorar serviços em execução no Azure. Ele fornece dados de nível da infraestrutura sobre a taxa de transferência de um serviço e o ambiente em redor. Se você está gerenciando todos os seus aplicativos no Azure e está decidindo se deseja escalar ou reduzir verticalmente os recursos, o Azure Monitor é o lugar ideal para você começar.

Use também os dados de monitoramento para obter mais insights sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

O Azure Monitor inclui os componentes a seguir.

### <a name="azure-activity-log"></a>Log de Atividades do Azure

O [Log de Atividades do Azure](../../azure-monitor/essentials/platform-logs-overview.md) fornece insights sobre as operações executadas nos recursos de sua assinatura. Ele era conhecido como "Log de Auditoria" ou "Log Operacional", pois relata eventos de plano de controle de suas assinaturas.

### <a name="azure-diagnostic-logs"></a>Logs de diagnóstico do Azure

Os [logs de diagnóstico do Azure](../../azure-monitor/essentials/platform-logs-overview.md) são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia de acordo com o tipo de recurso.

Os logs do sistema de eventos do Windows são uma categoria de logs de diagnóstico para VMs. Os logs de blob, tabela e fila são categorias de logs de diagnóstico para contas de armazenamento.

Os logs de diagnóstico são diferentes do [Log de Atividades](../../azure-monitor/essentials/platform-logs-overview.md). O Log de Atividades fornece informações sobre as operações executadas em recursos em sua assinatura. Os logs de diagnóstico fornecem insights sobre as operações que o recurso executou por conta própria.

### <a name="metrics"></a>Métricas

O Azure Monitor fornece a telemetria que proporciona a você a visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. O tipo mais importante de dados telemétricos do Azure são as [métricas](../../azure-monitor/data-platform.md) (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitorar e solucionar problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

O Diagnóstico do Azure permite a coleta de dados de diagnóstico em um aplicativo implantado. Use a extensão de Diagnóstico em várias fontes. No momento, as fontes compatíveis são as [funções do serviço de nuvem do Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), as [máquinas virtuais do Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) que executam o Microsoft Windows e o [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Observador de Rede do Azure

Os clientes criam uma rede de ponta a ponta no Azure orquestrando e compondo recursos de rede individuais, como redes virtuais, o Azure ExpressRoute, o Gateway de Aplicativo do Azure e balanceadores de carga. O monitoramento está disponível em cada um dos recursos da rede.

A rede de ponta a ponta pode ter configurações complexas e interações entre recursos. O resultado são cenários complexos que precisam de um monitoramento baseado em cenários por meio do [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md).

O Observador de Rede simplifica o monitoramento e o diagnóstico da rede do Azure. Use as ferramentas de diagnóstico e visualização do Observador de Rede para:

- Fazer capturas de pacote remoto em uma máquina virtual do Azure.
- Obter insights sobre o tráfego de rede usando os logs de fluxo.
- Diagnosticar as conexões e o Gateway de VPN do Azure.

O Observador de Rede tem atualmente os seguintes recursos:

- [Topologia](../../network-watcher/view-network-topology.md): fornece uma exibição das várias interconexões e associações entre os recursos de rede em um grupo de recursos.
- [Captura de Pacote Variável](../../network-watcher/network-watcher-packet-capture-overview.md): Captura os dados do pacote dentro e fora de uma máquina virtual. As opções avançadas de filtragem e os controles ajustados, como o recurso de definir o tempo e as limitações de tamanho, fornecem versatilidade. Os dados do pacote podem ser armazenados em um armazenamento de blobs ou no disco local no formato .cap.
- [Verificação de fluxo de IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md): verifica se um pacote é permitido ou negado com base nos parâmetros do pacote de cinco tuplas das informações do fluxo (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se um grupo de segurança nega o pacote, a regra e o grupo que negaram o pacote são retornados.
- [Próximo salto](../../network-watcher/network-watcher-next-hop-overview.md): determina o próximo salto para os pacotes encaminhados na malha de rede do Azure, para que você possa diagnosticar as rotas definidas pelo usuário configuradas incorretamente.
- [Vista de grupo de segurança](../../network-watcher/network-watcher-security-group-view-overview.md): Obtém as regras de segurança efetivas e aplicadas que são usadas em uma VM.
- [Logs de fluxo de NSG para grupos de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): permitem capturar logs relacionados ao tráfego que é permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por informações de 5 tuplas: IP de origem, IP de destino, porta de origem, porta de destino e protocolo.
- [Solução de problemas de conexão e do gateway de rede virtual](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): fornece a capacidade de solucionar problemas em conexões e gateways de rede virtual.
- [Limites de assinatura da rede](../../network-watcher/network-watcher-monitoring-overview.md): Permite exibir o uso dos recursos de rede em relação aos limites.
- [Logs de diagnóstico](../../network-watcher/network-watcher-monitoring-overview.md): fornece um só painel para habilitar ou desabilitar os logs de diagnóstico para os recursos de rede em um grupo de recursos.

Para obter mais informações, confira [Configurar o Observador de Rede](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Transparência de acesso de provedor de serviço de nuvem

O [Sistema de Proteção de Dados do Cliente para Microsoft Azure](customer-lockbox-overview.md) é um serviço integrado ao portal do Azure que fornece controle explícito no caso raro em que um engenheiro do Suporte da Microsoft precisa de acesso aos seus dados para resolver um problema.
Isso ocorre em pouquíssimos casos, como quando há um problema de acesso remoto de depuração, quando um engenheiro do Suporte da Microsoft precisa de permissões elevadas para resolver esse problema. Nesses casos, os engenheiros da Microsoft usam o serviço de acesso Just-In-Time que fornece autorização limitada, com limite de tempo e acesso limitado ao serviço.  
Embora a Microsoft sempre tenha solicitado consentimento do cliente para ter acesso, agora o Sistema de Proteção de Dados do Cliente possibilita examinar e aprovar ou negar essas solicitações no Portal do Azure. Os engenheiros do Suporte da Microsoft não terão acesso até que você aprove a solicitação.

## <a name="standardized-and-compliant-deployments"></a>Implantações padronizadas e em conformidade

O [Azure Blueprints](../../governance/blueprints/overview.md) permite que arquitetos de nuvem e grupos centrais de tecnologia da informação definam um conjunto repetitivo de recursos do Azure que implementa e adere aos padrões, padrões e requisitos de uma organização.  
Isso possibilita que equipes do DevOps criem e configurem rapidamente novos ambientes e saibam que os estão criando com uma infraestrutura que mantém a conformidade organizacional.
O Blueprints é uma maneira declarativa de orquestrar a implantação de vários modelos de recursos e outros artefatos, como:

- Atribuições de Funções
- Atribuições de Políticas
- Modelos do Azure Resource Manager
- Grupos de recursos

## <a name="devops"></a>DevOps

Antes do desenvolvimento de aplicativo de [DevOps (Operações de Desenvolvedor)](https://azure.microsoft.com/overview/what-is-devops/), as equipes eram responsáveis por reunir os requisitos comerciais de um programa de software e escrever o código. Em seguida, uma equipe separada de garantia de qualidade testava o programa em um ambiente de desenvolvimento isolado. Se os requisitos fossem atendidos, a equipe de garantia de qualidade liberaria o código para ser implantado pelas operações. As equipes de implantação eram ainda fragmentadas em grupos como rede e banco de dados. Sempre que um programa de software era passado para uma equipe independente, isso gerava gargalos.

O DevOps permite que as equipes entreguem soluções mais seguras e de melhor qualidade com mais agilidade e economia. Os clientes esperam uma experiência dinâmica e confiável ao consumir serviços e software. As equipes precisam iterar com agilidade pelas atualizações de software e avaliar o impacto das atualizações. Elas precisam responder rapidamente com novas iterações de desenvolvimento para resolver problemas ou oferecer mais valor.  

AS plataformas de nuvem, como o Microsoft Azure, removem gargalos tradicionais e ajudam a massificar a infraestrutura. Softwares estão presentes em todas as empresas como o principal diferencial nos resultados dos negócios. Nenhuma organização, desenvolvedor ou trabalhador de TI pode ou deve evitar o movimento do DevOps.

Os profissionais de DevOps experientes adotam várias das práticas a seguir. Essas práticas [envolvem pessoas](/azure/devops/learn/what-is-devops-culture) para formar estratégias com base em cenários de negócios. As ferramentas podem ajudar a automatizar as várias práticas.

- As técnicas de [Gerenciamento de projeto e planejamento Agile](https://www.visualstudio.com/learn/what-is-agile/) são usadas para planejar e isolar o trabalho em sprints, gerenciar a capacidade de equipe e ajuda as equipes a se adaptarem rapidamente às necessidades dos negócios.
- O [controle de versão, geralmente com Git](/azure/devops/learn/git/what-is-git), permite que equipes localizadas em qualquer lugar do mundo compartilhem fontes e integrem a ferramentas de desenvolvimento de software para automatizar o pipeline da versão.
- A [integração contínua](/azure/devops/learn/what-is-continuous-integration) conduz a mesclagem contínua e o teste do código, o que permite a localização antecipada dos defeitos.  Outros benefícios incluem menos tempo gasto no combate a problemas de mesclagem e o recebimento de comentários rapidamente as para equipes de desenvolvimento.
- A [entrega contínua](/azure/devops/learn/what-is-continuous-delivery) de soluções de software para ambientes de produção e teste ajuda as organizações a corrigir bugs rapidamente e atender aos requisitos de negócios em constante mudança.
- O [monitoramento](/azure/devops/learn/what-is-monitoring) dos aplicativos em execução – incluindo ambientes de produção para a integridade do aplicativo, assim como o uso do cliente – ajuda as organizações a formular uma hipótese e validar ou refutar rapidamente as estratégias.  Dados avançados são capturados e armazenados em vários formatos de log.
- A [IaC (Infraestrutura como Código)](/azure/devops/learn/what-is-infrastructure-as-code) é uma prática que permite a automação e validação da criação e desmontagem de redes e máquinas virtuais para ajudar a fornecer plataformas de hospedagem de aplicativos seguras e estáveis.
- A arquitetura de [microsserviços](/azure/devops/learn/what-are-microservices) é usada para isolar casos de uso de negócios em serviços menores reutilizáveis.  Essa arquitetura oferece eficiência e escalabilidade.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a solução de Segurança e Auditoria, consulte os seguintes artigos:

- [Segurança e conformidade](https://azure.microsoft.com/overview/trusted-cloud/)
- [Central de Segurança do Azure](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)