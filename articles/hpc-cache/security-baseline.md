---
title: Linha de base de segurança do Azure para cache HPC do Azure
description: A linha de base de segurança do cache HPC do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5a395f71ed1b1ab06d97b8aa6d83278d59d15c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091955"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Linha de base de segurança do Azure para cache HPC do Azure

Essa linha de base de segurança aplica diretrizes da [versão 2,0 do benchmark de segurança do Azure](../security/benchmarks/overview.md) para Microsoft Azure cache do HPC. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao cache do HPC do Azure. Os **controles** não aplicáveis ao cache HPC do Azure foram excluídos.

Para ver como o cache HPC do Azure é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança do cache HPC do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: ao implantar recursos de cache do Azure HPC, você deve criar ou usar uma rede virtual existente. 

Verifique se todas as redes virtuais do Azure seguem um princípio de segmentação empresarial que se alinha aos riscos de negócios. Qualquer sistema que possa incorrer em risco maior para a organização deve ser isolado em sua própria rede virtual e suficientemente protegido com um NSG (grupo de segurança de rede) e/ou o Firewall do Azure.

Dois pré-requisitos relacionados à rede devem ser configurados antes que você possa usar o cache: 

- Uma sub-rede dedicada para a instância de cache do HPC do Azure

- Suporte a DNS para que o cache possa acessar o armazenamento e outros recursos

O cache HPC do Azure precisa de uma sub-rede dedicada com estas qualidades: 

- A sub-rede deve ter pelo menos 64 endereços IP disponíveis.

- A sub-rede não pode hospedar nenhuma outra VM, mesmo para serviços relacionados, como computadores cliente.

- Se você usar várias instâncias de cache do HPC do Azure, cada uma precisará de sua própria sub-rede.

A prática recomendada é criar uma nova sub-rede para cada cache. Você pode criar uma nova rede virtual e uma sub-rede como parte da criação do cache.

Para usar o cache do HPC com o armazenamento NAS local, você deve garantir que determinadas portas na rede local permitam o tráfego irrestrito da sub-rede do cache HPC do Azure. 

- [Como configurar portas para acesso de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

**Orientação**: Use o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os datacenters do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública e oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. Para VPN ponto a site e VPN site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e do Azure ExpressRoute. 

Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [Quais são os modelos de conectividade do ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Visão geral da VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer o acesso de rede privada aos serviços do Azure

**Diretrizes**: Use pontos de extremidade de serviço de rede virtual do Azure para fornecer acesso seguro ao cache do HPC. Os pontos de extremidade de serviço são uma rota otimizada pela rede de backbone do Azure sem cruzar a Internet. 

O cache HPC não dá suporte ao uso do link privado do Azure para proteger seus pontos de extremidade de gerenciamento para uma rede privada. 

O acesso privado é uma medida adicional de defesa aprofundada, além da segurança de autenticação e tráfego oferecida pelos serviços do Azure.

- [Entender os pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Entenda como montar o cache do HPC do Azure](hpc-cache-mount.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Diretrizes**: Proteja seus recursos de cache do HPC contra ataques de redes externas, incluindo ataques de DDoS (negação de serviço distribuído), ataques específicos de aplicativo e tráfego de Internet não solicitado e potencialmente mal-intencionado. 

O Azure inclui recursos nativos para esta proteção: 

- Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. 
- Proteja seus ativos contra ataques de DDoS habilitando a proteção padrão de DDoS em suas redes virtuais do Azure. 
- Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados aos seus recursos de rede.

O cache HPC do Azure não se destina a executar aplicativos Web e não exige que você defina quaisquer configurações adicionais ou implante quaisquer serviços de rede extras para protegê-los contra ataques de rede externa destinados a aplicativos Web.

- [Documentação do firewall do Azure](../firewall/index.yml) 

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md) 

- [Recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md#recs-networking)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Orientação**: Use o Firewall do Azure com a filtragem baseada em inteligência contra ameaças para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. 

Quando a inspeção de carga é necessária, você pode implantar uma solução de detecção de intrusão/IPS (sistema de prevenção de invasão) de terceiros do Azure Marketplace com recursos de inspeção de conteúdo. Como alternativa, você pode optar por usar IDS/IPS baseados em host ou uma solução de EDR (detecção e resposta de ponto de extremidade) baseada em host em conjunto com o ou em vez de IDS/IPS baseados em rede.

Observação: se você tiver uma regulamentação ou outro requisito para o uso de IDS/IPS, verifique se ele sempre está ajustado para fornecer alertas de alta qualidade para sua solução SIEM.

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Recursos de IDS de terceiros do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Recurso EDR do Microsoft defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Orientação**: não aplicável; Essa recomendação destina-se a ofertas que podem ser implantadas em redes virtuais do Azure ou que têm a capacidade de definir agrupamentos de intervalos de IP permitidos para um gerenciamento eficiente. No momento, o cache do HPC não oferece suporte a marcas de serviço. 

A prática recomendada é criar uma nova sub-rede para cada cache. Você pode criar uma nova rede virtual e uma sub-rede como parte da criação do cache.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: serviço de nomes de domínio seguro (DNS)

**Orientação**: siga as práticas recomendadas para a segurança do DNS para mitigar ataques comuns, como pendente DNS, ataques de amplificações de DNS, envenenamento e falsificação de DNS e outros.

O cache HPC do Azure precisa que o DNS acesse recursos fora da rede virtual privada do cache. Se o fluxo de trabalho incluir recursos fora do Azure, você precisará configurar e proteger seu próprio servidor DNS, além de usar o DNS do Azure.

- Para acessar pontos de extremidade do armazenamento de BLOBs do Azure, computadores cliente baseados no Azure ou outros recursos do Azure, use o DNS do Azure.
- Para acessar o armazenamento local ou para se conectar ao cache de clientes fora do Azure, você precisa criar um servidor DNS personalizado que possa resolver esses nomes de host.
- Se o fluxo de trabalho incluir recursos internos e externos, configure seu servidor DNS personalizado para encaminhar solicitações de resolução específicas do Azure para o servidor DNS do Azure. 

Quando o DNS do Azure é usado como seu serviço DNS autoritativo, verifique se as zonas e os registros DNS estão protegidos contra modificações acidentais ou mal-intencionadas usando o RBAC do Azure e os bloqueios de recursos.

Se estiver configurando seu próprio servidor DNS, certifique-se de seguir estas diretrizes de segurança:

- [Guia de implantação do DNS (sistema de nomes de domínio) seguro](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Impedir entradas DNS pendente e evitar subdomínio tomada](../security/fundamentals/subdomain-takeover.md) 

- [Leia mais sobre os requisitos de acesso DNS para o cache do HPC](hpc-cache-prerequisites.md#dns-access)

- [Visão geral do DNS do Azure](../dns/dns-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: o cache do HPC do Azure não está integrado com Azure Active Directory para operações internas. No entanto, o Azure AD pode ser usado para autenticar usuários no portal do Azure ou na CLI para criar, exibir e gerenciar implantações de cache HPC e componentes relacionados.

Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e acesso padrão no Azure. Você deve padronizar o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.

- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar a postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

Observação: o Azure AD dá suporte à identidade externa que permite aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa.

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usar provedores de identidade externos para um aplicativo](../active-directory/external-identities/identity-providers.md) 

- [O que é a classificação de segurança de identidade no Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: o cache do HPC usa identidades gerenciadas pelo Azure para contas não humanas, como serviços ou automação. É recomendável usar o recurso de identidade gerenciada do Azure em vez de criar uma conta humana mais poderosa para acessar ou executar seus recursos. 

O cache do HPC pode autenticar nativamente em serviços/recursos do Azure que dão suporte à autenticação do Azure AD por meio de regras de concessão de acesso predefinidas. Isso evita a necessidade de usar credenciais embutidas em código no código-fonte ou arquivos de configuração.

- [Identidades gerenciadas pelo Azure](../active-directory/managed-identities-azure-resources/overview.md) 

- [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: o cache do HPC do Azure não se integra ao Azure ad para operações internas. No entanto, o Azure AD pode ser usado para autenticar usuários no portal do Azure ou na CLI para criar, exibir e gerenciar implantações de cache HPC e componentes relacionados.

Azure Active Directory fornece gerenciamento de acesso e identidade para recursos do Azure, aplicativos de nuvem e aplicativos locais. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros e fornecedores. Isso permite que o SSO (logon único) gerencie e proteja o acesso aos dados e aos recursos da sua organização no local e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo e seguro, além de maior visibilidade e controle.

- [Noções básicas sobre o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Orientação**: embora o cache HPC do Azure não se integre ao Azure ad para operações internas, o Azure AD pode ser usado para autenticar usuários no portal do Azure ou CLI para criar, exibir e gerenciar implantações de cache HPC e componentes relacionados.  

O Azure AD dá suporte a controles de autenticação fortes por meio da MFA (autenticação multifator) e de métodos fortes de senha.

- Autenticação multifator: habilite o Azure AD MFA e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para obter algumas práticas recomendadas em sua configuração de MFA. A MFA pode ser imposta em todos os usuários, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.
- Autenticação sem senha: três opções de autenticação sem senha estão disponíveis: Windows Hello para Empresas, aplicativo Microsoft Authenticator e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, certifique-se de usar o nível mais alto do método de autenticação forte. Distribua a política de autenticação forte apropriada para outros usuários.

O cache HPC do Azure também dá suporte à autenticação baseada em senha herdada para sistemas cliente que se conectam ao cache. Esses tipos de conexões incluem contas somente de nuvem (contas de usuário criadas diretamente no Azure) que têm uma política de senha de linha de base ou contas híbridas (contas de usuário que vêm do Active Directory local) que seguirão as políticas de senha local. 

Ao usar a autenticação baseada em senha, o Azure AD fornece um recurso de proteção de senha que impede que os usuários definam senhas que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas banidas que são atualizadas com base na telemetria, e os clientes podem aumentar a lista com base em suas necessidades (por exemplo, com identidade visual, referências culturais e assim por diante). Essa proteção por senha pode ser usada para contas híbridas e somente em nuvem.

Observação: a autenticação baseada em credenciais de senha sozinhas é suscetível a métodos de ataque populares. Para maior segurança, use autenticação forte, como MFA e uma política de senha forte. Se você usar aplicativos de terceiros e serviços do Marketplace que têm senhas padrão, defina uma senha nova e segura na primeira vez que configurar o serviço. 

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Política de senha padrão do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar senhas inadequadas usando a proteção de senha do Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: o cache do HPC do Azure pode usar Azure Active Directory para gerenciamento de usuário do portal do Azure.  Azure Active Directory fornece as seguintes fontes de dados:

- Entradas: o relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e as atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM de terceiros.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e em contas preteridas na assinatura.

O ATP (Proteção Avançada contra Ameaças) do Azure é uma solução de segurança que pode usar os sinais do Active Directory para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de membros mal-intencionados.

- [Relatórios de atividade de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

 

- [Alertas do módulo de proteção da inteligência contra ameaças da Central de Segurança do Azure](../security-center/alerts-reference.md) 

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

**Orientação**: não aplicável; O cache do HPC não permite que os clientes implantem dados persistentes no ambiente em execução.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: o cache do HPC usa o RBAC do Azure para isolar o acesso a sistemas críticos para os negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais eles estão.

Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Para criar um cache, o cache do HPC exige que os usuários tenham privilégios suficientes na assinatura para criar NICs. Se estiver usando o armazenamento de BLOBs, o colaborador de conta de armazenamento de funções do Azure e o colaborador de dados de blob de armazenamento serão necessários para o cache do HPC acessar o armazenamento 

Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo aos seus ativos críticos para os negócios, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas comerciais críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometerem ativos críticos para os negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Permissões de RBAC do cache do Azure HPC](hpc-cache-prerequisites.md#permissions)

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Orientação**: revise as contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seus níveis de acesso sejam válidos. 

O cache HPC do Azure pode usar contas do Azure Active Directory (Azure AD) para gerenciar o acesso do usuário por meio do portal do Azure e das interfaces relacionadas. O Azure AD oferece revisões de acesso que ajudam você a revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

Além disso, o Azure Privileged Identity Management pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar contas de administrador que estão obsoletas ou configuradas incorretamente.

Observação: alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

Ao usar destinos de armazenamento NFS, você precisará trabalhar com os administradores de rede e os gerenciadores de firewall para verificar as configurações de acesso e garantir que o cache do HPC do Azure poderá se comunicar com os sistemas de armazenamento NFS.

- [Para obter uma lista de permissões de cache do HPC, leia os pré-requisitos de cache do Azure HPC](hpc-cache-prerequisites.md) 

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: o cache do HPC pode usar Azure Active Directory para gerenciar o acesso aos recursos por meio do portal do Azure. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../active-directory/roles/security-emergency-access.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: o cache do HPC pode usar Azure Active Directory para gerenciar o acesso a recursos de cache por meio do portal do Azure. 

Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios. 

- [O que são revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é o gerenciamento de direitos do AD do Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Diretrizes**: o cache do HPC é integrado ao RBAC (controle de acesso baseado em função) do Azure para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. 

Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD Privileged Identity Management (PIM) e deve ser revisado periodicamente.

Use funções internas para alocar a permissão e somente crie uma função personalizada quando necessário.

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) 

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: descobrir, classificar e rotular dados confidenciais 

**Diretrizes**: o cache do HPC gerencia dados confidenciais, mas não tem capacidade de descobrir, classificar e rotular dados confidenciais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: proteja dados confidenciais restringindo o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure), controles de acesso baseados em rede e controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados).

Para garantir um controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa. A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados.

- [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/overview.md) 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitorar a transferência não autorizada de dados confidenciais

**Diretrizes**: o cache do HPC transmite dados confidenciais, mas não dá suporte ao monitoramento de transferência não autorizada de dados confidenciais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Diretrizes**: o cache do HPC dá suporte à criptografia de dados em trânsito com TLS v 1.2 ou superior.

Embora isso seja opcional para o tráfego em redes privadas, isso é essencial para o tráfego em redes externas e públicas. Para o tráfego HTTP, verifique se os clientes que se conectam aos recursos do Azure podem negociar TLS v 1.2 ou superior. Para gerenciamento remoto, use SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não criptografado. As versões e os protocolos de SSL, TLS e SSH obsoletos e as codificações fracas devem ser desabilitadas.

Por padrão, o Azure fornece criptografia para dados em trânsito entre os data centers do Azure.

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informações sobre a Segurança do TLS](/security/engineering/solving-tls1-problem) 

- [Criptografia dupla para dados do Azure em trânsito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: criptografar dados confidenciais em repouso

**Orientação**: para complementar os controles de acesso, os dados em repouso devem ser protegidos contra ataques "fora de banda" (por exemplo, acessando o armazenamento subjacente) usando a criptografia. Isso ajuda a garantir que os invasores não possam ler ou modificar os dados facilmente.

Por padrão, o Azure fornece dados em criptografia REST. Para dados altamente confidenciais, você tem opções para implementar criptografia adicional em repouso em todos os recursos do Azure, quando disponível. O Azure gerencia suas chaves de criptografia por padrão, mas o Azure fornece opções para gerenciar suas próprias chaves (chaves gerenciadas pelo cliente) para determinados serviços do Azure.

Todos os dados armazenados no Azure, incluindo os discos de cache, são criptografados em repouso usando chaves gerenciadas pela Microsoft por padrão. Você só precisa personalizar as configurações de cache do HPC do Azure se quiser gerenciar as chaves usadas para criptografar seus dados.

Se necessário para a conformidade em recursos de computação, implemente uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso aos dados, mesmo quando os dados são copiados fora de um sistema.

- [Como usar chaves de criptografia gerenciadas pelo cliente com o cache do Azure HPC](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [Modelo de criptografia e tabela de gerenciamento de chaves](../security/fundamentals/encryption-atrest.md)

 

- [Criptografia dupla de dados em repouso no Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização. 

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas. 

Observação: podem ser necessárias permissões adicionais a fim de obter visibilidade das cargas de trabalho e dos serviços. 

- [Visão geral da função Leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

**Diretrizes**: o cache HPC do Azure dá suporte ao uso de marcas. Aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. 

Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção. As marcas podem ser adicionadas ao criar um cache, bem como depois que o cache é implantado. 

Use o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre o software em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, habilite o diagnóstico de nível de convidado e traga os logs de eventos do Windows para um espaço de trabalho Log Analytics.
O cache do HPC não permite a execução de um aplicativo ou instalação de software em seus recursos. 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md) 

- [Guia de decisão de marcação e nomenclatura de recurso](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: o cache do HPC dá suporte a implantações de Azure Resource Manager. Use o Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Configurar e gerenciar Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretrizes**: Não aplicável. O cache do HPC do Azure não pode ser usado para garantir a segurança de ativos em um processo de gerenciamento do ciclo de vida. É responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados alto impacto. 

É recomendável que o cliente crie um processo para capturar as alterações de configuração de atributo e de rede, medir o impacto da alteração e criar tarefas de correção, conforme aplicável.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Diretrizes**: Use o recurso de detecção de ameaças interna da central de segurança do Azure e habilite o Azure defender (proteção avançada contra ameaças do Azure) para seus recursos de cache do HPC. O Azure defender para o cache do HPC fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus recursos de cache.

Encaminhe todos os logs do cache do HPC para o SIEM, que pode ser usado para configurar as detecções de ameaças personalizadas. Verifique se você está monitorando diferentes tipos de ativos do Azure para possíveis ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir os falsos positivos para os analistas classificarem. Os alertas podem ser originados de dados de log, agentes ou outros dados.

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md) 

- [Guia de referência de alertas de segurança da central de segurança do Azure](../security-center/alerts-reference.md) 

- [Criar regras de análise personalizadas para detectar ameaças](../sentinel/tutorial-detect-threats-custom.md) 

- [Inteligência contra ameaças cibernéticos com o Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Diretrizes**: o Azure ad fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure ad ou integrados com o Azure monitor, o Azure sentinela ou outras ferramentas de monitoramento/Siem para casos de uso de análise e monitoramento mais sofisticados:
- Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

- Logs de auditoria-fornecem rastreamento por meio de logs para todas as alterações feitas por vários recursos no Azure AD. Exemplos de logs de auditoria incluem alterações feitas em qualquer recurso no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha ou contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de Proteção contra Ameaças da Central de Segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (BD SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade em anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividade de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Orientação**: você pode usar gateways de VPN e seu recurso de captura de pacotes, além de ferramentas de captura de pacote normalmente disponíveis para gravar pacotes de rede viajando entre suas redes virtuais.

Implante um grupo de segurança de rede na rede onde os recursos de cache do Azure HPC são implantados. Habilite os logs de fluxo do grupo de segurança de rede em seus grupos de segurança de rede para a auditoria de tráfego.

Seus logs de fluxo são mantidos em uma conta de armazenamento. Habilite a solução Análise de Tráfego para processar e enviar esses logs de fluxo para um espaço de trabalho Log Analytics. Análise de Tráfego fornece informações adicionais sobre o fluxo de tráfego para suas redes do Azure. Análise de Tráfego pode ajudá-lo a visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar incorretas configurações de rede.

O cache precisa do DNS para acessar recursos fora de sua rede virtual. Dependendo de quais recursos você está usando, talvez seja necessário configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure. 

Implemente uma solução de terceiros do Azure Marketplace para a solução de registro em log DNS de acordo com as necessidades de suas organizações.

- [Configurar capturas de pacote para gateways de VPN](../vpn-gateway/packet-capture.md) 

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md) 

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md) 

- [Saiba mais sobre os pré-requisitos de DNS](hpc-cache-prerequisites.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: os recursos de cache do Azure HPC criam logs de atividade automaticamente. Esses logs contêm todas as operações de gravação (PUT, POST, DELETE), mas não incluem operações de leitura (GET). Os logs de atividades podem ser usados para encontrar um erro durante a solução de problemas ou para monitorar como um usuário em sua organização modificou um recurso.

Você também pode usar a central de segurança do Azure e Azure Policy para habilitar os logs de recursos do Azure para o cache do HPC e para a coleta de dados de log. Esses logs podem ser essenciais para investigar posteriormente os incidentes de segurança e executar exercícios forenses.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Diretriz**: verifique se a sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está os exercendo regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: preparação – configurar notificação de incidente 

**Diretriz**: configure as informações de contato de incidentes de segurança na Central de Segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – criar incidentes com base em alertas de alta qualidade

**Diretrizes**: verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade de alertas. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência com incidentes passados, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas, focando e correlacionando várias fontes de sinal. 

A central de segurança do Azure fornece alertas de alta qualidade em vários ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação. 

Exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. Exporte os alertas e as recomendações de modo manual ou contínuo.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detecção e análise – investigar um incidente

**Diretriz**: verifique se os analistas podem consultar e usar fontes de dados diversificadas à medida que investigam incidentes potenciais, para criar uma visão completa do que aconteceu. Logs diversificados devem ser coletados para acompanhar as atividades de um invasor potencial na cadeia de eliminação para evitar pontos cegos.  Você também deve garantir que os insights e os aprendizados sejam capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes de log centralizadas que já estão sendo coletadas dos serviços no escopo e dos sistemas em execução, mas também podem incluir:

- Dados de rede – Use os logs de fluxo dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar logs de fluxo de rede e outras informações de análise. 

- Instantâneos dos sistemas em execução: 

    - Use a funcionalidade de instantâneo da máquina virtual do Azure para criar um instantâneo do disco do sistema em execução. 

    - Use a funcionalidade de despejo de memória nativa do sistema operacional para criar um instantâneo da memória do sistema em execução.

    - Use o recurso de instantâneo dos serviços do Azure ou a funcionalidade do seu software para criar instantâneos dos sistemas em execução.

O Azure Sentinel fornece ampla análise de dados em praticamente qualquer origem de log e um portal de gerenciamento de casos para gerenciar o ciclo de vida completo de incidentes. As informações de inteligência coletadas durante uma investigação podem ser associadas a um incidente para fins de rastreamento e relatório. 

- [Criar um instantâneo de disco de um computador Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de disco de um computador Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretriz**: forneça aos analistas o contexto dos incidentes nos quais eles devem se concentrar primeiro com base na severidade do alerta e na confidencialidade do ativo. 

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretriz**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação de fluxo de trabalho na Central de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gerenciamento de postura e vulnerabilidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: estabelecer configurações seguras para recursos de computação

**Diretrizes**: Use a central de segurança do Azure e Azure Policy para estabelecer configurações seguras em todos os recursos de computação, incluindo VMS, contêineres e outros.

- [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="backup-and-recovery"></a>Backup e recuperação

*Para obter mais informações, confira o [Azure Security Benchmark: backup e recuperação](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantir backups automatizados regulares

**Orientação**: como o cache do HPC do Azure é uma solução de cache e não um sistema de armazenamento, concentre-se em garantir que os dados em seus destinos de armazenamento sejam regularmente armazenados em backup. Siga os procedimentos padrão para contêineres de blob do Azure e para fazer backup de qualquer destino de armazenamento local. 

Para minimizar a interrupção no caso de uma interrupção regional, você pode tomar medidas para garantir o acesso a dados entre regiões.  

Cada instância de cache do Azure HPC é executada em uma assinatura específica e em uma região. Isso significa que o fluxo de trabalho do cache poderia possivelmente ser interrompido se a região tiver uma interrupção completa. Para minimizar essa interrupção, a organização deve usar o armazenamento de back-end que pode ser acessado de várias regiões. Esse armazenamento pode ser um sistema NAS local com suporte DNS apropriado ou o armazenamento de BLOBs do Azure que reside em uma região diferente do cache.

Conforme o fluxo de trabalho continua em sua região primária, os dados são salvos no armazenamento de longo prazo fora da região. Se a região de cache ficar indisponível, você poderá criar uma instância duplicada de cache do Azure HPC em uma região secundária, conectar-se ao mesmo armazenamento e retomar o trabalho do novo cache.

- [Leia mais sobre o failover regional](hpc-region-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: criptografar dados de backup

**Diretrizes**: Verifique se os backups estão protegidos contra ataques. Isso deve incluir a criptografia dos backups para proteger contra perda de confidencialidade.

Para o backup local usando o backup do Azure, a criptografia em repouso é fornecida usando a senha que você fornecer. Para backup de serviço do Azure regular, os dados de backup são criptografados automaticamente usando chaves gerenciadas pela plataforma do Azure. Você pode optar por criptografar o backup usando chaves gerenciadas pelo cliente. Nesse caso, verifique se essa chave gerenciada pelo cliente no cofre de chaves também está no escopo de backup.

O cache HPC do Azure também é protegido pela criptografia do host da VM nos discos gerenciados que mantêm os dados armazenados em cache, mesmo que você adicione uma chave do cliente aos discos de cache. Adicionar uma chave gerenciada pelo cliente para criptografia dupla fornece um nível extra de segurança para clientes com necessidades de alta segurança. Leia criptografia no servidor do armazenamento em disco do Azure para obter detalhes.

Use o controle de acesso baseado em função no backup do Azure, Azure Key Vault ou outros recursos para proteger backups e chaves gerenciadas pelo cliente. Além disso, você pode habilitar recursos de segurança avançados para exigir MFA antes que os backups possam ser alterados ou excluídos.

- [Criptografia do host da VM](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Criptografia do servidor do armazenamento em disco do Azure](../virtual-machines/disk-encryption.md)

- [Visão geral dos recursos de segurança no Backup do Azure](../backup/security-overview.md) 

- [Criptografia de dados de backup usando chaves gerenciadas pelo cliente](../backup/encryption-at-rest-with-cmk.md)  

- [Como fazer backup de chaves Key Vault no Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: Verifique periodicamente se você pode restaurar as chaves gerenciadas pelo cliente de backup.

- [Como restaurar chaves de Key Vault no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-5.1.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: reduzir o risco de perda de chaves

**Diretrizes**: Verifique se você tem medidas em vigor para evitar e recuperar da perda de chaves. Habilite a exclusão temporária e a proteção de limpeza no Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos 

**Diretriz**: documente e comunique uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança dos riscos e do inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio do ciclo de vida

-   Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

-   Uso de funcionalidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definir uma estratégia de segmentação corporativa 

**Diretriz**: estabeleça uma estratégia de toda a empresa para segmentar o acesso aos ativos usando uma combinação de identidade, rede, aplicativo, assinatura, grupo de gerenciamento e outros controles.

Equilibre cuidadosamente a necessidade de separação de segurança com a necessidade de habilitação da operação diária dos sistemas que precisam se comunicar entre si e acessar dados.

Garanta que a estratégia de segmentação seja implementada de maneira consistente nos tipos de controle, incluindo segurança de rede, modelos de identidade e acesso, além de modelos de acesso/permissão de aplicativo e controles de processos humanos.

- [Diretrizes sobre a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Diretrizes sobre a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação corporativa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definir uma estratégia de gerenciamento de postura de segurança

**Diretriz**: Meça e atenue continuamente os riscos para seus ativos individuais e o ambiente no qual eles estão hospedados. Priorize os ativos de alto valor e as superfícies de ataque altamente expostas, como aplicativos publicados, pontos de entrada e saída de rede, pontos de extremidade de usuário e administrador etc.

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: alinhar funções e responsabilidades da organização

**Diretriz**: documente e comunique uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorize o fornecimento de responsabilidade clara por decisões de segurança, educando todos no modelo de responsabilidade compartilhada e instrua equipes técnicas sobre a tecnologia usada para proteger a nuvem.

- [Melhor prática de segurança do Azure 1 – Pessoas: educar as equipes na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor prática de segurança do Azure 2 – Pessoas: educar as equipes na tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor prática de segurança do Azure 3 – Processo: atribuir responsabilidade por decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definir uma estratégia de segurança de rede

**Diretriz**: estabeleça uma abordagem de segurança de rede do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Gerenciamento de rede centralizado e responsabilidade pela segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação corporativa

-   Estratégia de correção em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída e borda da Internet

-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](../security/benchmarks/security-controls-v2-network-security.md)

- [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede corporativa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definir uma estratégia de identidade e acesso privilegiado

**Diretriz**: estabeleça abordagens de identidade e acesso privilegiado do Azure como parte da estratégia geral de controle de acesso de segurança da sua organização.  

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Um sistema de identidade e autenticação centralizado e a interconectividade dele com outros sistemas de identidade internos e externos

-   Métodos de autenticação forte em diferentes casos de uso e condições

-   Proteção de usuários altamente privilegiados

-   Monitoramento e tratamento de atividades de usuário de anomalias  

-   Processo de revisão de acesso e reconciliação e identidade do usuário

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../security/fundamentals/identity-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definir uma estratégia de log e resposta a ameaças

**Diretriz**: estabeleça uma estratégia de log e resposta a ameaças para detectar e corrigir as ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências perfeitas aos analistas para que eles possam se concentrar em ameaças em vez de etapas manuais e de integração. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   A função e as responsabilidades da organização de SecOps (operações de segurança) 

-   Um processo de resposta a incidentes bem definido, alinhando-se ao NIST ou a outra estrutura do setor 

-   Captura e retenção de log para dar suporte às necessidades de conformidade, detecção de ameaças e resposta a incidentes

-   Visibilidade centralizada das ameaças e informações de correlação sobre elas por meio do SIEM, de funcionalidades nativas do Azure e de outras fontes 

-   Plano de comunicação e notificação com seus clientes, fornecedores e partes públicas interessadas

-   Uso de plataformas nativas e de terceiros do Azure para tratamento de incidentes, como log e detecção de ameaças, análise forense, além de correção e erradicação de ataques

-   Processos para tratamento de incidentes e atividades pós-incidente, como lições aprendidas e retenção de evidências

Para saber mais, consulte as referências a seguir:

- [Azure Security Benchmark – Log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)