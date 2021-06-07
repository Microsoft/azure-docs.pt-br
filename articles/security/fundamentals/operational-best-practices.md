---
title: Práticas recomendadas de segurança para seus ativos do Azure
titleSuffix: Azure security
description: Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498839"
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de Segurança Operacional do Azure
Este artigo fornece um conjunto de práticas recomendadas operacionais para proteger seus dados, aplicativos e outros ativos no Azure.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. As opiniões e tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implantar práticas fortes de segurança operacional
A segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Azure. A segurança operacional do Azure se baseia em uma estrutura que incorpora o conhecimento adquirido por recursos exclusivos da Microsoft, incluindo [o SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl), o programa [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e o conhecimento profundo do cenário de ameaças de segurança cibernética.

## <a name="manage-and-monitor-user-passwords"></a>Gerenciar e monitorar senhas de usuário
A tabela a seguir lista algumas das práticas recomendadas relacionadas ao gerenciamento de senhas de usuário:

**Prática recomendada**: Verifique se você tem o nível apropriado de proteção por senha na nuvem.   
**Detalhe**: siga as orientações em [diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/), que tem como escopo os usuários das plataformas de identidade da microsoft (Azure Active Directory, Active Directory e conta Microsoft).

**Prática recomendada**: monitorar ações suspeitas relacionadas às suas contas de usuário.   
**Detalhe**: Monitore para [usuários em risco](../../active-directory/identity-protection/overview-identity-protection.md) e [entradas arriscadas](../../active-directory/identity-protection/overview-identity-protection.md) usando os relatórios de segurança do Azure AD.

**Prática recomendada**: detectar e corrigir automaticamente as senhas de alto risco.   
**Detalhe**: [Azure ad Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) é um recurso da edição Azure ad Premium P2 que permite que você:

- Detectar possíveis vulnerabilidades que afetam as identidades da sua organização
- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização
- Investigue incidentes suspeitos e execute as ações apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receber notificações de incidentes da Microsoft
Certifique-se de que sua equipe de operações de segurança receba notificações de incidentes do Azure da Microsoft. Uma notificação de incidente permite que sua equipe de segurança saiba que você comprometeu os recursos do Azure para que eles possam responder rapidamente e corrigir possíveis riscos de segurança.

No portal de registro do Azure, você pode garantir que as informações de contato do administrador incluem detalhes que notificam as operações de segurança. Informações de contato são um número de telefone e um endereço de email.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizar assinaturas do Azure em grupos de gerenciamento
Se a organização tiver muitas assinaturas, talvez você precise de uma forma de gerenciar o acesso, as políticas e a conformidade com eficiência para essas assinaturas. Os [grupos de gerenciamento do Azure](../../governance/management-groups/create-management-group-portal.md) fornecem um nível de escopo acima das assinaturas. Você organiza as assinaturas em contêineres chamados grupos de gerenciamento e aplica suas condições de governança a esses grupos. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo de gerenciamento.

Você pode criar uma estrutura flexível de grupos de gerenciamento e assinaturas em um diretório. Cada diretório recebe um único grupo de gerenciamento de nível superior chamado grupo de gerenciamento raiz. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. O grupo de gerenciamento raiz permite que políticas globais e atribuições de função do Azure sejam aplicadas no nível do diretório.

Aqui estão algumas práticas recomendadas para o uso de grupos de gerenciamento:

**Prática recomendada**: Verifique se as novas assinaturas aplicam elementos de governança como políticas e permissões à medida que são adicionadas.   
**Detalhe**: Use o grupo de gerenciamento raiz para atribuir elementos de segurança de toda a empresa que se aplicam a todos os ativos do Azure. Políticas e permissões são exemplos de elementos.

**Prática recomendada**: Alinhe os principais níveis de grupos de gerenciamento com a estratégia de segmentação para fornecer um ponto para a consistência de controle e de política dentro de cada segmento.   
**Detalhe**: Crie um único grupo de gerenciamento para cada segmento no grupo de gerenciamento raiz. Não crie nenhum outro grupo de gerenciamento na raiz.

**Prática recomendada**: limite a profundidade do grupo de gerenciamento para evitar confusão que atrasa as operações e a segurança.   
**Detalhe**: limite sua hierarquia a três níveis, incluindo a raiz.

**Prática recomendada**: selecione cuidadosamente quais itens aplicar a toda a empresa com o grupo de gerenciamento raiz.   
**Detalhe**: Verifique se os elementos do grupo de gerenciamento raiz têm uma clara necessidade de serem aplicados em todos os recursos e se eles têm baixo impacto.

Os bons candidatos incluem:

- Requisitos regulatórios que têm um impacto comercial claro (por exemplo, restrições relacionadas à soberania de dados)
- Os requisitos com quase zero potencial negativo afetam as operações, como política com efeito de auditoria ou atribuições de permissão do RBAC do Azure que foram cuidadosamente revisadas

**Prática recomendada**: Planeje e teste cuidadosamente todas as alterações em toda a empresa no grupo de gerenciamento raiz antes de aplicá-las (política, modelo de RBAC do Azure e assim por diante).   
**Detalhe**: as alterações no grupo de gerenciamento raiz podem afetar cada recurso no Azure. Embora eles forneçam uma maneira poderosa de garantir a consistência em toda a empresa, os erros ou o uso incorreto podem afetar negativamente as operações de produção. Testar todas as alterações no grupo de gerenciamento raiz em um laboratório de teste ou em um piloto de produção.

## <a name="streamline-environment-creation-with-blueprints"></a>Simplifique a criação do ambiente com plantas
[O serviço de plantas do Azure](../../governance/blueprints/overview.md) permite que os arquitetos de nuvem e os grupos de tecnologia da informação central definam um conjunto repetível de recursos do Azure que implementam e aderem aos padrões, padrões e requisitos de uma organização. As plantas do Azure possibilitam que as equipes de desenvolvimento compilem e criem rapidamente novos ambientes com um conjunto de componentes internos e a confiança de que eles estão criando esses ambientes dentro da conformidade organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorar serviços de armazenamento quanto a mudanças inesperadas no comportamento
Questões de diagnóstico e de solução de problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas que em ambientes tradicionais. Aplicativos podem ser implantados em uma infraestrutura PaaS ou IaaS, local, em um dispositivo móvel ou em alguma combinação desses ambientes. Tráfego de rede do seu aplicativo pode passar por redes públicas e privadas, e seu aplicativo poderá usar várias tecnologias de armazenamento.

Você deve monitorar continuamente os serviços de armazenamento que o aplicativo usa para qualquer mudança inesperada em comportamento (como tempos de resposta mais lentos). Use o log para coletar dados mais detalhados e analisar o problema em profundidade. As informações de diagnósticos que você obtiver tanto do monitoramento quanto do registro em log o ajudarão a determinar a raiz do problema que o seu aplicativo encontrou. Você poderá solucionar o problema e determinar as etapas apropriadas para corrigi-lo.

A [Análise de Armazenamento do Azure](../../storage/common/storage-analytics.md) executa o registro em log e fornece dados de métrica para uma conta de armazenamento do Azure. Recomendamos que você use esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detectar e reagir a ameaças
A [central de segurança do Azure](../../security-center/security-center-introduction.md) ajuda você a prevenir, detectar e responder a ameaças, fornecendo maior visibilidade sobre (e controlar) a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um diversas soluções de segurança.

A camada gratuita da central de segurança oferece segurança limitada apenas para os recursos do Azure. A camada Standard estende esses recursos para locais e outras nuvens. A central de segurança Standard ajuda a localizar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando análise e inteligência e responder rapidamente quando sob ataque. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. Recomendamos que você [Atualize sua assinatura do Azure para a central de segurança Standard](../../security-center/security-center-get-started.md).

Use a central de segurança para obter uma exibição central do estado de segurança de todos os seus recursos do Azure. Verifique rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, além disso, identifique com rapidez os recursos que exigem atenção.

A central de segurança também se integra à [ATP (proteção avançada contra ameaças) do Microsoft defender](../../security-center/security-center-wdatp.md), que fornece recursos de EDR (detecção e resposta de ponto de extremidade) abrangentes. Com a integração do Microsoft defender ATP, você pode identificar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela central de segurança.

Quase todas as organizações empresariais têm um sistema SIEM (gerenciamento de informações e eventos de segurança) para ajudar a identificar ameaças emergentes, consolidando informações de log de diferentes dispositivos de coleta de sinais. Os logs são então analisados por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todas as soluções de coleta e análise de logs.

O [Azure Sentinel](../../sentinel/overview.md) é uma solução de disparar (gerenciamento de informações de segurança e segurança) escalonável, nativa de nuvem, de Siem (Information and Event Management). O Azure Sentinel fornece análise de segurança inteligente e inteligência contra ameaças por meio de detecção de alertas, visibilidade de ameaças, busca proativa e resposta automatizada contra ameaças.

Aqui estão algumas práticas recomendadas para impedir, detectar e responder a ameaças:

**Prática recomendada**: aumente a velocidade e a escalabilidade de sua solução Siem usando um Siem baseado em nuvem.   
**Detalhe**: investigue os recursos e as capacidades do [Azure Sentinel](../../sentinel/overview.md) e compare-os com os recursos do que você está usando no momento. Considere a adoção do Azure Sentinel se ele atender aos requisitos de SIEM de sua organização.

**Prática recomendada**: Encontre as vulnerabilidades de segurança mais sérias para que você possa priorizar a investigação.   
**Detalhe**: examine sua [Pontuação de segurança do Azure](../../security-center/secure-score-security-controls.md) para ver as recomendações resultantes das políticas e iniciativas do Azure criadas na central de segurança do Azure. Essas recomendações ajudam a resolver os principais riscos, como atualizações de segurança, proteção de ponto de extremidade, criptografia, configurações de segurança, WAF ausentes, VMs conectadas à Internet e muito mais.

A pontuação segura, que é baseada em controles de CIS (Center for Internet Security), permite que você benchmark a segurança do Azure de sua organização em relação a fontes externas. A validação externa ajuda a validar e enriquecer a estratégia de segurança da sua equipe.

**Prática recomendada**: monitorar a postura de segurança de máquinas, redes, armazenamento e serviços de dados e aplicativos para descobrir e priorizar possíveis problemas de segurança.  
**Detalhe**: siga as [recomendações de segurança](../../security-center/security-center-recommendations.md) na central de segurança iniciando, com os itens de prioridade mais alta.

**Prática recomendada**: integre alertas da central de segurança à sua solução de Siem (gerenciamento de informações e eventos de segurança).   
**Detalhe**: a maioria das organizações com um Siem o utiliza como uma câmara de compensação central para alertas de segurança que exigem uma resposta de analista. Os eventos processados produzidos pela central de segurança são publicados no log de atividades do Azure, um dos logs disponíveis por meio de Azure Monitor. O Azure Monitor oferece um pipeline consolidado para qualquer um dos seus dados de monitoramentos de roteamento para uma ferramenta do SIEM. Consulte [transmitir alertas para uma solução Siem, disparar ou de gerenciamento de serviço de ti](../../security-center/export-to-siem.md) para obter instruções. Se você estiver usando o Azure Sentinel, consulte [conectar a central de segurança do Azure](../../sentinel/connect-azure-security-center.md).

**Prática recomendada**: integre os logs do Azure ao Siem.   
**Detalhe**: use [Azure monitor para coletar e exportar dados](../../azure-monitor/overview.md#integrate-and-export-data). Essa prática é essencial para habilitar a investigação de incidentes de segurança e a retenção de log online é limitada. Se você estiver usando o Azure Sentinel, consulte [conectar fontes de dados](../../sentinel/connect-data-sources.md).

**Prática recomendada**: Acelere seus processos de investigação e busca e reduza os falsos positivos integrando recursos de EDR (detecção de ponto de extremidade e resposta) à sua investigação de ataque.   
**Detalhe**: [habilitando a integração do Microsoft defender para ponto de extremidade](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration) por meio da sua política de segurança da central de segurança. Considere usar o Azure Sentinel para a busca de ameaças e a resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Acompanhar monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure, combinando recursos de rede como rede virtual, ExpressRoute, Gateway de Aplicativo e balanceadores de carga. O monitoramento está disponível em cada um dos recursos da rede.

O [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Use suas ferramentas de diagnóstico e visualização para monitorar e diagnosticar condições em um nível de cenário de rede no Azure, para o Azure e do Azure.

A seguir estão as melhores práticas para as ferramentas disponíveis e monitoramento de rede.

**Melhor prática**: automatize o monitoramento remoto de rede com a captura de pacote.  
**Detalhe**: monitore e realize o diagnóstico de problemas de rede sem fazer logon em suas VMs usando o Observador de Rede. Disparar [captura de pacote](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes para um diagnóstico melhor.

**Prática recomendada**: obtenha insight sobre seu tráfego de rede usando logs de fluxo.  
**Detalhe**: desenvolva um entendimento aprofundado sobre padrões de tráfego de rede usando os [logs de fluxo do grupo de segurança de rede](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). As informações em logs de fluxo ajudam a coletar dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

**Prática recomendada**: diagnosticar problemas de conectividade de VPN.  
**Detalhe**: use o Observador de Rede para [diagnosticar os problemas mais comuns de Gateway de VPN e conexão](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Você pode não apenas identificar o problema, como também usar logs detalhados para investigar ainda mais.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implantação segura usando ferramentas do DevOps comprovadas
Use as seguintes práticas recomendadas de DevOps para garantir que suas equipes e sua empresa sejam produtivas e eficientes.

**Prática recomendada**: automatizar a compilação e a implantação de serviços.  
**Detalhe**: [infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI a remover a sobrecarga de compilação e do gerenciamento diários da infraestrutura modular. Habilita os profissionais de TI a criar e realizar a manutenção do ambiente de servidor moderno de maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo.

Você pode usar o [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para provisionar seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo em cada estágio do ciclo de vida do aplicativo.

**Melhor prática**: compile e implante automaticamente serviços de nuvem ou aplicativos Web do Azure.  
**Detalhe**: você pode configurar seu Azure DevOps Projects para  [Compilar e implantar automaticamente](/azure/devops/pipelines/index) em aplicativos Web ou serviços de nuvem do Azure. O Azure DevOps implanta automaticamente os binários depois de fazer uma compilação no Azure após cada check-in de código. O processo de build do pacote é equivalente ao comando Package no Visual Studio, e as etapas de publicação equivalem ao comando Publish do Visual Studio.

**Melhor prática**: automatizar o gerenciamento de versão.  
**Detalhe:**[Azure Pipelines](/azure/devops/pipelines/index) é uma solução para automatizar a implantação em vários estágios e gerenciar o processo de lançamento. Crie pipelines de implantação gerenciados e contínuos, a fim de lançar com rapidez, facilidade e frequência. Com o Azure Pipelines, você pode automatizar o processo de liberação e pode ter fluxos de trabalho de aprovação predefinidos. Implante localmente e na nuvem, estenda e personalize conforme a necessidade.

**Melhor prática**: verifique o desempenho de seu aplicativo antes de inicializá-lo ou implantar atualizações na produção.  
**Detalhe**: executar [testes de carga](/azure/devops/test/load-test/overview#alternatives) baseados em nuvem para:

- Localizar problemas de desempenho em seu aplicativo Web.
- Melhorar a qualidade da implantação.
- Garantir que seu aplicativo esteja sempre disponível.
- Garantir que seu aplicativo possa lidar com o tráfego da sua próxima campanha de marketing ou de seu próximo lançamento.

O [Apache JMeter](https://jmeter.apache.org/) é uma ferramenta de software livre gratuita e popular com um forte suporte de comunidade.

**Melhor prática**: monitorar o desempenho do aplicativo.  
**Detalhe**: o [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) é um serviço de APM (gerenciamento de desempenho de aplicativo) extensível para desenvolvedores da Web em várias plataformas. Use o Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

## <a name="mitigate-and-protect-against-ddos"></a>Atenuação de riscos e proteção contra DDoS
Ataque de DDoS (negação de serviço distribuído) é um tipo de ataque que tenta esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Esses ataques estão se tornando cada vez mais sofisticados e maiores tanto em termos de tamanho quanto de impacto. Eles podem ser direcionados a qualquer ponto de extremidade publicamente acessível pela Internet.

Projetar e criar para garantir a resiliência contra DDoS exige planejar e projetar para uma variedade de modos de falha. A seguir, estão as melhores práticas para a criação de serviços resilientes a DDoS no Azure.

**Melhor prática**: garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitam que um volume relativamente baixo de solicitações use muitos recursos, resultando em uma interrupção de serviço.  
**Detalhe**: para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](/azure/architecture/guide/pillars). Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

**Melhor prática**: projete seus aplicativos para que sejam [escalados horizontalmente](/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.  
**Detalhe**: para o [Serviço de Aplicativo do Azure](../../app-service/overview.md), selecione um [Plano do Serviço de Aplicativo](../../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../../cloud-services/cloud-services-choose-me.md).

Para [Máquinas Virtuais do Azure](../../virtual-machines/windows/overview.md), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../../virtual-machines/windows/tutorial-availability-sets.md). É recomendável usar conjuntos de dimensionamento de máquinas virtuais para obter recursos de dimensionamento automático.

**Melhor prática**: dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de sucesso de um ataque. Implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.  
**Detalhe**: por exemplo, o risco de ataque aumenta conforme o tamanho (área da superfície) do aplicativo. Você pode reduzir a área da superfície usando uma lista de aprovação para fechar o espaço de endereço IP exposto e portas de escuta que não são necessárias nos balanceadores de carga ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) e [aplicativo Azure gateway](../../application-gateway/application-gateway-create-probe-portal.md)).

[Grupos de segurança de rede](../../virtual-network/network-security-groups-overview.md) são outra maneira de reduzir a superfície de ataque. Você pode usar [marcas de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) e [grupos de segurança de aplicativo](../../virtual-network/network-security-groups-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão.

Usar [pontos de extremidade de serviço](../../virtual-network/virtual-network-service-endpoints-overview.md) altera o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, minimize a exposição dos recursos locais à Internet pública.

O Azure tem duas [ofertas de serviço](../../ddos-protection/ddos-protection-overview.md) contra DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A básica não exige nenhuma alteração ao aplicativo ou à configuração do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS, como o DNS do Azure.
- A proteção Standard fornece funcionalidades avançadas de atenuação de DDoS contra ataques de rede. Se ajusta automaticamente para proteger os recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de redes virtuais. Isso também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou recurso.

## <a name="enable-azure-policy"></a>Habilitar Azure Policy
[Azure Policy](../../governance/policy/overview.md) é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas. Essas políticas impõem regras e efeitos sobre seus recursos, para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. O Azure Policy atende a essa necessidade, avaliando os recursos quanto à não conformidade com políticas atribuídas.

Habilite Azure Policy para monitorar e impor a política de escrita da sua organização. Isso garantirá a conformidade com os requisitos de sua empresa ou de segurança regulatória gerenciando centralmente políticas de segurança em suas cargas de trabalho de nuvem híbrida. Saiba como [criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md). Consulte [estrutura de definição de Azure Policy](../../governance/policy/concepts/definition-structure.md) para obter uma visão geral dos elementos de uma política.

Aqui estão algumas práticas recomendadas de segurança a serem seguidas depois que você adotar Azure Policy:

**Prática recomendada**: a política dá suporte a vários tipos de efeitos. Você pode ler sobre eles na [estrutura de definição de Azure Policy](../../governance/policy/concepts/definition-structure.md#policy-rule). As operações de negócios podem ser afetadas negativamente pelo efeito de **negação** e o efeito de **correção** , portanto, comece com o efeito de **auditoria** para limitar o risco de impacto negativo da política.   
**Detalhe**: [inicie as implantações de política no modo de auditoria](../../governance/policy/concepts/definition-structure.md#policy-rule) e, posteriormente, progresso para **negar** ou **corrigir**. Teste e examine os resultados do efeito de auditoria antes de mover para **negar** ou **corrigir**.

Para obter mais informações, consulte [criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md).

**Prática recomendada**: identificar as funções responsáveis pelo monitoramento de violações de política e garantir que a ação de correção correta seja executada rapidamente.   
**Detalhe**: tenha a conformidade do monitor de função atribuída por meio da [portal do Azure](../../governance/policy/how-to/get-compliance-data.md#portal) ou por meio da [linha de comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Prática recomendada**: Azure Policy é uma representação técnica das políticas escritas de uma organização. Mapeie todas as definições de Azure Policy para políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: mapeamento de documento na documentação da sua organização ou na própria definição de Azure Policy adicionando uma referência à política organizacional na definição de [política](../../governance/policy/concepts/definition-structure.md#display-name-and-description) ou na descrição de [definição de iniciativa](../../governance/policy/concepts/initiative-definition-structure.md#metadata) .

## <a name="monitor-azure-ad-risk-reports"></a>Monitorar relatórios de risco do Azure AD
A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure AD usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado [detecção de risco](../../active-directory/identity-protection/overview-identity-protection.md). As detecções de risco são registradas nos relatórios de segurança do Azure AD. Para obter mais informações, leia sobre o [relatório de segurança de usuários em risco](../../active-directory/identity-protection/overview-identity-protection.md) e o [relatório de segurança de entradas arriscadas](../../active-directory/identity-protection/overview-identity-protection.md).

## <a name="next-steps"></a>Próximas etapas
Veja [Melhores práticas e padrões de segurança do Azure](best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](/archive/blogs/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email para secure@microsoft.com