---
title: Linha de base de segurança do Azure para serviço de Signaler do Azure
description: A linha de base de segurança de serviço do Azure Signalr fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3a8c0139d5739fbb75e6f6a157e80287864aeac8
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094973"
---
# <a name="azure-security-baseline-for-azure-signalr-service"></a>Linha de base de segurança do Azure para serviço de Signaler do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 2,0](../security/benchmarks/overview.md) para o Signalr do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Signalr do Azure. Os **controles** não aplicáveis ao Signalr do Azure foram excluídos.

 
Para ver como o Azure Signalr é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança do signaler do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: Microsoft Azure serviço signalr não dá suporte à implantação diretamente em uma rede virtual, por isso, você não pode aproveitar determinados recursos de rede com os recursos da oferta, como grupos de segurança de rede, tabelas de rotas ou outros dispositivos dependentes de rede, como um firewall do Azure. 

O serviço de Signalr do Azure, no entanto, permite que você crie pontos de extremidade privados para proteger o tráfego entre os recursos em sua rede virtual e o serviço de Signaler do Azure.

Você também pode usar marcas de serviço e configurar regras de grupo de segurança de rede para restringir o tráfego de entrada/saída para o serviço do Azure Signalr.

- [Usar pontos de extremidade privados para o serviço de Signaler do Azure](howto-private-endpoints.md)

- [Configurar o controle de acesso à rede](howto-network-access-control.md)

- [Usar marcas de serviço para o serviço de Signaler do Azure](howto-service-tags.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-2---connect-private-networks-together"></a>NS-2:-conectar redes privadas juntas  

**Orientação**: Use pontos de extremidade privados para proteger o tráfego entre sua rede virtual e o serviço de Signaler do Azure. Escolha o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os data centers do Azure e a infraestrutura local em um ambiente de colocação. 

As conexões do ExpressRoute não passam pela Internet pública e oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. Para VPN ponto a site e VPN site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e do Azure ExpressRoute.

Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [Usar pontos de extremidade privados para o serviço de Signaler do Azure](howto-private-endpoints.md)

- [Quais são os modelos de conectividade do ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Visão geral da VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer o acesso de rede privada aos serviços do Azure

**Orientação**: Use o link privado do Azure para habilitar o acesso privado ao serviço de signaler do Azure de suas redes virtuais sem cruzar a Internet.

O acesso privado é uma medida adicional de defesa profunda, além da segurança de autenticação e tráfego oferecida pelos serviços do Azure.

- [Entender o link privado do Azure](../private-link/private-link-overview.md)

- [Usar pontos de extremidade privados para o serviço de Signaler do Azure](howto-private-endpoints.md)

- [Configurar o controle de acesso à rede](howto-network-access-control.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: Use as marcas de serviço de rede virtual do Azure para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure configurados para seus recursos de serviço do signaler Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo: AzureSignalR) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Usar marcas de serviço para o serviço de Signaler do Azure](howto-service-tags.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: o serviço de signaler do azure usa Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Você deve padronizar o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:

- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.
- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

O serviço de Signaler do Azure dá suporte apenas à autenticação do Azure AD para o plano de gerenciamento, mas não para o plano de dados. Aqui está a lista de funções internas no serviço de Signaler do Azure:

- Colaborador do signalr
- Leitor de AccessKey do signalr

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar a postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

O Azure AD dá suporte a identidades externas que permitem aos usuários sem um conta Microsoft entrar em seus aplicativos e recursos com sua identidade externa.

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Usar provedores de identidade externos para o aplicativo](../active-directory/external-identities/identity-providers.md)

- [O que é a classificação de segurança de identidade no Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: o serviço do Azure signalr usa identidades gerenciadas do Azure para contas não humanas, como a que está chamando upstream em um cenário sem servidor. É recomendável usar o recurso de identidade gerenciada do Azure para acessar outros recursos do Azure. O serviço de sinalizador do Azure pode se autenticar nativamente nos serviços ou recursos do Azure que dão suporte à autenticação Azure Active Directory (AD do Azure) por meio da regra de concessão de acesso predefinida sem usar a credencial codificada no código-fonte ou nos arquivos de configuração.

- [Identidades gerenciadas do Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) 

- [Identidades gerenciadas para o serviço de Signaler do Azure](howto-use-managed-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: o serviço do Azure signalr usa Azure Active Directory (Azure AD) para fornecer gerenciamento de identidade e acesso aos recursos do signalr. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros e fornecedores. Isso permite que o logon único gerencie e proteja o acesso aos dados e recursos de sua organização localmente e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo e seguro, além de maior visibilidade e controle.

- [Noções básicas sobre o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Diretrizes**: o serviço de signaler do azure usa Azure Active Directory (Azure AD) que dá suporte a controles de autenticação fortes por meio da autenticação multifator e métodos fortes de senha.

- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para obter práticas recomendadas aplicáveis em sua configuração de autenticação multifator. A autenticação multifator pode ser imposta em todos os usuários selecionados ou no nível por usuário com base nas condições de entrada e nos fatores de risco.

- Autenticação sem senha: três opções de autenticação sem senha estão disponíveis: Windows Hello para Empresas, aplicativo Microsoft Authenticator e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, garanta o uso do nível mais alto do método de autenticação forte, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Política de senha padrão do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar senhas ruins usando Azure Active Directory proteção por senha](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: o serviço de signaler do Azure é integrado com Azure Active Directory no, que fornece as seguintes fontes de dados:

- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entrada arriscada-uma entrada arriscada é um indicador para uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.

- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM (gerenciamento de informações e eventos de segurança) de terceiros.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura.

O ATP (Proteção Avançada contra Ameaças) do Azure é uma solução de segurança que pode usar os sinais do Active Directory para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de membros mal-intencionados.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: o serviço de signaler do Azure dá suporte ao acesso condicional do Azure Active Directory (Azure AD) para um controle de acesso mais granular com base em condições definidas pelo usuário, como logons de usuário de determinados intervalos de IP precisarão usar a MFA para logon. A política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteger e limitar os usuários altamente privilegiados

**Orientação**: as funções internas mais críticas são Azure Active Directory (Azure AD) são o administrador global e o administrador de função com privilégios, pois os usuários atribuídos a essas duas funções podem delegar funções de administrador:

- Administrador global: os usuários com essa função têm acesso a todos os recursos administrativos no Azure AD, bem como a serviços que usam identidades do Azure AD.

- Administrador de função com privilégios: os usuários com essa função podem gerenciar atribuições de função no Azure Active Directory (Azure AD), bem como em Azure AD Privileged Identity Management (PIM). Além disso, essa função permite o gerenciamento de todos os aspectos do PIM e das unidades administrativas.

O serviço de Signaler do Azure tem funções internas altamente privilegiadas. Limite o número de contas ou funções altamente privilegiadas e proteja as contas do Azure AD em um nível elevado, pois os usuários com esse privilégio podem ler e modificar de forma direta ou indireta todos os recursos em seu ambiente do Azure.

Você pode habilitar o acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD usando o Azure AD PIM (Privileged Identity Management). O JIT concede permissões temporárias para executar tarefas privilegiadas somente quando os usuários precisam dela. O PIM também pode gerar alertas de segurança quando há atividades suspeitas ou não seguras na sua organização do Azure AD.

- [Colaborador do signalr](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Permissões da função Administrador no Azure AD](../active-directory/roles/permissions-reference.md)

- [Usar os alertas de segurança do Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../active-directory/roles/security-planning.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: o serviço de signaler do Azure usa o Azure RBAC (controle de acesso baseado em função) para isolar o acesso a sistemas críticos de negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais eles estão.

Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory, ferramentas de segurança e ferramentas de gerenciamento de sistema com agentes instalados em sistemas de negócios críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Orientação**: revise as contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seus níveis de acesso sejam válidos.

O serviço de sinalizador do Azure usa contas do Azure Active Directory (Azure AD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos. Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

As funções internas no serviço de Signaler do Azure incluem:

- Colaborador do signalr
- Leitor de AccessKey do signalr

Além disso, o Azure Privileged Identity Management também pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar as contas de administrador que estão obsoletas ou configuradas incorretamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Colaborador do signalr](../role-based-access-control/built-in-roles.md#signalr-contributor)

- [Leitor de AccessKey do signalr](../role-based-access-control/built-in-roles.md#signalr-accesskey-reader)

-

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: o serviço de signaler do Azure usa o Azure Active Directory (Azure AD) para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../active-directory/roles/security-emergency-access.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: o serviço de signaler do Azure é integrado ao Azure Active Directory (Azure AD) para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.

- [O que são revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [O que é o gerenciamento de direitos do AD do Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas de modo central para impor a configuração protegida, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Orientação**: o serviço signalr é integrado ao Azure RBAC (controle de acesso baseado em função) para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Atribua essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Existem funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, Azure PowerShell ou portal do Azure. 

Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD PIM (Privileged Identity Management) e deve ser examinado periodicamente.

Funções internas no serviço Signalr:

- Colaborador do signalr
- Leitor de AccessKey do signalr

Use funções internas para alocar permissão e apenas para criar funções personalizadas quando necessário.

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: proteja dados confidenciais restringindo o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure), controles de acesso baseados em rede e controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados).

Para garantir um controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa. A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados.

- [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/overview.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

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

**Orientação**: aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md)

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretriz**: estabeleça ou atualize as políticas de segurança que atendem aos processos de gerenciamento do ciclo de vida de ativos em relação a modificações que, potencialmente, sejam de alto impacto. Essas modificações incluem alterações, mas não se limitam a: provedores de identidade e acesso, confidencialidade de dados, configuração de rede e atribuição de privilégio administrativo. No serviço de Signaler do Azure, essas alterações incluem: regenerar chave de acesso, criar/atualizar ponto de extremidade privado, gerenciar o controle de acesso à rede.

Remova os recursos do Azure quando eles não forem mais necessários.

- [Excluir o grupo de recursos e o recurso do Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Diretrizes**: Azure Active Directory (Azure AD) fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure ad ou integrados com Azure monitor, Azure Sentinel ou outras ferramentas de monitoramento/Siem para casos de uso de análise e monitoramento mais sofisticados:

- Entrar – o relatório Sign-n fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

- Entradas arriscadas-uma entrada arriscada é um indicador de uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de Proteção contra Ameaças da Central de Segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (BD SQL e armazenamento) e camadas de serviço do Azure. Essa funcionalidade permite que você tenha visibilidade das anomalias nas contas nos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Diretrizes**: o serviço do Azure signalr não se destina a implantar em redes virtuais, por isso você não pode habilitar o log de fluxo do grupo de segurança de rede, rotear o tráfego por meio de um firewall ou executar capturas de pacote.

No entanto, o serviço do Azure Signalr registra o tráfego de rede que ele processa para acesso do cliente. Habilite os logs de recursos em seus recursos de oferta implantados e configure esses logs para serem enviados a uma conta de armazenamento para retenção e auditoria de longo prazo.

- [Logs de recursos do serviço de Signaler do Azure](signalr-howto-diagnostic-logs.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que estão disponíveis automaticamente, contêm todas as operações de gravação (put, post, Delete) para os recursos do serviço Signaler do Azure, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

Habilite os logs de recursos do Azure para o serviço de sinalizador do Azure. Você pode usar a central de segurança do Azure e Azure Policy para habilitar logs de recursos e coleta de dados de log. Esses logs podem ser essenciais para investigar posteriormente os incidentes de segurança e executar exercícios forenses.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Logs de recursos do serviço de Signaler do Azure](signalr-howto-diagnostic-logs.md)

- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um sistema de gerenciamento de eventos e informações de segurança (SIEM) de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

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

**Diretriz**: verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade deles. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência dos últimos incidentes, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas pela combinação e pela correlação de fontes de sinal diversificadas. 

A Central de Segurança do Azure fornece alertas de alta qualidade em muitos ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação. 

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

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na conclusão ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que gerou o alerta.

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: estabelecer configurações seguras para os serviços do Azure 

**Diretrizes**: o serviço de signaler do Azure dá suporte a políticas específicas de serviço que estão disponíveis na central de segurança do Azure para auditar e impor configurações de seus recursos do Azure. Isso pode ser configurado na central de segurança do Azure ou com iniciativas de Azure Policy.

Você pode usar os planos gráficos do Azure para automatizar a implantação e a configuração de serviços e ambientes de aplicativos, incluindo modelos do Azure Resource Manager, controles do Azure RBAC (controles de acesso baseado em função) e políticas em uma única definição de Blueprint.

- [Trabalhando com políticas de segurança na central de segurança do Azure](../security-center/tutorial-security-policy.md)

- [Auditar a conformidade dos recursos do serviço de Signaler do Azure usando o Azure Policy](signalr-howto-azure-policy.md)

- [Tutorial – criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: manter configurações seguras para os serviços do Azure

**Orientação**: Use a central de segurança do Azure para monitorar sua linha de base de configuração e impor o uso de Azure Policy [Deny] e [implantar se não existir] para impor a configuração segura para o serviço de Signaler do Azure. A política de serviço do Azure Signalr inclui:

Informações adicionais estão disponíveis nos links referenciados.

- [Auditar a conformidade dos recursos do serviço de Signaler do Azure usando o Azure Policy](signalr-howto-azure-policy.md)

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: estabelecer configurações seguras para recursos de computação

**Diretrizes**: Use a central de segurança do azure e Azure Policy para estabelecer configurações seguras no serviço de Signaler do Azure.

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

-   Uso de funcionalidades nativas e de terceiros de proteção de dados do Azure

-   Requisitos de criptografia de dados para casos de uso em trânsito e em repouso

-   Padrões de criptografia apropriados

Mais informações estão disponíveis nos links referenciados.

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

**Diretriz**: estabeleça uma estratégia de log e resposta a ameaças para detectar e corrigir as ameaças rapidamente, atendendo aos requisitos de conformidade. Priorize o fornecimento de alertas de alta qualidade e experiências integradas aos analistas, de modo que possam se concentrar nas ameaças em vez da integração e de etapas manuais. 

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
