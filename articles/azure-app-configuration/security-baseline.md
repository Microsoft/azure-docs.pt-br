---
title: Linha de base de segurança do Azure para configuração de Azure App
description: O Azure App linha de base de segurança de configuração fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8b68e22e69bf2d4a1655883b249f883f98f565a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101091246"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Linha de base de segurança do Azure para configuração de Azure App

Essa linha de base de segurança aplica diretrizes da [versão 2,0 do benchmark de segurança do Azure](../security/benchmarks/overview.md) para Azure app configuração. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à configuração de Azure app. Os **controles** não aplicáveis à configuração de Azure app foram excluídos.

Para ver como Azure App configuração é completamente mapeada para o benchmark de segurança do Azure, consulte o [arquivo completo Azure app mapeamento de linha de base de segurança de configuração](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementar a segurança para tráfego interno

**Orientação**: Azure app configuração não implanta recursos diretamente em uma rede virtual. Como o serviço não é implantado em uma rede virtual, você não pode aproveitar determinados recursos de rede para proteger o tráfego interno do serviço, como: grupos de segurança de rede, tabelas de rotas ou outros dispositivos de rede, como um firewall do Azure. No entanto, a configuração de aplicativo permite que você use pontos de extremidade privados para se conectar com segurança à configuração de Azure App de uma rede virtual.

Use o Azure Sentinel para descobrir o uso de protocolos inseguros herdados, como SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, associações LDAP não assinadas e codificações fracas no Kerberos.

- [Usando pontos de extremidade privados para configuração de Azure App](concept-private-endpoint.md)

- [Pasta de trabalho de protocolos inseguros do Azure Sentinel](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="ns-2-connect-private-networks-together"></a>NS-2: conectar redes privadas juntas

**Diretrizes**: a configuração do Azure App dá suporte ao uso de pontos de extremidade privados para enviar dados com segurança por meio de um link privado. Use o Azure ExpressRoute ou a VPN (rede virtual privada) do Azure para criar conexões privadas entre os data centers do Azure e a infraestrutura local em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública e oferecem mais confiabilidade, velocidades mais rápidas e latências menores do que as conexões de Internet típicas. Para VPN ponto a site e VPN site a site, você pode conectar dispositivos ou redes locais a uma rede virtual usando qualquer combinação dessas opções de VPN e do Azure ExpressRoute.

Para conectar duas ou mais redes virtuais no Azure juntas, use o emparelhamento de rede virtual. O tráfego de rede entre redes virtuais emparelhadas é privado e é mantido na rede de backbone do Azure.

- [Quais são os modelos de conectividade do ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Visão geral da VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: estabelecer o acesso de rede privada aos serviços do Azure

**Orientação**: Use o link privado do Azure para habilitar o acesso privado para Azure app configuração de suas redes virtuais sem cruzar a Internet.

O acesso privado é uma medida aprofundada de defesa adicional, além da segurança de autenticação e tráfego oferecida pelos serviços do Azure.

- [Entender o link privado do Azure](../private-link/private-link-overview.md)

- [Como configurar o link privado na configuração do Azure App](concept-private-endpoint.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteger aplicativos e serviços de ataques de rede externa

**Orientação**: ao acessar valores de configuração por meio de uma rede virtual, proteja seus recursos contra ataques de redes externas, incluindo ataques de DDoS (negação de serviço distribuído), ataques específicos de aplicativo e tráfego de Internet não solicitado e potencialmente mal-intencionado. Use o Firewall do Azure para proteger aplicativos e serviços contra tráfego potencialmente mal-intencionado da Internet e de outros locais externos. Proteja seus ativos contra ataques de DDoS habilitando a proteção padrão de DDoS em suas redes virtuais do Azure. Use a central de segurança do Azure para detectar riscos de configuração incorreta relacionados aos seus recursos relacionados à rede.

Azure App configuração não se destina a executar aplicativos Web, ela fornece a configuração para esses aplicativos Web. Não é necessário definir nenhuma configuração adicional ou implantar serviços de rede adicionais para protegê-los contra ataques de rede externa direcionados a aplicativos Web.

- [Documentação do firewall do Azure](../firewall/index.yml)

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md)

- [Recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md#recs-networking)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implantar sistemas de detecção de intrusões/prevenção de intrusão (IDS/IPS)

**Orientação**: Use o Firewall do Azure com a filtragem baseada em inteligência contra ameaças para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft. Quando a inspeção de carga é necessária, você pode implantar uma solução de IDS/IPS de terceiros do Azure Marketplace com recursos de inspeção de conteúdo. Como alternativa, você pode optar por usar IDS/IPS baseados em host ou uma solução de EDR (detecção e resposta de ponto de extremidade) baseada em host em conjunto com o ou em vez de IDS/IPS baseados em rede.

Observação: se você tiver uma regulamentação ou outro requisito para o uso de IDS/IPS, verifique se ele sempre está ajustado para fornecer alertas de alta qualidade para sua solução SIEM.

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [O Azure Marketplace inclui recursos de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Recurso EDR do Microsoft defender ATP](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: Use as marcas de serviço de rede virtual do Azure para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure configurados para seus recursos de configuração Você pode usar a marca de serviço "AppConfiguration" no lugar de endereços IP específicos ao criar regras de segurança para o tráfego de saída na rede do seu aplicativo. Ao especificar o nome da marca de serviço no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: Azure app configuração é integrada ao Azure Active Directory (AD do Azure) que é o serviço de gerenciamento de identidade e acesso padrão do Azure. Você deve padronizar o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:
- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.
- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

A proteção do Azure AD deve ser uma prioridade alta na prática de segurança de nuvem da sua organização. O Azure AD fornece uma classificação de segurança de identidade para ajudar você a avaliar a postura de segurança de identidade em relação às recomendações de melhores práticas da Microsoft. Use a classificação para medir o alinhamento da sua configuração com as recomendações de melhores práticas e fazer aprimoramentos na sua postura de segurança.

O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:

- Proprietário de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura/gravação/exclusão aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.

- Leitor de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.

- Colaborador: Use essa função para gerenciar o recurso de Configuração de Aplicativos. Embora os dados de configuração do aplicativo possam ser acessados usando chaves de acesso, essa função não concede acesso direto aos dados usando o Azure AD.

- Leitor: Use essa função para conceder acesso de leitura ao recurso de Configuração de Aplicativos. Isso não concede acesso às chaves de acesso do recurso nem aos dados armazenados na Configuração de Aplicativos.

Para saber mais, consulte as referências a seguir:

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [O que é a classificação de segurança de identidade no Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Autorizar o acesso à configuração do Azure App usando o Azure AD](concept-enable-rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas pelo Azure para acessar Azure app configuração de contas não humanas, como outros serviços do Azure. É recomendável usar o recurso de identidade gerenciada do Azure em vez de criar uma conta humana mais poderosa para acessar ou executar seus recursos para limitar a necessidade de gerenciar credenciais adicionais. A configuração de Azure App também pode ser atribuída a uma identidade gerenciada para autenticar nativamente para outros serviços/recursos do Azure que dão suporte à autenticação do Azure AD. Isso pode ser útil para habilitar o acesso fácil da configuração do aplicativo para Azure Key Vault ao recuperar segredos. Ao usar identidades gerenciadas, a identidade é gerenciada pela plataforma do Azure e não exige que você provisione ou gire nenhum segredo.

Azure App configuração dá suporte ao seu aplicativo que recebe dois tipos de identidades:
- Uma identidade atribuída pelo sistema é vinculada ao recurso de configuração. Ele será excluído se o recurso de configuração for excluído. Um recurso de configuração pode ter apenas uma identidade atribuída pelo sistema.
- Uma identidade atribuída pelo usuário é um recurso autônomo do Azure que pode ser atribuído ao recurso de configuração. Um recurso de configuração pode ter várias identidades atribuídas pelo usuário.

Quando identidades gerenciadas não puderem ser aproveitadas, crie uma entidade de serviço com permissões restritas no nível de recurso de configuração Azure App. Configure essas entidades de serviço com credenciais de certificado e volte para os segredos do cliente. Em ambos os casos, Azure Key Vault pode ser usada em conjunto com as identidades gerenciadas do Azure, para que o ambiente de tempo de execução (por exemplo, uma função do Azure) possa recuperar a credencial do cofre de chaves.

- [Como usar identidades gerenciadas para configuração de Azure App](overview-managed-identity.md)

- [Identidades gerenciadas do Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Serviços compatíveis com identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Usar identidades gerenciadas para acessar a Configuração de Aplicativos](howto-integrate-azure-managed-service-identity.md)

- [Entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Criar uma entidade de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Usar Azure Key Vault para o registro de entidade de segurança](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: a configuração do Azure App usa o Azure Active Directory (AD do Azure) para fornecer gerenciamento de identidade e acesso aos recursos do Azure, aplicativos de nuvem e aplicativos locais. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros e fornecedores. O Azure AD permite que o SSO (logon único) gerencie o serviço de configuração de aplicativo por meio do portal do Azure usando qualquer identidade de Active Directory corporativa sincronizada. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo e seguro, além de maior visibilidade e controle.

- [Noções básicas sobre o SSO do aplicativo com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Orientação**: Azure app configuração usa Azure Active Directory que dá suporte a controles de autenticação fortes por meio da MFA (autenticação multifator) e de métodos fortes de senha.
- Autenticação multifator: habilite a MFA do Azure AD e siga as recomendações de gerenciamento de identidades e acesso da Central de Segurança do Azure para obter algumas melhores práticas na sua configuração de MFA. A MFA pode ser imposta em todos os usuários, em usuários selecionados ou no nível por usuário com base nas condições de entrada e nos fatores de risco.
- Autenticação sem senha: três opções de autenticação sem senha estão disponíveis: Windows Hello para Empresas, aplicativo Microsoft Authenticator e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, verifique se o nível mais alto de método de autenticação forte é usado, seguido da distribuição da política de autenticação forte apropriada para outros usuários.

Observação: a MFA pode ser imposta nas contas de usuário que estão acessando e gerenciando a configuração do aplicativo, mas não em contas de serviço programático. Use a autenticação com senha, como identidades gerenciadas, sempre que possível, e aplique a MFA em qualquer conta de usuário.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: Azure app configuração é integrada com o Azure Active Directory no, que fornece as seguintes fontes de dados:

-   Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

-   Logs de auditoria – fornece rastreamento por meio de logs para todas as alterações feitas por meio de vários recursos no Azure AD. Exemplos de logs de auditoria de alterações registradas incluem adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

-   Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

-   Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM de terceiros.

A central de segurança do Azure também pode alertar sobre determinadas atividades suspeitas, como um número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. 

A ATP (proteção avançada contra ameaças) do Azure é uma solução de segurança que pode usar sinais de Active Directory locais para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de insider mal-intencionadas.

- [Relatórios de atividade de auditoria no Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Alertas do módulo de proteção da inteligência contra ameaças da Central de Segurança do Azure](../security-center/alerts-reference.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Conectar dados de Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Proteção Avançada contra Ameaças do Azure](/azure-advanced-threat-protection/what-is-atp)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: a configuração do Azure App dá suporte ao acesso condicional do Azure Active Directory (Azure AD) para um controle de acesso mais granular com base em condições definidas pelo usuário, como logons de usuário de determinados intervalos de IP, precisarão usar a MFA para logon. A política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso. Essas políticas de acesso condicional serão aplicadas somente a contas de usuário que estão se Autenticando no Azure AD para acessar e gerenciar o serviço de configuração de aplicativo, mas elas não se aplicarão a entidades de serviço ou cadeias de conexão que se conectam ao seu recurso de configuração.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminar a exposição involuntária de credenciais

**Orientação**: Azure app configuração permite que os clientes armazenem configurações que possam conter identidades ou segredos. É recomendável implementar o verificador de credenciais para identificar as credenciais nas configurações. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Use o serviço de configuração Azure App junto com Azure Key Vault. Armazene as credenciais em Key Vault e, em seguida, vincule a essas credenciais criando uma referência de Key Vault no recurso de configuração de aplicativo. Quando a configuração do aplicativo cria essas referências, ela armazena os URIs dos valores Key Vault em vez dos próprios valores. Os aplicativos podem se conectar à configuração do aplicativo para recuperar as credenciais de Key Vault.

Para o GitHub, você pode usar o recurso de verificação de segredo nativo para identificar as credenciais ou outra forma de segredos dentro do código.

- [Tutorial para usar referências de Key Vault em um aplicativo ASP.NET Core](use-key-vault-references-dotnet-core.md)

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Verificação de segredos do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteger e limitar os usuários altamente privilegiados

**Orientação**: limite o número de contas ou funções altamente privilegiadas e proteja essas contas em um nível elevado, pois os usuários com esse privilégio podem ler e modificar de forma direta ou indireta todos os recursos em seu ambiente do Azure.

Você pode habilitar o acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD usando o Azure AD PIM (Privileged Identity Management). O JIT concede permissões temporárias para executar tarefas privilegiadas somente quando os usuários precisam dela. O PIM também pode gerar alertas de segurança quando há atividades suspeitas ou não seguras na sua organização do Azure AD.

As chaves de acesso são altamente privilegiadas e devem ser giradas regularmente como uma prática recomendada de segurança. Chaves de acesso contêm cadeias de conexão, que contêm informações de credenciais e são consideradas segredos. Esses segredos precisam ser armazenados em Azure Key Vault e seu código deve se autenticar no Key Vault para recuperá-los. As chaves de acesso podem conceder acesso de leitura/gravação ou apenas de leitura a um aplicativo. Certifique-se de que o tipo correto de chave de acesso seja emitido para impedir o acesso não autorizado. Para ser mais seguro, use o recurso de identidades gerenciadas no Azure AD. Isso requer que os aplicativos tenham a URL do ponto de extremidade de configuração para acessar valores de configuração.

- [Práticas recomendadas de configuração de aplicativo](howto-best-practices.md#app-configuration-bootstrap)

- [Usar identidades gerenciadas para acessar a Configuração de Aplicativos](howto-integrate-azure-managed-service-identity.md)
- [Permissões da função Administrador no Azure AD](../active-directory/roles/permissions-reference.md)

- [Usar os alertas de segurança do Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../active-directory/roles/security-planning.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Orientação**: Azure app configuração usa o RBAC do Azure para isolar o acesso a sistemas críticos para os negócios, restringindo quais contas recebem acesso privilegiado. O RBAC do Azure tem suporte pela configuração de aplicativo no nível de recurso. Para Silor com segurança as configurações críticas para os negócios, armazene essas informações em seu próprio recurso de configuração de aplicativo. Dentro de um recurso, o acesso granular também está disponível por meio de contas ou chaves de acesso somente leitura, bem como rótulos e marcação.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Para integrar o RBAC usando o Azure AD com a configuração de aplicativo](concept-enable-rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: a configuração do Azure App usa contas do Azure Active Directory (AD do Azure) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos. 

O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:

- Proprietário de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura/gravação/exclusão aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.

- Leitor de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de configuração de aplicativo

Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função, como as funções de configuração de aplicativo acima. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

Observação: as identidades gerenciadas são sugeridas quando possível para autenticação na configuração do aplicativo de outro serviço ou aplicativo. Você precisará gerenciar quaisquer entidades de serviço ou cadeias de conexão configuradas com acesso à configuração de aplicativo separadamente quando usadas.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizar o acesso à configuração do Azure App usando o Azure AD](concept-enable-rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: Azure app configuração é integrada ao Azure Active Directory para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../active-directory/roles/security-emergency-access.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: Azure app configuração é integrada ao Azure Active Directory para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.

- [O que são revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [O que é o gerenciamento de direitos do AD do Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas relacionadas à configuração do aplicativo. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas de modo central para impor a configuração protegida, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Orientação**: Azure app configuração é integrada ao RBAC (controle de acesso baseado em função) do Azure para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para configuração de Azure App, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, Azure PowerShell ou portal do Azure. Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD PIM (Privileged Identity Management) e deve ser examinado periodicamente.

O Azure fornece as seguintes funções internas do Azure para autorizar o acesso aos dados de configuração do aplicativo usando o Azure AD e o OAuth:
- Proprietário de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura/gravação/exclusão aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.
- Leitor de Dados da Configuração de Aplicativos: Use essa função para conceder acesso de leitura aos dados da Configuração de Aplicativos. Isso não concede acesso ao recurso de Configuração de Aplicativos.

Use funções internas para alocar permissão e apenas para criar funções personalizadas quando necessário. 

A configuração de aplicativo dá suporte ao armazenamento de configuração de vários aplicativos em um recurso de configuração de aplicativo. Para limitar o acesso às informações entre os aplicativos, crie um recurso de configuração de aplicativo por aplicativo e configure o RBAC do Azure adequadamente.

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizar o acesso à configuração do Azure App usando o Azure AD](concept-enable-rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: escolha o processo de aprovação para o suporte da Microsoft  

**Diretrizes**: implemente um processo de aprovação organizacional para cenários de suporte em que a Microsoft possa precisar acessar seus dados de configuração de aplicativo. O Sistema de Proteção de Dados do Cliente não está disponível no momento para cenários de suporte de configuração de aplicativo.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: descobrir, classificar e rotular dados confidenciais

**Orientação**: descubra, classifique e rotule seus dados confidenciais para que você possa criar os controles apropriados para garantir que as informações confidenciais sejam armazenadas, processadas e transmitidas com segurança pelos sistemas de tecnologia da organização. A rotulação de informações confidenciais, na forma de marcação, tem suporte para recursos de configuração de aplicativo, mas não para valores de configuração contidos nelas. Quando um aplicativo tem acesso de leitura ou leitura/gravação a um repositório de configuração, ele tem acesso completo a qualquer uma das configurações nesse repositório.

- [Marcar informações confidenciais usando a Proteção de Informações do Azure](/azure/information-protection/what-is-information-protection)

- [Marcação de classificações de dados no Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: para a plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou funcionalidades e controles padrão de proteção de dados. Verifique se você está girando regularmente as chaves de acesso para os recursos de configuração do aplicativo. As informações de credenciais de cadeias de conexão podem ser armazenadas em Azure Key Vault e seu código deve se autenticar no Key Vault para recuperá-las. As chaves de acesso podem conceder acesso de leitura/gravação ou apenas de leitura a um aplicativo. Certifique-se de que o tipo correto de chave de acesso seja emitido para impedir o acesso não autorizado. Para ser mais seguro, use o recurso de identidades gerenciadas no Azure AD. Isso requer que os aplicativos tenham a URL do ponto de extremidade de configuração para acessar valores de configuração.

Restringir o acesso usando o controle de acesso baseado em função do Azure (RBAC do Azure):

- Separe os dados confidenciais em seu próprio recurso de configuração de aplicativo e aloque políticas de RBAC de forma adequada para que apenas o acesso autorizado seja habilitado 

- Usar controles de acesso baseados em rede

- Controles específicos nos serviços do Azure (como criptografia em SQL e outros bancos de dados) e garantem o controle de acesso consistente, todos os tipos de controle de acesso devem ser alinhados à sua estratégia de segmentação corporativa.

- A estratégia de segmentação corporativa também deve ser informada pela localização de sistemas e dados confidenciais e comercialmente críticos.

Para saber mais, consulte as referências a seguir:

- [Autorizar o acesso à Configuração de Aplicativos do Azure usando o Azure Active Directory](concept-enable-rbac.md)

- [Criptografia de dados de configuração de aplicativo](faq.yml#does-app-configuration-encrypt-my-data)

- [RBAC (Controle de Acesso Baseado em Função) do Azure](../role-based-access-control/overview.md) 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: criptografar informações confidenciais em trânsito

**Orientação**: para complementar os controles de acesso, os dados em trânsito devem ser protegidos contra ataques "fora de banda" usando criptografia. Isso ajuda a garantir que os invasores não possam ler ou modificar os dados facilmente.

Azure App configuração usa a criptografia TLS para todas as solicitações HTTP. A infraestrutura do Azure fornece uma camada adicional de criptografia no nível de rede para todas as solicitações entre os data centers do Azure. Certifique-se de que o tráfego HTTP que qualquer cliente se conecta aos seus recursos de configuração de aplicativo possa negociar TLS v 1.2 ou superior.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: criptografar dados confidenciais em repouso

**Orientação**: para complementar os controles de acesso, os dados em repouso devem ser protegidos contra ataques "fora de banda" (como o acesso ao armazenamento subjacente) usando a criptografia. Isso ajuda a garantir que os invasores não possam ler ou modificar os dados facilmente.

Por padrão, o Azure fornece dados em criptografia REST. Para dados altamente confidenciais, você tem opções para implementar criptografia adicional em repouso em todos os recursos do Azure, quando disponível. O Azure gerencia suas chaves de criptografia por padrão, mas o Azure fornece a opção de gerenciar suas próprias chaves (chaves gerenciadas pelo cliente) para configuração de Azure App.

- [Usar chaves gerenciadas pelo cliente para criptografar seus dados na configuração Azure App](concept-customer-managed-keys.md)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modelo de criptografia e tabela de gerenciamento de chaves](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Criptografia dupla de dados em repouso no Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

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

**Orientação**: garanta que as equipes de segurança tenham acesso a um inventário de ativos continuamente atualizado no Azure, como a configuração de Azure app. As equipes de segurança geralmente precisam desse inventário para avaliar a potencial exposição da organização delas a riscos emergentes e como uma entrada para aprimoramentos de segurança contínuos. Crie um grupo de Azure Active Directory para conter a equipe de segurança autorizada de sua organização e atribua a eles acesso de leitura a todos os Azure App recursos de configuração. isso pode ser simplificado por uma única atribuição de função de alto nível em sua assinatura.

O recurso de inventário da central de segurança do Azure e o grafo de recursos do Azure podem consultar e descobrir todos os recursos em suas assinaturas, incluindo serviços do Azure, aplicativos e recursos de rede.

Aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Gerenciamento de inventário de ativos da central de segurança do Azure](../security-center/asset-inventory.md)

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: a configuração do Azure App dá suporte a implantações baseadas em Azure Resource Manager e imposição de configuração usando o Azure Policy. Use o Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretriz**: estabeleça ou atualize as políticas de segurança que atendem aos processos de gerenciamento do ciclo de vida de ativos em relação a modificações que, potencialmente, sejam de alto impacto. Essas modificações incluem alterações, mas não se limitam a: provedores de identidade e acesso, confidencialidade de dados, configuração de rede e atribuição de privilégio administrativo.

Remova os recursos do Azure quando eles não forem mais necessários. Verifique se os administradores giram regularmente as chaves de acesso para garantir que somente usuários autenticados tenham acesso ao recurso de configuração.

- [Girar as chaves de criptografia usadas para a configuração do aplicativo](concept-customer-managed-keys.md)

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

**Orientação**: a configuração de aplicativo integra-se com o Azure Active Directory (Azure AD). Isso fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados com o Azure Monitor, o Azure Sentinel ou outras ferramentas de monitoramento/SIEM para casos de uso de monitoramento e análise mais sofisticados:
- Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de proteção contra ameaças da central de segurança do Azure também pode coletar alertas de segurança mais aprofundados das camadas de serviço do Azure. Esse recurso oferece visibilidade sobre anomalias de conta dentro dos recursos individuais.

Outro método de obter acesso ao recurso de configuração de configuração de aplicativo está usando chaves de acesso. Eles precisam ser girados regularmente para garantir que nenhum agente não autorizado tenha acesso ao recurso de configuração. Girá-los pode ser feito diretamente no portal em "chaves de acesso".

- [Para permitir que Azure App configuração acesse outros recursos protegidos do Azure AD usando uma identidade gerenciada](overview-managed-identity.md)

- [Usar identidades gerenciadas com a configuração Azure App](howto-integrate-azure-managed-service-identity.md)

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Autorizando o acesso à configuração de Azure App usando o Azure AD](concept-enable-rbac.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: habilitar o registro em log para atividades de rede do Azure

**Orientação**: Azure app configuração não implanta recursos diretamente em uma rede virtual. No entanto, a configuração de aplicativo permite que você use pontos de extremidade privados para se conectar com segurança à configuração de Azure App de uma rede virtual. Azure App configuração também não produz nem processa logs de consulta DNS que precisam ser habilitados.

Habilite o log em seus pontos de extremidade particulares de configuração de aplicativo configurados para capturar:
- dados processados pelo Ponto de Extremidade Privado (ENTRADA/SAÍDA)
- dados processados pelo serviço de Link Privado (ENTRADA/SAÍDA)
- Disponibilidade da porta NAT

Para saber mais, consulte as referências a seguir:

- [Monitoramento de link privado do Azure](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que estão disponíveis automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos de configuração de aplicativo, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso. Para configuração de aplicativo, os logs de atividade só estão disponíveis no plano de controle e são exibidos pelo Azure Resource Manager (ARM). Atualmente, não há suporte para o log do plano de dados voltado para o cliente para configuração de aplicativo Os logs de recursos do Azure também não estão disponíveis para serem configurados.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros. Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Orientação**: Verifique se as contas de armazenamento ou os espaços de trabalho log Analytics usados para armazenar os logs de configuração de aplicativo têm o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização.

- [Como configurar Log Analytics período de retenção do espaço de trabalho](../azure-monitor/logs/manage-cost-storage.md)

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

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

**Diretrizes**: a configuração do Azure App dá suporte a políticas específicas do serviço que estão disponíveis na central de segurança do Azure para auditar e impor as configurações dos recursos do Azure. Isso pode ser configurado na central de segurança do Azure ou em iniciativas de Azure Policy.
- A configuração do aplicativo deve usar uma chave gerenciada pelo cliente: chaves gerenciadas pelo cliente fornecem proteção de dados avançada, permitindo que você gerencie suas chaves de criptografia. Isso geralmente é necessário para atender aos requisitos de conformidade.
- A configuração do aplicativo deve usar um link privado: as conexões de ponto de extremidade privado permitem que os clientes em uma rede virtual acessem com segurança Azure App configuração por meio de um link privado.

Você pode usar plantas do Azure para automatizar a implantação e a configuração de serviços e ambientes de aplicativos, incluindo modelos do Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint.

- [Mais informações sobre as políticas de configuração de aplicativo](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Trabalhando com políticas de segurança na central de segurança do Azure](../security-center/tutorial-security-policy.md)

- [Ilustração da implementação de guardrails na zona de aterrissagem de escala empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: manter configurações seguras para os serviços do Azure

**Diretrizes**: Use a central de segurança do Azure para monitorar sua linha de base de configuração e impor o uso de Azure Policy. O Azure Policy para a configuração de aplicativo inclui: 
- A configuração do aplicativo deve usar uma chave gerenciada pelo cliente: chaves gerenciadas pelo cliente fornecem proteção de dados avançada, permitindo que você gerencie suas chaves de criptografia. Isso geralmente é necessário para atender aos requisitos de conformidade.
- A configuração do aplicativo deve usar um link privado: as conexões de ponto de extremidade privado permitem que os clientes em uma rede virtual acessem com segurança Azure App configuração por meio de um link privado.

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md) 

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

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