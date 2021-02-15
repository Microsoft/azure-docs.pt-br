---
title: Linha de base de segurança do Azure para área de trabalho virtual do Windows
description: A linha de base de segurança de área de trabalho virtual do Windows fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 01cd79afe3e718a32663544a76a2a43353bc082e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575374"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linha de base de segurança do Azure para área de trabalho virtual do Windows

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 2,0](../security/benchmarks/overview.md) para área de trabalho virtual do Windows. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à área de trabalho virtual do Windows. Os **controles** não aplicáveis à área de trabalho virtual do Windows foram excluídos.

O serviço de área de trabalho virtual do Windows inclui o próprio serviço, o Windows 10 Enterprise para SKU virtual de várias sessões, bem como FSLogix. Para obter recomendações de segurança relacionadas ao FSLogix, consulte a [linha de base de segurança para armazenamento](../storage/common/security-baseline.md). O serviço tem um agente em execução em máquinas virtuais, mas como as máquinas virtuais estão sob controle total do cliente, siga as [recomendações de segurança para computação](../virtual-machines/windows/security-baseline.md)

Para ver como a área de trabalho virtual do Windows é totalmente mapeada para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança de desktop virtual](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: você deve criar ou usar uma rede virtual existente ao implantar máquinas virtuais a serem registradas na área de trabalho virtual do Windows. Verifique se todas as redes virtuais do Azure seguem um princípio de segmentação empresarial que se alinha aos riscos de negócios. Qualquer sistema que possa incorrer em um risco maior para a organização deve ser isolado em sua própria rede virtual e suficientemente protegido com um grupo de segurança de rede ou o Firewall do Azure.

Use os recursos de proteção de rede adaptável na central de segurança do Azure para recomendar configurações de grupo de segurança de rede que limitam portas e IPs de origem com referência a regras de tráfego de rede externa.

Com base em seus aplicativos e estratégia de segmentação corporativa, restrinja ou permita o tráfego entre os recursos internos com base nas regras do grupo de segurança de rede. Para aplicativos específicos bem definidos (como um aplicativo de três camadas), isso pode ser uma abordagem altamente segura de "negar por padrão, permitir por exceção". Isso pode não ser bem dimensionado se você tiver muitos aplicativos e pontos de extremidade interagindo entre si. Você também pode usar o Firewall do Azure em circunstâncias em que o gerenciamento central é necessário em um grande número de segmentos corporativos ou spokes (em uma topologia hub/spoke)

Para os grupos de segurança de rede associados à sua máquina virtual (que fazem parte das sub-redes da área de trabalho virtual do Windows), você deve permitir o tráfego de saída para pontos de extremidade específicos. 

- [Descubra quais URLs devem ter permissão de acesso para a área de trabalho virtual do Windows](safe-url-list.md)

- [Proteção de rede adaptável na central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md) 

- [Firewall do Azure para área de trabalho virtual do Windows ](../firewall/protect-windows-virtual-desktop.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

**Diretrizes**: Use o Azure ExpressRoute ou a rede virtual privada do Azure para criar conexões privadas entre os datacenters do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública, oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. 

Para redes virtuais privadas de ponto a site e site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação de opções de rede virtual privada e do Azure ExpressRoute.

Use o emparelhamento de rede virtual para conectar duas ou mais redes virtuais juntas no Azure. O tráfego de rede entre redes virtuais emparelhadas é privado e permanece na rede de backbone do Azure.

- [Quais são os modelos de conectividade do ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Visão geral da VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Emparelhamento de rede virtual](/azure/virtual-network/virtual-network-peering-overview)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Orientação**: Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. Proteja seus recursos de área de trabalho virtual do Windows contra ataques de redes externas, incluindo ataques de negação de serviço distribuídos, ataques específicos de aplicativo, tráfego de Internet não solicitado e potencialmente mal-intencionado. Proteja seus ativos contra ataques de negação de serviço distribuídos, habilitando a proteção padrão de DDoS em suas redes virtuais do Azure. Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados aos recursos relacionados à rede.

A área de trabalho virtual do Windows não se destina a executar aplicativos Web e não exige que você defina configurações adicionais ou implante quaisquer serviços de rede extras para protegê-los contra ataques de rede externos direcionados a aplicativos Web.

- [Documentação do firewall do Azure](/azure/firewall)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](/azure/virtual-network/manage-ddos-protection) 

- [Recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md#networking-recommendations)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Diretrizes**: Use o Firewall do Azure com filtragem baseada em inteligência contra ameaças para alertar e, opcionalmente, bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. Quando a inspeção de carga é necessária, você pode implantar uma solução de prevenção ou detecção de intrusão de terceiros no Azure Marketplace. 

Se você tiver uma regulamentação ou outro requisito para detecção de intrusão ou uso da solução de prevenção, verifique se ele sempre está ajustado para fornecer alertas de alta qualidade para sua solução SIEM (gerenciamento de eventos e informações de segurança).

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [O Azure Marketplace inclui recursos de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Recurso EDR do Microsoft defender ATP](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: Use as marcas de serviço de rede virtual do Azure para definir os controles de acesso à rede em grupos de segurança de rede ou um firewall do Azure configurado para seus recursos de área de trabalho virtual do Windows. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo: WindowsVirtualDesktop) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Saiba mais sobre o que é coberto pela marca de serviço de área de trabalho virtual do Windows aqui ](safe-url-list.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: a área de trabalho virtual do Windows usa Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Você deve padronizar o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.

- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar a postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

O Azure AD dá suporte a identidades externas que permitem aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa.

- [Locação no Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Usar provedores de identidade externos para o aplicativo](/azure/active-directory/b2b/identity-providers)

- [O que é a classificação de segurança de identidade do Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Funções específicas que você precisa para operar a área de trabalho virtual do Windows ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: a área de trabalho virtual do Windows dá suporte a identidades gerenciadas do Azure para contas não humanas, como serviços ou automação. É recomendável usar o recurso de identidade gerenciada do Azure em vez de criar uma conta humana mais poderosa para acessar ou executar seus recursos. 

A área de trabalho virtual do Windows recomenda usar o Azure Active Directory (Azure AD) para criar uma entidade de serviço com permissões restritas no nível de recurso para configurar entidades de serviço com credenciais de certificado e fazer fallback para os segredos do cliente. Em ambos os casos, Azure Key Vault pode ser usada em conjunto com as identidades gerenciadas do Azure, para que o ambiente de tempo de execução (como, uma função do Azure) possa recuperar a credencial do cofre de chaves.

- [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Criar uma entidade de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Usar Azure Key Vault para o registro de entidade de segurança](../key-vault/general/authentication.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: a área de trabalho virtual do Windows usa Azure Active Directory (Azure AD) para fornecer gerenciamento de acesso e identidade para recursos do Azure, aplicativos de nuvem e aplicativos locais. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros e fornecedores. Isso permite que o SSO (logon único) gerencie e proteja o acesso aos dados e aos recursos da sua organização no local e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso seguro contínuo com maior visibilidade e controle.

- [Noções básicas sobre o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory 

**Diretrizes**: a área de trabalho virtual do Windows usa o Azure Active Directory (Azure AD), que dá suporte a controles de autenticação fortes por meio de autenticação multifator e métodos fortes de senha.

- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de identidade e acesso da central de segurança do Azure para obter algumas práticas recomendadas na configuração de autenticação multifator. A autenticação multifator pode ser imposta em todos, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.

- Autenticação sem senha: três opções de autenticação sem senha estão disponíveis: Windows Hello para Empresas, aplicativo Microsoft Authenticator e métodos de autenticação locais, como cartões inteligentes.

A área de trabalho virtual do Windows dá suporte à autenticação baseada em senha herdada, como contas somente em nuvem (contas de usuário criadas diretamente no Azure) que têm uma política de senha de linha de base ou contas híbridas (contas de usuário do Azure AD local que seguem as políticas de senha locais). Ao usar a autenticação baseada em senha, o Azure AD fornece um recurso de proteção de senha que impede que os usuários definam senhas que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas banidas que são atualizadas com base na telemetria, e os clientes podem aumentar a lista com base em suas necessidades (como identidade visual, referências culturais e assim por diante). Essa proteção por senha pode ser usada para contas híbridas e somente em nuvem.

A autenticação baseada em credenciais de senha sozinhas é suscetível a métodos de ataque populares. Para maior segurança, use autenticação forte, como autenticação multifator e uma política de senha forte. Para aplicativos de terceiros e serviços do Marketplace que podem ter senhas padrão, você deve alterá-los na configuração inicial do serviço.

Para administradores e usuários privilegiados, verifique se o nível mais alto de métodos de autenticação forte são usados, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Política de senha padrão do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminar senhas ruins usando Azure Active Directory proteção por senha](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: a área de trabalho virtual do Windows é integrada ao Azure Active Directory (AD do Azure) que fornece as seguintes fontes de dados:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entrada arriscada-uma entrada arriscada é um indicador para uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM (gerenciamento de eventos e informações de segurança) de terceiros. A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. O ATP (Proteção Avançada contra Ameaças) do Azure é uma solução de segurança que pode usar os sinais do Active Directory para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de membros mal-intencionados.

- [Relatórios de atividade de auditoria no Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Alertas do módulo de proteção da inteligência contra ameaças da Central de Segurança do Azure](../security-center/alerts-reference.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: a área de trabalho virtual do Windows dá suporte ao acesso condicional com o Azure Active Directory (AD do Azure) para um controle de acesso granular com base em condições definidas pelo usuário. Por exemplo, os logons de usuário de determinados intervalos de IP podem ser necessários para usar a autenticação multifator para acesso. 

Além disso, a política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md) 

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [As informações de configuração de acesso condicional específicas da área de trabalho virtual do Windows podem ser encontradas aqui](set-up-mfa.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: a área de trabalho virtual do Windows usa o controle de acesso baseado em função do Azure (RBAC do Azure) para isolar o acesso a sistemas críticos para os negócios. Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory, ferramentas de segurança e ferramentas de gerenciamento de sistema com agentes instalados em sistemas críticos para os negócios. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem causar um risco imediato de comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Permissões de administrador mínimas necessárias para gerenciar a área de trabalho virtual do Windows](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: a área de trabalho virtual do Windows usa contas do Azure Active Directory (Azure AD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos.

Use as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas.

Além disso, o Azure Privileged Identity Management também pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar as contas de administrador que estão obsoletas ou configuradas incorretamente.

Alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

- [Funções internas para área de trabalho virtual do Windows](rbac.md)

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: a área de trabalho virtual do Windows usa o Azure Active Directory (AD do Azure) para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: a área de trabalho virtual do Windows é integrada ao Azure Active Directory (AD do Azure) para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expirações. Também há suporte para as aprovações adicionais, duplas ou de vários estágios.

- [O que são revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é o gerenciamento de direitos do AD do Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviço críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas. 

Use o Azure Active Directory (Azure AD), a ATP (proteção avançada contra ameaças) do Microsoft defender ou Microsoft Intune para implantar uma estação de trabalho de usuário segura e gerenciada para tarefas administrativas. A estação de trabalho protegida pode ser gerenciada centralmente para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implantar uma estação de trabalho com acesso privilegiado](/azure/active-directory/devices/howto-azure-managed-workstation)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Diretrizes**: a área de trabalho virtual do Windows é integrada com o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. 

Os privilégios que você atribui a recursos com o RBAC do Azure devem ser sempre limitados àqueles conforme exigido pelas funções. Isso complementa a abordagem JIT (just in time) do Privileged Identity Management (PIM), com Azure Active Directory (Azure AD) e deve ser revisado periodicamente.

Além disso, use funções internas para alocar permissões e apenas criar funções personalizadas quando necessário.

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) 

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Funções internas para área de trabalho virtual do Windows](rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: escolha o processo de aprovação para o suporte da Microsoft  

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar dados do cliente, a área de trabalho virtual do Windows dá suporte a sistema de proteção de dados do cliente para fornecer uma interface para você revisar e aprovar ou rejeitar solicitações de acesso a dados do cliente.

- [Entender Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: descobrir, classificar e rotular dados confidenciais

**Orientação**: descubra, classifique e rotule seus dados confidenciais para que você possa criar os controles apropriados. Isso é para garantir que as informações confidenciais sejam armazenadas, processadas e transmitidas com segurança pelos sistemas de tecnologia da organização.

Use a proteção de informações do Azure (e sua ferramenta de verificação associada) para obter informações confidenciais em documentos do Office no Azure, no local, no Office 365 e em outros locais.

Use a Proteção de Informações do SQL do Azure para auxiliar na classificação e na rotulagem das informações armazenadas em Bancos de Dados SQL do Azure.

- [Marcar informações confidenciais usando a Proteção de Informações do Azure](/azure/information-protection/what-is-information-protection) 

- [Como implementar a descoberta de dados do SQL do Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: proteja dados confidenciais restringindo o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure), controles de acesso baseados em rede e controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados).

Para garantir um controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa. A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

A Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados.

- [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/overview.md) 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: monitorar a transferência não autorizada de dados confidenciais

**Diretriz**: monitore a transferência não autorizada de dados para localizações fora da visibilidade e do controle da empresa. Isso normalmente envolve o monitoramento de atividades anormais (transferências grandes ou incomuns) que podem indicar exfiltração não autorizada dos dados.

Os recursos de ATP (proteção avançada contra ameaças) com o armazenamento do Azure e o SQL ATP do Azure podem alertar sobre a transferência anômala de informações, indicando quais podem ser transferências não autorizadas de informações confidenciais.

A AIP (Proteção de Informações do Azure) fornece funcionalidades de monitoramento para informações que foram classificadas e rotuladas.

Use soluções de prevenção de perda de dados, como as baseadas em host, para impor a detecção e/ou os controles preventivos para evitar vazamento de dados.

- [Habilitar o ATP do SQL do Azure](../azure-sql/database/threat-detection-overview.md) 

- [Habilitar o ATP do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento de riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização. 

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas. 

Permissões adicionais podem ser necessárias para visibilidade em cargas de trabalho e serviços. 

- [Visão geral da função Leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

Use o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre o software em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, habilite o diagnóstico de nível de convidado e traga os logs de eventos do Windows para um espaço de trabalho Log Analytics.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md) 

- [Guia de decisão de marcação e nomenclatura de recurso](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretrizes**: Não aplicável. A área de trabalho virtual do Windows não pode ser usada para garantir a segurança de ativos em um processo de gerenciamento do ciclo de vida. É responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados de alto impacto. 

É recomendável que o cliente crie um processo para capturar as alterações de configuração de rede e atributo, meça o impacto de alterações e crie tarefas de correção, conforme aplicável.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: usar somente aplicativos aprovados em recursos de computação

**Diretrizes**: Use o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, habilite o diagnóstico de nível de convidado e traga os logs de eventos do Windows para um espaço de trabalho Log Analytics.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Orientação**: Use o recurso de detecção de ameaças interna da central de segurança do Azure e habilite o Azure defender (proteção avançada contra ameaças do Azure) para seus recursos de área de trabalho virtual do Windows. O Azure defender para área de trabalho virtual do Windows fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seus recursos de área de trabalho virtual do Windows.

Encaminhe todos os logs da área de trabalho virtual do Windows para a solução SIEM (gerenciamento de eventos de informações de segurança) que pode ser usada para configurar as detecções de ameaças personalizadas. Verifique se você está monitorando diferentes tipos de ativos do Azure para possíveis ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir os falsos positivos para os analistas classificarem. Os alertas podem ser originados de dados de log, agentes ou outros dados.

- [Proteção contra ameaças na Central de Segurança do Azure](/azure/security-center/threat-protection) 

- [Guia de referência de alertas de segurança da central de segurança do Azure](../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detectar ameaças](../sentinel/tutorial-detect-threats-custom.md) 

- [Inteligência contra ameaças cibernéticos com o Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Diretrizes**: o Azure Active Directory (Azure AD) fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure ad ou integrados com o Azure monitor, o Azure Sentinel ou outras ferramentas de monitoramento e de gerenciamento de eventos e informações de segurança para obter mais casos de uso de monitoramento e análise mais sofisticados:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entrada arriscada-uma entrada arriscada é um indicador para uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo proteção contra ameaças na central de segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (banco de dados SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade em anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças na Central de Segurança do Azure](/azure/security-center/threat-protection)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Diretrizes**: a área de trabalho virtual do Windows não produz ou processa logs de consulta do serviço de nomes de domínio (DNS). No entanto, os recursos registrados para o serviço podem produzir logs de fluxo.

Habilitar e coletar logs de fluxo e recursos de grupo de segurança de rede, logs de firewall do Azure e WAF (firewall de aplicativo Web) para análise de segurança para dar suporte a investigações de incidentes, busca de ameaças e geração de alertas de segurança. Você pode enviar os logs de fluxo para um espaço de trabalho Azure Monitor Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Logs e métricas do Firewall do Azure](/azure/firewall/logs-and-metrics) 

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md) 

- [Soluções de monitoramento de rede do Azure no Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que são habilitados automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos de área de trabalho virtual do Windows, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, as ferramentas usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos de informações de segurança) de terceiros. Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Diretriz**: verifique se a sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está os exercendo regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

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

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

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

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: estabelecer configurações seguras para recursos de computação

**Diretrizes**: Use a central de segurança do Azure e Azure Policy para estabelecer configurações seguras em todos os recursos de computação, incluindo VMS, contêineres e outros.

Você pode usar imagens personalizadas do sistema operacional ou a configuração de estado da automação do Azure para estabelecer a configuração de segurança do sistema operacional exigido pela sua organização.

- [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md) 

- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md) 

- [Ambiente da Área de Trabalho Virtual do Windows](environment-setup.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: sustentar configurações seguras para recursos de computação

**Diretrizes**: Use a central de segurança do azure e Azure Policy para avaliar e corrigir regularmente os riscos de configuração em seus recursos de computação do Azure, incluindo máquinas virtuais, contêineres e outros. Além disso, você pode usar modelos de Azure Resource Manager, imagens do sistema operacional personalizado ou configuração de estado da automação do Azure para manter a configuração de segurança do sistema operacional exigido pela sua organização. Os modelos de máquina virtual da Microsoft combinados com a configuração de estado de automação do Azure podem ajudar a atender e a manter os requisitos de segurança.

As imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são gerenciadas e mantidas pela Microsoft.

A central de segurança do Azure também pode verificar vulnerabilidades na imagem de contêiner e executa o monitoramento contínuo da configuração do Docker em contêineres com relação ao benchmark do Docker da segurança da Internet da central. Você pode usar a página de recomendações da central de segurança do Azure para exibir recomendações e corrigir problemas.

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Como criar uma máquina virtual do Azure por meio de um modelo do ARM](../virtual-machines/windows/ps-template.md) 

- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md) 

- [Segurança de contêineres na Central de Segurança](../security-center/container-security.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: armazenar com segurança imagens de contêiner e do sistema operacional personalizado

**Diretrizes**: a área de trabalho virtual do Windows permite que os clientes gerenciem imagens do sistema operacional. Use o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que somente usuários autorizados possam acessar suas imagens personalizadas. Use uma galeria de imagens compartilhadas do Azure você pode compartilhar suas imagens para diferentes usuários, entidades de serviço ou grupos de Active Directory em sua organização. Armazene imagens de contêiner no registro de contêiner do Azure e use o RBAC para garantir que somente usuários autorizados tenham acesso.

- [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Visão geral da Galeria de imagens compartilhadas](/azure/virtual-machines/windows/shared-image-galleries)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: executar avaliações de vulnerabilidade de software

**Diretrizes**: a área de trabalho virtual do Windows permite implantar suas próprias máquinas virtuais e registrá-las no serviço, bem como ter o banco de dados SQL em execução no ambiente.

A área de trabalho virtual do Windows pode usar uma solução de terceiros para executar avaliações de vulnerabilidade em dispositivos de rede e aplicativos Web. Ao realizar verificações remotas, não use uma única conta administrativa permanente. Considere a implementação da metodologia de provisionamento JIT para a conta de verificação. As credenciais para a conta de verificação devem ser protegidas, monitoradas e usadas somente para verificação de vulnerabilidade.

Conforme necessário, exporte os resultados da verificação em intervalos consistentes e compare os resultados com verificações anteriores para verificar se as vulnerabilidades foram corrigidas.

Siga as recomendações da central de segurança do Azure para executar avaliações de vulnerabilidade em suas máquinas virtuais do Azure (e servidores SQL). A central de segurança do Azure tem um verificador de vulnerabilidades interno para máquina virtual, imagens de contêiner e banco de dados SQL.

Conforme necessário, exporte os resultados da verificação em intervalos consistentes e compare os resultados com verificações anteriores para verificar se as vulnerabilidades foram corrigidas. Ao usar as recomendações de gerenciamento de vulnerabilidade sugeridas pela central de segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricas.

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Verificador de vulnerabilidades integrada para máquinas virtuais](/azure/security-center/built-in-vulnerability-assessment) 
- [Avaliação de vulnerabilidade do SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: corrigir de maneira rápida e automática as vulnerabilidades de software

**Diretrizes**: a área de trabalho virtual do Windows não usa ou requer software de terceiros. No entanto, a área de trabalho virtual do Windows permite que você implante suas próprias máquinas virtuais e registre-as no serviço.

Use a Gerenciamento de Atualizações de automação do Azure ou uma solução de terceiros para garantir que as atualizações de segurança mais recentes sejam instaladas nas máquinas virtuais do Windows Server. Para máquinas virtuais do Windows, verifique se Windows Update foi habilitado e definido para atualizar automaticamente.

Use uma solução de gerenciamento de patches de terceiros para software de terceiros ou System Center Updates Publisher para Configuration Manager.

- [Como configurar Gerenciamento de Atualizações para máquinas virtuais no Azure](/azure/automation/update-management/overview) 

- [Gerenciar atualizações e patches para as VMs do Azure](/azure/automation/update-management/manage-updates-for-vm)

- [Configurar o Microsoft Endpoint Configuration Manager para área de trabalho virtual do Windows](configure-automatic-updates.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretrizes**: a área de trabalho virtual do Windows não permite que os clientes realizem seus próprios testes de penetração em seus recursos de área de trabalho virtual do Windows.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="endpoint-security"></a>Segurança de ponto de extremidade

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança do ponto de extremidade](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usar a detecção e resposta de ponto de extremidade (EDR)

**Diretrizes**: a área de trabalho virtual do Windows não fornece recursos específicos para processos de resposta e detecção de ponto de extremidade (EDR). No entanto, os recursos registrados para o serviço podem se beneficiar de recursos de detecção e resposta de ponto de extremidade. 

Habilite a detecção de ponto de extremidade e os recursos de resposta para servidores e clientes e integre-os com soluções de SIEM (gerenciamento de eventos e informações de segurança) e processos de operações de segurança.

A proteção avançada contra ameaças do Microsoft defender fornece recursos de detecção e resposta de ponto de extremidade, como parte de uma plataforma de segurança de ponto de extremidade empresarial para prevenir, detectar, investigar e responder a ameaças avançadas.

- [Visão geral da proteção avançada contra ameaças do Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Serviço Microsoft defender ATP para Windows Servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Serviço Microsoft defender ATP para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft defender ATP para infraestrutura de área de trabalho virtual não persistente](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: usar software antimalware moderno gerenciado centralmente

**Diretrizes**: Proteja seus recursos de área de trabalho virtual do Windows com uma solução antimalware de ponto de extremidade moderno e gerenciada centralmente capaz de verificação periódica e em tempo real.

A central de segurança do Azure pode identificar automaticamente o uso de várias soluções Antimalware populares para suas máquinas virtuais e relatar o status de execução do Endpoint Protection e fazer recomendações.

O Microsoft antimalware para serviços de nuvem do Azure é o antimalware padrão para VMs (máquinas virtuais) do Windows. Além disso, você pode usar a detecção de ameaças com a central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento do Azure.

- [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md) 

- [Soluções de proteção de ponto de extremidade com suporte](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: garantir que o software anti-malware e as assinaturas sejam atualizados

**Diretrizes**: Verifique se as assinaturas Antimalware são atualizadas de forma rápida e consistente.

Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todas as máquinas virtuais e/ou contêineres estejam atualizados com as assinaturas mais recentes.

O Microsoft Antimalware instalará automaticamente as assinaturas e atualizações de mecanismo mais recentes por padrão.

- [Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure](../security/fundamentals/antimalware.md) 

- [Avaliação e recomendações do Endpoint Protection na central de segurança do Azure](../security-center/security-center-endpoint-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="backup-and-recovery"></a>Backup e recuperação

*Para obter mais informações, confira o [Azure Security Benchmark: backup e recuperação](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantir backups automatizados regulares

**Diretrizes**: Verifique se você está fazendo backup de sistemas e dados para manter a continuidade dos negócios após um evento inesperado. Isso deve ser orientado por quaisquer objetivos de RPO (objetivo de ponto de recuperação) e RTO (objetivo de tempo de recuperação).

Habilite o backup do Azure e configure a fonte de backup (por exemplo, VMs do Azure, SQL Server, bancos de dados HANA ou compartilhamentos de arquivos), bem como a frequência e o período de retenção desejados.

Para um nível mais alto de redundância, você pode habilitar a opção de armazenamento com redundância geográfica para replicar dados de backup para uma região secundária e recuperar usando a restauração entre regiões.

- [Continuidade dos negócios e recuperação de desastres de escala empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Como habilitar o backup do Azure](/azure/backup/) 

- [Como habilitar a restauração entre regiões](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Como configurar um plano de continuidade de negócios e recuperação de desastres na área de trabalho virtual do Windows](disaster-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: criptografar dados de backup

**Diretrizes**: Verifique se os seus backups estão protegidos contra ataques. Isso deve incluir a criptografia dos backups para proteger contra perda de confidencialidade.

Para backup de serviço do Azure regular, os dados de backup são criptografados automaticamente usando chaves gerenciadas pela plataforma do Azure. Você pode optar por criptografar o backup usando a chave gerenciada pelo cliente. Nesse caso, verifique se essa chave gerenciada pelo cliente no cofre de chaves também está no escopo de backup.

Use o controle de acesso baseado em função no backup do Azure, Azure Key Vault ou outros recursos para proteger backups e chaves gerenciadas pelo cliente. Além disso, você pode habilitar recursos de segurança avançados para exigir autenticação multifator antes que os backups possam ser alterados ou excluídos.

Visão geral dos recursos de segurança no backup do Azure/Azure/backup/Security-Overview 

- [Criptografia de dados de backup usando chaves gerenciadas pelo cliente](/azure/backup/encryption-at-rest-with-cmk) 

- [Como fazer backup de chaves de Key Vault no Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Recursos de segurança para ajudar a proteger backups híbridos contra ataques](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: é recomendável validar a integridade dos dados na mídia de backup regularmente executando um processo de restauração de dados para garantir que o backup esteja funcionando corretamente.

- [Como recuperar arquivos do backup de máquina virtual do Azure](/azure/backup/backup-azure-restore-files-from-vm)

- [Implementação de segurança](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definir a estratégia de proteção de dados e gerenciamento de ativos 

**Diretriz**: documente e comunique uma estratégia clara para o monitoramento contínuo e a proteção de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o monitoramento de sistemas e dados comercialmente críticos. 

Essa estratégia deve incluir diretrizes documentadas, políticas e padrões para os seguintes elementos: 

-   Padrão de classificação de dados de acordo com os riscos de negócios

-   Visibilidade da organização de segurança dos riscos e do inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure para uso 

-   Segurança de ativos por meio do ciclo de vida

-   Estratégia de controle de acesso obrigatório de acordo com a classificação de dados organizacionais

-   Uso de funcionalidades nativas e de terceiros de proteção de dados do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Para saber mais, consulte as referências a seguir:
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark – Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark – Segurança de rede](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure Security Benchmark – Gerenciamento de identidades](/azure/automation/update-management/overview)

- [Azure Security Benchmark – Acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Azure Security Benchmark – Log e detecção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark – Resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
