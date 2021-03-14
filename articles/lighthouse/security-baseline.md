---
title: Linha de base de segurança do Azure para Azure Lighthouse
description: A linha de base de segurança Lighthouse do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69234d7c22f1725f6f21fe52a455e64d743f052e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709845"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Linha de base de segurança do Azure para Azure Lighthouse

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 2,0](../security/benchmarks/overview.md) para o Azure Lighthouse. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Lighthouse. Os **controles** não aplicáveis ao Azure Lighthouse foram excluídos.

Para ver como o Azure Lighthouse é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de Lighthouse do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: o Azure Lighthouse usa o Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Padronize o Azure AD para controlar a identidade e o gerenciamento de acesso de sua organização no:
- Microsoft Cloud recursos, como o portal do Azure, o armazenamento do Azure, a máquina virtual do Azure (Linux e Windows), Azure Key Vault, PaaS e aplicativos SaaS.
- Os recursos da sua organização, como os aplicativos no Azure ou os recursos de rede corporativa.

Com o Azure Lighthouse, os usuários designados em um locatário de gerenciamento têm uma função interna do Azure que permite acessar assinaturas delegadas e/ou grupos de recursos no locatário de um cliente. Atualmente, todas as funções internas têm suporte, exceto o proprietário ou quaisquer funções internas com a permissão dataactions. A função de Administrador de Acesso do Usuário tem suporte apenas para uso limitado na atribuição de funções a identidades gerenciadas. As funções personalizadas e as funções de administrador de assinatura clássica não têm suporte.

- [Locatários no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usar provedores de identidade externos para o aplicativo](../active-directory/external-identities/identity-providers.md) 

- [O que é a classificação de segurança de identidade no Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: as identidades gerenciadas do Azure podem ser autenticadas nos serviços e recursos do Azure que dão suporte à autenticação do Azure AD. A autenticação é habilitada por meio de regras de concessão de acesso predefinidas, evitando credenciais embutidas no código-fonte ou arquivos de configuração. Com o Azure Lighthouse, os usuários com a função de administrador de acesso do usuário na assinatura de um cliente podem criar uma identidade gerenciada no locatário desse cliente. Embora essa função não seja geralmente suportada com o Azure Lighthouse, ela pode ser usada nesse cenário específico, permitindo que os usuários com essa permissão atribuam uma ou mais funções internas específicas a identidades gerenciadas.

Para serviços que não dão suporte a identidades gerenciadas, use o Azure AD para criar uma entidade de serviço com permissões restritas no nível de recurso. O Azure Lighthouse permite que as entidades de serviço acessem os recursos do cliente de acordo com as funções concedidas durante o processo de integração. É recomendável configurar entidades de serviço com credenciais de certificado e fazer fallback para os segredos do cliente. Em ambos os casos, Azure Key Vault pode ser usado em conjunto com as identidades gerenciadas do Azure, para que o ambiente de tempo de execução (como uma função do Azure) possa recuperar a credencial do cofre de chaves.

- [Identidades gerenciadas do Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Usar Azure Key Vault para o registro de entidade de segurança](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Criar um usuário que pode atribuir funções a uma identidade gerenciada em um locatário do cliente](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Orientação**: exigir MFA (autenticação multifator) para todos os usuários em seu locatário de gerenciamento, incluindo usuários que terão acesso a recursos de cliente delegados. Sugerimos que você solicite que seus clientes implementem a MFA em seus locatários também.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: o Azure ad fornece as seguintes fontes de dados: 

-   Entradas – O relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

-   Logs de auditoria – fornece rastreamento por meio de logs para todas as alterações feitas por meio de vários recursos no Azure AD. Exemplos de logs de auditoria de alterações registradas incluem adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

-   Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

-   Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM de terceiros.

Provedores de serviço usando o Azure Lighthouse podem encaminhar logs do Azure AD para o Azure Sentinel e exibir/definir alertas entre locatários para monitorar e alertar em anomalias de conta.

- [Relatórios de atividade de auditoria no Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Gerenciar espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

- [Conectar dados do Azure AD ao Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Diretrizes**: o Azure Lighthouse não dá suporte a uma capacidade de acesso condicional para recursos de cliente delegados. No locatário de gerenciamento, use o acesso condicional do Azure AD para obter controle de acesso mais granular com base em condições definidas pelo usuário, como exigir logons de usuário de determinados intervalos de IP para usar a MFA (autenticação multifator). Um gerenciamento de sessão de autenticação granular também pode ser usado por meio da política de acesso condicional do Azure AD para diferentes casos de uso. 

Você deve exigir MFA para todos os usuários em seu locatário de gerenciamento, incluindo usuários que terão acesso a recursos de cliente delegados. Sugerimos que você solicite que seus clientes implementem a MFA em seus locatários também.

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas de acesso condicional comuns](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar o gerenciamento da sessão de autenticação com Acesso Condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteger e limitar os usuários altamente privilegiados

**Orientação**: limite o número de contas de usuário altamente privilegiadas e proteja essas contas em um nível elevado. Uma conta de administrador global não é necessária para habilitar e usar o Lighthouse do Azure.

Para acessar dados do log de atividades no nível do locatário, uma conta deve ser atribuída à função interna do leitor de monitoramento do Azure no escopo raiz (/). Como a função leitor de monitoramento no escopo raiz é um nível amplo de acesso, recomendamos que você atribua essa função a uma conta de entidade de serviço, em vez de a um usuário individual ou a um grupo. Essa atribuição deve ser executada por um usuário que tenha a função de administrador global com acesso elevado adicional. Esse acesso elevado deve ser adicionado imediatamente antes de fazer a atribuição de função e, em seguida, removido quando a atribuição for concluída.

- [Permissões da função Administrador no Azure AD](../active-directory/roles/permissions-reference.md)

- [Atribuindo acesso para monitorar dados do log de atividades no nível do locatário](how-to/monitor-delegation-changes.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: o Azure Lighthouse usa o Azure RBAC (controle de acesso baseado em função) para isolar o acesso a sistemas críticos de negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento nos quais eles estão.

Certifique-se de seguir o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para executar suas tarefas específicas.

Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas comerciais críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Práticas recomendadas para definir usuários e funções para o Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Lighthouse usa contas do Azure Active Directory (Azure AD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos. Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

Os clientes podem examinar o nível de acesso concedido aos usuários no locatário de gerenciamento por meio do Azure Lighthouse no portal do Azure. Eles podem remover esse acesso a qualquer momento.

Além disso, o Azure Privileged Identity Management também pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar as contas de administrador que estão obsoletas ou configuradas incorretamente.

Observação: alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Removendo o acesso a uma delegação](how-to/remove-delegation.md)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Exibindo a página provedores de serviço no portal do Azure](how-to/view-manage-service-providers.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: o Azure Lighthouse é integrado com Azure Active Directory para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../active-directory/roles/security-emergency-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: o Azure Lighthouse é integrado com o Azure Active Directory (Azure AD) para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.

- [O que são revisões de acesso do Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é o gerenciamento de direitos do AD do Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Dependendo dos seus requisitos, você pode usar estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para executar tarefas administrativas com o Azure Lighthouse em ambientes de produção. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas centralmente para impor configuração segura, incluindo autenticação forte, linhas de base de software e hardware e acesso lógico restrito à rede. 

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Diretrizes**: o Azure Lighthouse é integrado ao RBAC (controle de acesso baseado em função) do Azure para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções internas a usuários, grupos, entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD PIM (Privileged Identity Management) e deve ser examinado periodicamente. Use funções internas para alocar permissão e apenas para criar funções personalizadas quando necessário.

O Azure Lighthouse permite o acesso a recursos de clientes delegados usando funções internas do Azure. Na maioria dos casos, você desejará atribuir essas funções a um grupo ou a uma entidade de serviço, em vez de muitas contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados.

Para delegar recursos do cliente a um locatário de gerenciamento, uma implantação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a função interna do proprietário para a assinatura que está sendo integrada (ou que contém os grupos de recursos que estão sendo integrados).

- [Entender locatários, usuários e funções no Azure Lighthouse](concepts/tenants-users-roles.md)

- [Como aplicar o princípio de privilégios mínimos ao Azure Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

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

**Orientação**: as equipes de segurança dos clientes podem examinar os logs de atividades para ver a atividade realizada pelos provedores de serviços que usam o Azure Lighthouse. 

Se um provedor de serviços quiser permitir que sua equipe de segurança examine recursos delegados do cliente, as autorizações da equipe de segurança deverão incluir a função interna do leitor.

- [Como um cliente pode examinar a atividade do provedor de serviços](how-to/view-service-provider-activity.md)

- [Como especificar funções ao integrar um cliente ao Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Diretrizes**: Remova o acesso a recursos que foram delegados por meio do Azure Lighthouse depois que eles não forem mais necessários, para que os provedores de serviços não tenham mais acesso. O acesso pode ser removido pelo cliente ou pelo provedor de serviços. 

- [Remover o acesso a uma delegação](how-to/remove-delegation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: o Azure Lighthouse é integrado com o Azure Active Directory (Azure AD) para identidade e autenticação. Você pode usar o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: habilitar a detecção de ameaças para recursos do Azure

**Orientação**: por meio do Azure Lighthouse, você pode monitorar os recursos do Azure dos seus clientes para possíveis ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir os falsos positivos para os analistas classificarem. Os alertas podem ser originados de dados de log, agentes ou outros dados.

Use o recurso de detecção de ameaças interna da central de segurança do Azure, que se baseia no monitoramento da telemetria de serviço do Azure e na análise de logs de serviço. Os dados são coletados usando o agente de Log Analytics, que lê várias configurações relacionadas à segurança e logs de eventos do sistema e copia os dados para o espaço de trabalho para análise. 

Além disso, use o Azure Sentinel para criar regras de análise, que comparam as ameaças que correspondem a critérios específicos no ambiente do cliente. As regras geram incidentes quando os critérios são correspondidos, para que você possa investigar cada incidente. O Azure Sentinel também pode importar inteligência de ameaças de terceiros para aprimorar seu recurso de detecção de ameaças. 

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

- [Guia de referência de alertas de segurança da central de segurança do Azure](../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detectar ameaças](../sentinel/tutorial-detect-threats-custom.md)

- [Gerenciar espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Orientação**: por meio do Azure Lighthouse, você pode usar a central de segurança do Azure para alertar sobre determinadas atividades suspeitas nos locatários do cliente que você gerencia, como um número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura.

O Azure Active Directory (AD do Azure) fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure AD ou integrados com o Azure Monitor, o Azure Sentinel ou outras ferramentas de monitoramento/SIEM para casos de uso de monitoramento e análise mais sofisticados:
- Entrar – o relatório de entrada fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entrada arriscada-uma entrada arriscada é um indicador para uma tentativa de entrada que pode ter sido executada por alguém que não seja o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de Proteção contra Ameaças da Central de Segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (BD SQL e armazenamento) e camadas de serviço do Azure. Esse recurso fornece visibilidade sobre anomalias de conta dentro dos recursos individuais. 

- [Serviços aprimorados e cenários com o Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que estão disponíveis automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos do Azure Lighthouse, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

Com o Azure Lighthouse, você pode usar os logs de Azure Monitor de maneira escalonável nos locatários do cliente que você está gerenciando. Crie Log Analytics espaços de trabalho diretamente nos locatários do cliente para que os dados do cliente permaneçam em seus locatários em vez de serem exportados para o seu. Isso também permite o monitoramento centralizado de quaisquer recursos ou serviços com suporte pelo Log Analytics, proporcionando a você mais flexibilidade sobre os tipos de dados que você monitora.

Os clientes que tiverem assinaturas delegadas para o Azure Lighthouse podem exibir os dados do log de atividades do Azure para ver todas as ações executadas. Isso dá aos clientes visibilidade total das operações que os provedores de serviços estão executando, juntamente com as operações realizadas pelos usuários no locatário do próprio Azure Active Directory (Azure AD) do cliente.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Monitorar recursos delegados em escala](how-to/monitor-at-scale.md)

- [Exibir atividade do provedor de serviços](how-to/view-service-provider-activity.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o armazenamento e a análise de log para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.

Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Com o Azure Lighthouse, você pode usar os logs de Azure Monitor de maneira escalonável nos locatários do cliente que você está gerenciando. Crie Log Analytics espaços de trabalho diretamente nos locatários do cliente para que os dados do cliente permaneçam em seus locatários em vez de serem exportados para o seu. Isso também permite o monitoramento centralizado de quaisquer recursos ou serviços com suporte pelo Log Analytics, proporcionando a você mais flexibilidade sobre os tipos de dados que você monitora.

Os clientes que tiverem assinaturas delegadas para o Azure Lighthouse podem exibir os dados do log de atividades do Azure para ver todas as ações executadas. Isso dá aos clientes visibilidade total das operações que os provedores de serviços estão executando, juntamente com as operações realizadas pelos usuários no locatário do próprio Azure Active Directory (Azure AD) do cliente.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Monitorar recursos delegados em escala](how-to/monitor-at-scale.md)

- [Como os clientes podem exibir a atividade do provedor de serviços](how-to/view-service-provider-activity.md)

- [Gerenciar espaços de trabalho do Azure Sentinel em escala](how-to/manage-sentinel-workspaces.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Diretrizes**: no momento, o Azure Lighthouse não produz nenhum log relacionado à segurança. Os clientes que desejam exibir a atividade do provedor de serviços podem configurar a retenção de log de acordo com os requisitos de conformidade, regulamentação e negócios. 

No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- A [exportação de recomendações e alertas da central de segurança do Azure](../security-center/continuous-export.md) configura e o cliente não pode definir nenhuma retenção de log.

- [Como um cliente pode examinar os dados do log de atividades para provedores de serviços](how-to/view-service-provider-activity.md)

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

**Diretrizes**: o Azure Lighthouse dá suporte a políticas específicas de serviço que estão disponíveis na central de segurança do Azure para auditar e impor configurações de seus recursos do Azure. Isso pode ser configurado na central de segurança do Azure ou em iniciativas de Azure Policy.

- Permitir o gerenciamento de IDs de locatário para integração por meio do Azure Lighthouse

- Auditar a delegação de escopos a um locatário de gerenciamento

Você pode usar plantas do Azure para automatizar a implantação e a configuração de serviços e ambientes de aplicativos, incluindo modelos de Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint.

- [Políticas do Azure Lighthouse](samples/policy-reference.md)

- [Tutorial – criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprints](../governance/blueprints/overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: manter configurações seguras para os serviços do Azure

**Diretrizes**: o Azure Lighthouse dá suporte a políticas específicas de serviço que estão disponíveis na central de segurança do Azure para auditar e impor configurações de seus recursos do Azure. Isso pode ser configurado na central de segurança do Azure ou em iniciativas de Azure Policy.

- [Políticas do Azure Lighthouse](samples/policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: estabelecer configurações seguras para recursos de computação

**Diretrizes**: Use a central de segurança do Azure e Azure Policy para estabelecer configurações seguras em todos os recursos de computação, incluindo VMS, contêineres e outros.

- [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

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

- [Azure Security Benchmark – Gerenciamento de ativos](../security/benchmarks/security-controls-v2-incident-response.md)

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

-   Borda da Internet e a estratégia de entrada e saída/Azure/Security/benchmarks/Security-Controls-v2-Logging-Threat-Detection
-   Estratégia de interconectividade local e de nuvem híbrida

-   Artefatos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para saber mais, consulte as referências a seguir:
- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Segurança de rede](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

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