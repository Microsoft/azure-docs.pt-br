---
title: "Melhores práticas de Segurança Operacional do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de Segurança Operacional do Azure."
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
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de Segurança Operacional do Azure
A Segurança Operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros recursos no Microsoft Azure. A Segurança Operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento adquirido por meio de várias funcionalidades que são exclusivas à Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma conscientização profunda do cenário de ameaças à segurança cibernética.

Neste artigo, veremos uma coleção de melhores práticas de segurança de banco de dados do Azure. Essas melhores práticas derivam da nossa experiência com segurança de banco de dados do Azure e da experiência de clientes como você.

Para cada prática recomendada, vamos explicar:
-   O que é a prática recomendada
-   Por que é ideal habilitar essa prática recomendada
-   O que poderá acontecer se você não habilitar a prática recomendada
- Como você pode aprender a habilitar a prática recomendada

Este artigo Melhores práticas de Segurança Operacional do Azure baseia-se em um consenso e nos recursos da plataforma Azure e em conjuntos de recursos tal como existiam no momento em que o artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

As melhores práticas de Segurança Operacional do Azure discutidas neste artigo incluem:

-   Monitorar, gerenciar e proteger a infraestrutura de nuvem
-   Gerenciar identidade e implementar o SSO (logon único)
-   Rastrear solicitações, analisar tendências de uso e diagnosticar problemas
-   Serviços de monitoramento com uma solução de monitoramento centralizada
-   Evitar, detectar e reagir a ameaças
-   Monitoramento de rede baseado em cenário de ponta a ponta
-   Implantação segura usando ferramentas do DevOps comprovadas

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorar, gerenciar e proteger a infraestrutura de nuvem
As Operações de TI são responsáveis por gerenciar a infraestrutura de datacenter, os aplicativos e os dados, incluindo a estabilidade e a segurança desses sistemas. No entanto, a obtenção de informações de segurança em ambientes de TI cada vez mais complexos geralmente exige que as empresas reúnam dados de vários sistemas de gerenciamento e segurança.

O [OMS (Microsoft Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem.

A [solução de Segurança e Auditoria do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permite que o departamento de TI monitore de forma ativa todos os recursos, o que pode ajudar a minimizar o impacto dos incidentes de segurança. A Segurança e Auditoria do OMS tem domínios de segurança que podem ser usados para monitorar os recursos.

Para obter mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Para ajudá-lo a evitar, detectar e responder a ameaças, a [solução de Segurança e Auditoria do OMS (Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) coleta e processa dados sobre seus recursos, o que inclui:

-   Log de eventos de segurança
-   Eventos de ETW (Rastreamento de Eventos para Windows)
-   Eventos de auditoria do AppLocker
-   Log do Firewall do Windows
-   Eventos de Análise de Ameaças Avançadas
-   Resultados da avaliação de linha de base
-   Resultados da avaliação antimalware
-   Resultados da avaliação de atualização/patch
-   Fluxos de Syslog habilitados explicitamente no agente


## <a name="manage-identity-and-implement-single-sign-on"></a>Gerenciar identidade e implementar o logon único
O [Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft.

O [Azure AD](https://azure.microsoft.com/services/active-directory/) também inclui um pacote completo de funcionalidades de [gerenciamento de identidade](https://docs.microsoft.com/azure/security/security-identity-management-overview), incluindo [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), registro de dispositivos, gerenciamento de senhas de autoatendimento, gerenciamento de grupos de autoatendimento, gerenciamento de contas com privilégios, controle de acesso baseado em função, monitoramento de uso de aplicativos, auditoria avançada e alertas e monitoramento de segurança.

Os recursos abaixo podem ajudar a proteger aplicativos com base na nuvem, simplificar os processos de TI, cortar custos e ajudar a garantir que as metas de conformidade corporativa sejam atingidas:

-   Gerenciamento de identidade e acesso para a nuvem
-   Simplifique o acesso do usuário a qualquer aplicativo de nuvem
-   Proteja dados e aplicativos confidenciais
-   Habilite o autoatendimento aos seus funcionários
-   Integre-se com o Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Gerenciamento de identidade e acesso para a nuvem
O Azure Active Directory (Azure AD) é uma [solução na nuvem de gerenciamento de acesso e de identidade](https://www.microsoft.com/cloud-platform/identity-management) abrangente que oferece um conjunto consistente de funcionalidades para gerenciar usuários e grupos. Ele ajuda a assegurar o acesso a aplicativos locais e na nuvem, incluindo serviços Web da Microsoft como o Office 365 e muitos aplicativos SaaS (software como serviço) não Microsoft.
Para saber mais como habilitar a proteção de identidade no Azure AD, confira [Habilitando o Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Simplifique o acesso do usuário a qualquer aplicativo de nuvem
[Habilite o logon único](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) para simplificar o acesso do usuário aos milhares de aplicativos na nuvem dos dispositivos Windows, Mac, Android e iOS. Os usuários podem iniciar os aplicativos por meio de um painel de acesso personalizado baseado na Web ou por meio de um aplicativo móvel, usando suas credenciais da empresa. Utilize o módulo do Proxy de Aplicativo do Azure AD e vá além de aplicativos SaaS, publicando aplicativos Web locais para fornecer acesso remoto extremamente seguro e logon único.

### <a name="protect-sensitive-data-and-applications"></a>Proteja dados e aplicativos confidenciais
A [Autenticação Multifator do Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) ajuda a proteger contra o acesso não autorizado a aplicativos locais e na nuvem fornecendo um nível adicional de autenticação. Proteja seus negócios e reduza ameaças em potencial com monitoramento de segurança, alertas e relatórios baseados em machine learning que identificam padrões de acesso inconsistentes.

### <a name="enable-self-service-for-your-employees"></a>Habilite o autoatendimento aos seus funcionários
Delegue tarefas importantes aos seus funcionários, como redefinição de senhas e criação e gerenciamento de grupos. [Habilite alteração de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), redefinição e gerenciamento de grupos por autoatendimento com o Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integre-se com o Azure Active Directory
Estenda o [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) e outros diretórios locais ao Azure AD para habilitar logon único em todos os aplicativos baseados em nuvem. Os atributos de usuário podem ser sincronizados automaticamente com o diretório na nuvem em todos os tipos de diretórios locais.

Para saber mais sobre a integração do Azure Active Directory e como habilitá-lo, leia o artigo [Integrar os diretórios locais com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Rastrear solicitações, analisar tendências de uso e diagnosticar problemas
O [Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) executa o registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

A métrica da Análise de Armazenamento vem habilitada por padrão nas novas contas de armazenamento. Você pode habilitar o registro em log e configurar a métrica e o registro em log no portal do Azure. Para obter detalhes, confira [Monitorar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação Definir Propriedades do Serviço para habilitar o Storage Analytics individualmente em cada serviço.

Para um guia aprofundado sobre como usar a Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao Armazenamento do Azure, consulte [Monitorar, diagnosticar e solucionar problemas do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Para saber mais sobre a integração do Azure Active Directory e como habilitá-lo, leia o artigo [Habilitando e configurando a Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Serviços de monitoramento
Os aplicativos em nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de monitoramento para obter mais informações sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

### <a name="monitor-azure-resources"></a>Monitorar os recursos do Azure
O [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) é o serviço de plataforma que fornece uma única fonte para monitorar os recursos do Azure. Com o Azure Monitor, você pode visualizar, consultar, rotear, arquivar e tomar medidas relacionadas às métricas e aos logs provenientes dos recursos do Azure. Você pode trabalhar com esses dados usando a folha do portal do Monitor, [Cmdlets do PowerShell do Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [CLI de plataforma cruzada](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) ou [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Habilitar o Dimensionamento Automático com o Azure Monitor
Habilite o [Dimensionamento Automático do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started), que se aplica somente aos VMSS (conjuntos de dimensionamento da máquina virtual), aos serviços de nuvem, aos planos do Serviço de Aplicativo e aos ambientes do Serviço de Aplicativo.

### <a name="manage-roles-permissions-and-security"></a>Gerencie permissões e segurança de funções
Muitas equipes precisam [regular estritamente o acesso aos dados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) e configurações de monitoramento. Por exemplo, se você tiver os membros da equipe que trabalham exclusivamente no monitoramento (engenheiros de suporte, engenheiros de devops) ou se você usar um provedor de serviços gerenciados, você talvez queira conceder-lhes acesso aos dados de monitoramento apenas enquanto restringe a capacidade de criar, modificar ou excluir recursos.

Aqui, mostramos como aplicar uma função interna de RBAC de monitoramento a um usuário no Azure rapidamente ou criar sua própria função personalizada para um usuário que precise de permissões limitadas de monitoramento. Em seguida, ele aborda considerações de segurança para os recursos relacionados ao Azure Monitor e como você pode limitar o acesso aos dados contidos nos mesmos.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detectar e reagir a ameaças
A detecção de ameaças da Central de Segurança funciona coletando informações de segurança dos recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Os alertas de segurança são priorizados na Central de Segurança, juntamente com recomendações sobre como corrigir a ameaça.

-   [Configure uma política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para sua assinatura do Azure.
-   Use as [recomendações na Central de Segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations) para ajudar a proteger os recursos do Azure.
-   Confira e gerencie os [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) atuais.

A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda você a impedir, detectar e responder a ameaças com maior visibilidade e controle sobre a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

A Central de Segurança oferece uma prevenção contra ameaças eficiente e fácil de usar, além de recursos de detecção e resposta que são internos no Azure. Os principais recursos são:

-   Entenda o estado da segurança na nuvem
-   Controle a segurança da nuvem
-   Implante facilmente soluções de segurança em nuvem integradas
-   Detecte ameaças e responda rapidamente

### <a name="understand-cloud-security-state"></a>Entenda o estado da segurança na nuvem
Use a Central de Segurança do Azure para obter uma exibição central do estado da segurança de todos os seus recursos do Azure. Verifique rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, além disso, identifique com rapidez os recursos, o que exige atenção.

### <a name="take-control-of-cloud-security"></a>Controle a segurança da nuvem
Defina [políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para suas assinaturas do Azure de acordo com as necessidades de segurança em nuvem de sua empresa, adaptadas para os tipos de aplicativos ou para a confidencialidade dos dados de cada assinatura. Use recomendações baseadas em políticas para direcionar os proprietários dos recursos pelo processo de implementação dos controles necessários, eliminando processos baseados em adivinhação da segurança da nuvem.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Implante facilmente soluções de segurança em nuvem integradas
[Habilite soluções de segurança](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) da Microsoft e de parceiros, incluindo firewalls e antimalwares líderes da indústria. Use o provisionamento simplificado para implantar soluções de segurança – até mesmo as alterações de rede são configuradas para você. Seus eventos de segurança de soluções de parceiros são coletados automaticamente para análise e alerta.

### <a name="detect-threats-and-respond-fast"></a>Detecte ameaças e responda rapidamente
Esteja sempre preparado para enfrentar as ameaças em nuvem atuais e emergentes com uma abordagem integrada e baseada em análise. Ao combinar a experiência e a [inteligência global](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) de ameaças da Microsoft com informações sobre eventos relacionados à segurança da nuvem em suas implantações do Azure, a Central de Segurança ajuda a detectar ameaças reais de maneira precoce e a reduzir os falsos positivos. Os alertas de segurança da nuvem fornecem informações sobre a campanha de ataque, incluindo os eventos relacionados e os recursos afetados, além de sugerir formas de correção de problemas e de recuperação rápida.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure administrando e compondo vários recursos de rede individuais, como VNet, ExpressRoute, Gateway de Aplicativo, Balanceadores de carga e mais. O monitoramento está disponível em cada um dos recursos da rede.

O [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatizar o monitoramento remoto de rede com a captura de pacote
Monitore e diagnostique problemas de rede sem fazer logon em suas VMs (máquinas virtuais), usando o Observador de Rede. Disparar [captura de pacote](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes para um diagnóstico melhor.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Obtenha informações sobre seu tráfego de rede usando os logs de fluxo
Criar um entendimento aprofundado sobre o padrão de tráfego de sua rede, usando os [logs de fluxo do Grupo de Segurança de Rede](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview). As informações fornecidas pelos logs de fluxo ajudam a reunir dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnosticar problemas de conectividade de VPN
O Observador de Rede fornece a habilidade para [diagnosticar os problemas mais comuns de Gateway de VPN e de conexões](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Permite que você não apenas identifique o problema, mas que também use os logs detalhados criados para ajudar a investigar ainda mais.

Para saber mais sobre como configurar o Observador de Rede e como habilitá-lo, leia o artigo [Configurar Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Implantação segura usando ferramentas do DevOps comprovadas
Estas são algumas práticas da Lista de práticas do Azure DevOps nesse espaço do Microsoft Cloud, que tornam as empresas e equipes mais produtivas e eficientes.

-   **IaC (Infraestrutura como código):** infraestrutura como código é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI a se livrar da sobrecarga associada à compilação e o gerenciamento diários da infraestrutura modular. Ela permite que os profissionais de TI criem e mantenham seu ambiente de servidor moderno de maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo. O [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) permite que você provisione seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.
-   **Integração e implantação contínuas:** você pode configurar seus projetos de equipe do Visual Studio Online para [compilação e implantação automática](https://www.visualstudio.com/docs/build/overview) em aplicativos Web ou serviços de nuvem do Azure. O VSO implanta os binários automaticamente depois de fazer uma compilação para o Azure após cada verificação do código. O processo de compilação do pacote descrito aqui equivale ao comando Package do Visual Studio, e as etapas de publicação equivalem ao comando Publish do Visual Studio.
-   **Gerenciamento de versão:** o Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) é uma ótima solução para automatizar a implantação em vários estágios e gerenciar o processo de lançamento. Crie pipelines de implantação gerenciados e contínuos, a fim de lançar com rapidez, facilidade e frequência. Com o Release Management, podemos automatizar grande parte do nosso processo de lançamento e ter fluxos de trabalho de aprovação predefinidos. Implante localmente e na nuvem, estenda e personalize conforme a necessidade.
-   **Monitoramento de desempenho do aplicativo:** detecte e resolva problemas e melhore continuamente seus aplicativos. Diagnostique problemas rapidamente em seu aplicativo em tempo real. Entenda o que os usuários fazem com ele. A configuração é somente uma questão de adicionar código JS e uma entrada de webconfig, para ver resultados em minutos no portal com todos os detalhes. O [App Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) ajuda as empresas a detectar problemas e corrigi-los mais rapidamente.
-   **Teste de Carga e Auto-Escala:** é possível localizar problemas de desempenho no aplicativo para melhorar a qualidade de implantação e garantir que o aplicativo esteja sempre disponível ou disponível para atender às necessidades da empresa. Verifique se seu aplicativo pode lidar com o tráfego da sua próxima campanha de marketing ou de seu próximo lançamento. Inicie a execução de [testes de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) baseados em nuvem sem demora com o Visual Studio Online.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Segurança Operacional Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Para saber mais, [Operations Management Suite | Segurança e Conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Introdução à solução de Segurança e Auditoria do Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
