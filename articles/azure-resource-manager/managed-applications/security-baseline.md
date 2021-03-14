---
title: Linha de base de segurança do Azure para aplicativos gerenciados do Azure
description: A linha de base de segurança de aplicativos gerenciados do Azure fornece orientações de procedimentos e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42aea886e38622c773ac1ca0ea2533d8aa1c47bb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717019"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Linha de base de segurança do Azure para aplicativos gerenciados do Azure

Essa linha de base de segurança aplica orientações da [versão 2,0 do benchmark de segurança do Azure](../../security/benchmarks/overview.md) para aplicativos gerenciados do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis aos aplicativos gerenciados do Azure. Os **controles** não aplicáveis aos aplicativos gerenciados do Azure foram excluídos.

Para ver como os aplicativos gerenciados do Azure mapeiam completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de aplicativos gerenciados do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificar as regras de segurança de rede

**Diretrizes**: Use as marcas de serviço de rede virtual do Azure para definir os controles de acesso à rede nos grupos de segurança de rede ou no firewall do Azure configurados para os recursos do aplicativo gerenciado do Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo: AzureResourceManager) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Usar marcas de serviço Azure Resource Manager](../../virtual-network/service-tags-overview.md#available-service-tags)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gerenciamento de Identidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de identidades](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: padronizar o Azure Active Directory como o sistema central de identidade e autenticação

**Diretrizes**: os aplicativos gerenciados do Azure usam o Azure Active Directory (Azure AD) como o serviço de gerenciamento de identidade e acesso padrão. Padronize no Azure AD para controlar a identidade e o gerenciamento de acesso da sua organização.

O Azure fornece as seguintes funções internas do Azure para autorizar o acesso a aplicativos gerenciados usando o Azure AD e o OAuth:

- Função de colaborador de aplicativo gerenciado: permite a criação de recursos de aplicativos gerenciados.

- Função de operador de aplicativo gerenciado: permite que você leia e execute ações em recursos de aplicativos gerenciados

- Leitor de aplicativos gerenciados: permite que você leia recursos em um aplicativo gerenciado e solicite acesso JIT.

Para saber mais, consulte as referências a seguir:

- [Função colaborador de aplicativo gerenciado](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Função de operador de aplicativo gerenciado](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leitor de aplicativos gerenciados](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Locatários no Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gerenciar identidades de aplicativo de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas pelo Azure para conceder permissões para seus aplicativos gerenciados. É recomendável usar o recurso de identidade gerenciada do Azure em vez de criar uma conta humana mais poderosa para acessar ou executar seus recursos para limitar a necessidade de gerenciar credenciais adicionais. O serviço de aplicativos gerenciados do Azure também pode ser atribuído a uma identidade gerenciada para autenticar nativamente para outros serviços/recursos do Azure que dão suporte à autenticação do Azure AD. Isso pode ser útil para permitir o fácil acesso de seus aplicativos gerenciados do Azure para Azure Key Vault ao recuperar segredos. Ao usar identidades gerenciadas, a identidade é gerenciada pela plataforma do Azure e não exige que você provisione ou gire nenhum segredo.

Os aplicativos gerenciados do Azure dão suporte ao seu aplicativo que recebe dois tipos de identidades:

- Uma identidade atribuída pelo sistema é vinculada ao recurso de configuração. Ele será excluído se o recurso de configuração for excluído. Um recurso de configuração pode ter apenas uma identidade atribuída pelo sistema.

- Uma identidade atribuída pelo usuário é um recurso autônomo do Azure que pode ser atribuído ao recurso de configuração. Um recurso de configuração pode ter várias identidades atribuídas pelo usuário.

Quando identidades gerenciadas não puderem ser aproveitadas, crie uma entidade de serviço com permissões restritas no nível de recurso do aplicativo gerenciado do Azure. Configure essas entidades de serviço com credenciais de certificado e volte para os segredos do cliente. Em ambos os casos, Azure Key Vault pode ser usada em conjunto com as identidades gerenciadas do Azure, para que o ambiente de tempo de execução (por exemplo, uma função do Azure) possa recuperar a credencial do cofre de chaves.

- [Como usar identidades gerenciadas para aplicativos gerenciados do Azure](publish-managed-identity.md)

- [Identidades gerenciadas do Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Entidade de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Usar Azure Key Vault para o registro de entidade de segurança](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usar o SSO (logon único) do Azure AD para acesso ao aplicativo

**Diretrizes**: os aplicativos gerenciados do azure usam Azure Active Directory para fornecer gerenciamento de identidade e acesso aos recursos do Azure, aplicativos de nuvem e aplicativos locais. Isso inclui identidades corporativas, como funcionários, bem como identidades externas, como parceiros e fornecedores. Isso permite que o SSO (logon único) gerencie e proteja o acesso aos dados e aos recursos da sua organização no local e na nuvem. Conecte todos os seus usuários, aplicativos e dispositivos ao Azure AD para obter acesso contínuo e seguro, além de maior visibilidade e controle.

- [Noções básicas sobre o SSO do aplicativo com o Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usar controles de autenticação forte para todo o acesso baseado no Azure Active Directory

**Diretrizes**: os aplicativos gerenciados do Azure usam Azure Active Directory que oferece suporte a controles de autenticação fortes por meio da autenticação multifator e métodos fortes de senha.
- Autenticação multifator – habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure para obter algumas práticas recomendadas na configuração de autenticação multifator. a autenticação multifator pode ser imposta em todos, Selecionar usuários ou no nível por usuário com base nas condições de entrada e nos fatores de risco.
- Autenticação sem senha: três opções de autenticação sem senha estão disponíveis: Windows Hello para Empresas, aplicativo Microsoft Authenticator e métodos de autenticação locais, como cartões inteligentes.

Para administradores e usuários privilegiados, verifique se o nível mais alto de autenticação forte é usado.

- [Como habilitar a autenticação multifator no Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução às opções de autenticação sem senha do Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitorar anomalias nas contas e fornecer alertas sobre elas

**Diretrizes**: os aplicativos gerenciados do Azure são integrados ao Azure Active Directory no, que fornece as seguintes fontes de dados:
- Entradas: o relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e as atividades de entrada do usuário.
- Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
- Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.
- Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

Essas fontes de dados podem ser integradas com Azure Monitor, Sentinela do Azure ou sistemas SIEM de terceiros.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura.

O ATP (Proteção Avançada contra Ameaças) do Azure é uma solução de segurança que pode usar os sinais do Active Directory para identificar, detectar e investigar ameaças avançadas, identidades comprometidas e ações de membros mal-intencionados.

- [Relatórios de atividades de auditoria no Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: restringir o acesso aos recursos do Azure com base em condições

**Orientação**: os aplicativos gerenciados do Azure dão suporte ao acesso condicional do Azure ad para um controle de acesso mais granular com base em condições definidas pelo usuário, como logons de usuário de determinados intervalos de IP precisarão usar a autenticação multifator para logon. A política de gerenciamento de sessão de autenticação granular também pode ser usada para diferentes casos de uso.

- [Visão geral do acesso condicional do Azure](../../active-directory/conditional-access/overview.md) 

- [Políticas comuns de acesso condicional](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar o gerenciamento de sessão de autenticação com acesso condicional](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso privilegiado

*Para obter mais informações, confira o [Azure Security Benchmark: acesso privilegiado](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: proteger e limitar os usuários altamente privilegiados

**Diretrizes**: os aplicativos gerenciados do Azure usam o Azure Active Directory (Azure AD) para identidade e acesso. As funções internas mais críticas são o administrador global e o administrador de função com privilégios, pois os usuários atribuídos a essas duas funções podem delegar funções de administrador:
- Administrador global: os usuários com essa função têm acesso a todos os recursos administrativos no Azure AD, bem como a serviços que usam identidades do Azure AD.
- Administrador de função com privilégios: os usuários com essa função podem gerenciar atribuições de função no Azure AD, bem como dentro de Azure AD Privileged Identity Management (PIM). Além disso, essa função permite o gerenciamento de todos os aspectos do PIM e das unidades administrativas.

Observação: você pode ter outras funções críticas que precisam ser governadas se você usar funções personalizadas com determinadas permissões privilegiadas atribuídas. E você também pode querer aplicar controles semelhantes à conta de administrador de ativos de negócios críticos.

Você pode habilitar o acesso privilegiado JIT (just-in-time) aos recursos do Azure e ao Azure AD usando o Azure AD PIM (Privileged Identity Management). O JIT concede permissões temporárias para executar tarefas privilegiadas somente quando os usuários precisam dela. O PIM também pode gerar alertas de segurança quando há atividades suspeitas ou não seguras na sua organização do Azure AD.

- [Permissões da função Administrador no Azure AD](../../active-directory/roles/permissions-reference.md)

- [Usar os alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protegendo o acesso privilegiado para implantações de nuvem e híbridos no Azure AD](../../active-directory/roles/security-planning.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: restringir o acesso administrativo a sistemas críticos para os negócios

**Diretrizes**: os aplicativos gerenciados do Azure usam o RBAC do Azure para isolar o acesso a sistemas críticos para os negócios, restringindo quais contas recebem acesso privilegiado às assinaturas e aos grupos de gerenciamento em que eles estão.

Certifique-se de também restringir o acesso aos sistemas de gerenciamento, identidade e segurança que têm acesso administrativo ao seu acesso comercialmente crítico, como controladores de Domínio do Active Directory (DCs), ferramentas de segurança e ferramentas de gerenciamento do sistema com agentes instalados em sistemas comerciais críticos. Os invasores que comprometem esses sistemas de gerenciamento e segurança podem fazê-los imediatamente para comprometer os ativos críticos aos negócios.

Todos os tipos de controles de acesso devem ser alinhados à sua estratégia de segmentação corporativa para garantir o controle de acesso consistente.

- [Componentes do Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao grupo de gerenciamento](../../governance/management-groups/overview.md#management-group-access)

- [Administradores de assinatura do Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: examinar e reconciliar regularmente o acesso do usuário

**Orientação**: revise as contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seus níveis de acesso sejam válidos.

Os aplicativos gerenciados do Azure usam contas do Azure Active Directory (AAD) para gerenciar seus recursos, examinar contas de usuário e a atribuição de acesso regularmente para garantir que as contas e seu acesso sejam válidos. Você pode usar as revisões de acesso do Azure AD para revisar as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Os relatórios do AD do Azure podem fornecer logs para ajudar a descobrir contas obsoletas. Você também pode usar Azure AD Privileged Identity Management para criar o fluxo de trabalho de relatório de revisão de acesso para facilitar o processo de revisão.

Além disso, o Azure Privileged Identity Management também pode ser configurado para alertar quando um número excessivo de contas de administrador for criado e para identificar as contas de administrador que estão obsoletas ou configuradas incorretamente.

Observação: alguns serviços do Azure dão suporte a usuários e funções locais que não são gerenciados por meio do Azure AD. Você precisará gerenciar esses usuários separadamente.

- [Criar uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como usar as revisões de identidade e acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurar o acesso de emergência no Azure AD

**Diretrizes**: os aplicativos gerenciados do Azure usam Azure Active Directory para gerenciar seus recursos. Para evitar que seja bloqueado acidentalmente da sua organização do Azure AD, configure uma conta de acesso de emergência para acesso quando contas administrativas normais não puderem ser usadas. As contas de acesso de emergência geralmente são altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas à emergência ou cenários de urgência em que as contas administrativas normais não podem ser usadas.

Você deve verificar se as credenciais (como senha, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas pelos indivíduos que têm autorização para usá-las somente em uma emergência.

- [Gerenciar contas de acesso de emergência no Microsoft Azure Active Directory](../../active-directory/roles/security-emergency-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizar o gerenciamento de direitos 

**Diretrizes**: os aplicativos gerenciados do Azure são integrados com o Azure Active Directory para gerenciar seus recursos. Use os recursos de gerenciamento de direitos do Azure AD para automatizar fluxos de trabalho de solicitação de acesso, incluindo atribuições de acesso, revisões e expiração. Também há suporte para a aprovação dupla ou de vários estágios.

- [O que são revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [O que é o gerenciamento de direitos do AD do Azure](../../active-directory/governance/entitlement-management-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usar estações de trabalho com acesso privilegiado

**Diretrizes**: estações de trabalho seguras e isoladas são extremamente importantes para a segurança de funções confidenciais, como administradores, desenvolvedores e operadores de serviços críticos. Use estações de trabalho de usuário altamente protegidas e/ou bastiões do Azure para tarefas administrativas relacionadas aos seus aplicativos gerenciados. Use o Azure Active Directory, o Microsoft ATP (Proteção Avançada contra Ameaças) e/ou o Microsoft Intune para implantar uma estação de trabalho do usuário protegida e gerenciada para tarefas administrativas. As estações de trabalho protegidas podem ser gerenciadas de modo central para impor a configuração protegida, incluindo autenticação forte, linhas de base de software e hardware, acesso lógico restrito e de rede.

- [Entender as estações de trabalho com acesso privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implantar uma estação de trabalho com acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguir a administração Just Enough (princípio de privilégios mínimos) 

**Diretrizes**: os aplicativos gerenciados do Azure são integrados ao RBAC (controle de acesso baseado em função) do Azure para gerenciar seus recursos. O RBAC do Azure permite gerenciar o acesso aos recursos do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que você atribui aos recursos por meio do RBAC do Azure devem ser sempre limitados ao que é exigido pelas funções. Isso complementa a abordagem JIT (just-in-time) do Azure AD PIM (Privileged Identity Management) e deve ser examinado periodicamente.

Sempre que possível, use funções internas para alocar permissão e somente criar função personalizada quando necessário.

O Azure fornece as seguintes funções internas do Azure para autorizar o acesso a aplicativos gerenciados usando o Azure AD e o OAuth:

- Função de colaborador de aplicativo gerenciado: permite a criação de recursos de aplicativos gerenciados.

- Função de operador de aplicativo gerenciado: permite que você leia e execute ações em recursos de aplicativos gerenciados

- Leitor de aplicativos gerenciados: permite que você leia recursos em um aplicativo gerenciado e solicite acesso JIT.

Para saber mais, consulte as referências a seguir:

- [Função colaborador de aplicativo gerenciado](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Função de operador de aplicativo gerenciado](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Leitor de aplicativos gerenciados](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: escolha o processo de aprovação para o suporte da Microsoft  

**Diretrizes**: implemente um processo de aprovação organizacional para cenários de suporte em que a Microsoft possa precisar acessar seus dados de aplicativo gerenciado do Azure. O Sistema de Proteção de Dados do Cliente não está disponível atualmente para cenários de aplicativos gerenciados.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteger dados confidenciais

**Orientação**: para colocar a criptografia com suas próprias chaves, você pode utilizar sua própria conta de armazenamento para o armazenamento dos arquivos de configuração do aplicativo gerenciado.

- [Proteção de dados de arquivos de configuração gerenciados usando seu próprio armazenamento](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: criptografar dados confidenciais em repouso

**Diretrizes**: definições de aplicativo gerenciado que definem seu aplicativo e seus recursos podem ser armazenados em suas próprias contas de armazenamento que podem ser configuradas com chaves de criptografia gerenciadas pelo cliente.

Para cenários em que você não deseja colocar seu próprio armazenamento para definições de aplicativos gerenciados, o Azure fornece dados em criptografia REST por padrão.

- [Traga seu próprio armazenamento para definições de aplicativos gerenciados](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Noções básicas sobre a criptografia em repouso do Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

## <a name="asset-management"></a>Gerenciamento de Ativos

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de ativos](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantir que a equipe de segurança tenha visibilidade dos riscos de ativos

**Diretriz**: verifique se as equipes de segurança receberam as permissões Leitor de segurança no seu locatário e nas suas assinaturas do Azure, de modo que possam monitorar os riscos de segurança usando a Central de Segurança do Azure. 

Dependendo de como as responsabilidades da equipe de segurança são estruturadas, o monitoramento dos riscos de segurança pode ser a responsabilidade de uma equipe de segurança central ou de uma equipe local. Dito isso, os insights e os riscos de segurança sempre precisam ser agregados de maneira centralizada em uma organização. 

As permissões de Leitor de segurança podem ser aplicadas amplamente a um locatário inteiro (grupo de gerenciamento raiz) ou terem como escopo grupos de gerenciamento ou assinaturas específicas. 

Observação: podem ser necessárias permissões adicionais a fim de obter visibilidade das cargas de trabalho e dos serviços. 

- [Visão geral da função Leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: verificar se a equipe de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: aplique marcas aos recursos, grupos de recursos e assinaturas do Azure para organizá-los logicamente em uma taxonomia. Cada marca consiste em um par de nome/valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

As marcas que são fornecidas no momento da criação do aplicativo gerenciado também são aplicadas ao grupo de recursos gerenciado. O editor do seu aplicativo pode fornecer sua própria marcação adicional dos recursos gerenciados após a implantação.

- [Marcas pelo elemento de interface do usuário Application gerenciado por recursos](microsoft-common-tagsbyresource.md)

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md) 

- [Para obter mais informações sobre como marcar ativos, consulte o guia de decisão de nomenclatura e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usar somente serviços do Azure aprovados

**Diretrizes**: os aplicativos gerenciados do Azure dão suporte a implantações baseadas em Azure Resource Manager e imposição de configuração usando Azure Policy. Use o Azure Policy para auditar e restringir quais serviços os usuários podem provisionar em seu ambiente. Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles. Você também poderá usar o Azure Monitor para criar regras para disparar alertas quando um serviço não aprovado for detectado.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantir a segurança do gerenciamento do ciclo de vida dos ativos

**Orientação**: os recursos do aplicativo gerenciado e seus grupos de recursos gerenciados conectados podem ser excluídos com a exclusão do recurso de aplicativo gerenciado. Quando um recurso de aplicativo gerenciado é excluído, ele também exclui o grupo de recursos gerenciado e seu conteúdo. Recursos de ciclo de vida adicionais são especificados pelo editor do aplicativo, em que eles podem conceder ao consumidor direitos adicionais sobre o ciclo de vida dos recursos gerenciados subjacentes por meio de ações permitidas. Consulte o editor do seu aplicativo gerenciado para saber quais recursos são gerenciados pelo consumidor.

- [Limpar recursos de aplicativos gerenciados](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Log e detecção de ameaças

*Para obter mais informações, confira o [Azure Security Benchmark: log e detecção de ameaças](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: habilitar a detecção de ameaças para o gerenciamento de identidades e acesso do Azure

**Diretrizes**: o Azure ad fornece os seguintes logs de usuário que podem ser exibidos no relatório do Azure ad ou integrados com o Azure monitor, o Azure Sentinel ou outras ferramentas de monitoramento/Siem para casos de uso de análise e monitoramento mais sofisticados: entradas – o relatório de entradas fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
Logs de auditoria – Permitem o rastreio de todas as alterações feitas por vários recursos no Azure AD por meio de logs. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.
Entradas arriscadas - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.
Usuários sinalizados para riscos - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida.

A Central de Segurança do Azure também pode fornecer alertas sobre algumas atividades suspeitas, como número excessivo de tentativas de autenticação com falha e contas preteridas na assinatura. Além do monitoramento básico de higiene de segurança, o módulo de Proteção contra Ameaças da Central de Segurança do Azure também pode coletar alertas de segurança mais aprofundados de recursos de computação individuais do Azure (máquinas virtuais, contêineres, serviço de aplicativo), recursos de dados (BD SQL e armazenamento) e camadas de serviço do Azure. Esse recurso oferece visibilidade sobre anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Habilitar o Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: habilitar o registro em log para recursos do Azure

**Diretrizes**: logs de atividade, que estão disponíveis automaticamente, contêm todas as operações de gravação (put, post, Delete) para seus recursos de aplicativo gerenciado, exceto operações de leitura (Get). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e diferentes tipos de log no Azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizar o gerenciamento e a análise do log de segurança

**Orientação**: centralizar o registro em log, o armazenamento e a análise para habilitar a correlação. Para cada origem de log, verifique se você atribuiu um proprietário de dados, orientação de acesso, local de armazenamento, quais ferramentas são usadas para processar e acessar os dados e os requisitos de retenção de dados.
Verifique se você está integrando os logs de atividades do Azure ao seu registro central. Os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto de extremidade, recursos de rede e outros sistemas de segurança. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Além disso, habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados com frequência e o armazenamento do Azure para dados "frios" que são usados com menos frequência.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurar a retenção do armazenamento de log

**Orientação**: Verifique se as contas de armazenamento ou os espaços de trabalho de log Analytics usados para armazenar logs criados por seus recursos de aplicativo gerenciado têm o período de retenção de log definido de acordo com os regulamentos de conformidade de sua organização.
No Azure Monitor, você pode definir seu período de retenção de espaço de trabalho de Log Analytics de acordo com os regulamentos de conformidade de sua organização. Use o armazenamento do Azure, Data Lake ou Log Analytics contas de espaço de trabalho para armazenamento de longo prazo e arquivamento.

- [Como configurar Log Analytics período de retenção do espaço de trabalho](../../azure-monitor/logs/manage-cost-storage.md)

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: preparação – atualizar o processo de resposta a incidentes do Azure

**Diretriz**: verifique se a sua organização tem processos para responder a incidentes de segurança, atualizou esses processos para o Azure e está os exercendo regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparação – configurar a notificação de incidentes

**Diretriz**: configure as informações de contato de incidentes de segurança na Central de Segurança do Azure. Essas informações de contato serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobrir que os seus dados foram acessados por uma pessoa não autorizada ou ilegal. Você também tem opções para personalizar o alerta de incidente e a notificação em diferentes serviços do Azure de acordo com as suas necessidades de resposta a incidentes. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detecção e análise – criar incidentes com base em alertas de alta qualidade

**Diretrizes**: verifique se você tem um processo para criar alertas de alta qualidade e medir a qualidade de alertas. Isso permite que você aprenda as lições dos últimos incidentes e priorize os alertas para os analistas, de modo a não perderem tempo em falsos positivos. 

Alertas de alta qualidade podem ser criados com base na experiência com incidentes passados, fontes de comunidade validadas e ferramentas projetadas para gerar e limpar alertas, focando e correlacionando várias fontes de sinal. 

A central de segurança do Azure fornece alertas de alta qualidade em vários ativos do Azure. Use o conector de dados da ASC para transmitir os alertas para o Azure Sentinel. Com o Azure Sentinel, você pode criar regras de alertas avançadas a fim de gerar incidentes automaticamente para uma investigação. 

Exporte alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação para ajudar a identificar os riscos para os recursos do Azure. Exporte os alertas e as recomendações de modo manual ou contínuo.

- [Como configurar a exportação](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Criar um instantâneo de disco de um computador Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de disco de um computador Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e coleta de despejo de memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detecção e análise – priorizar incidentes

**Diretriz**: forneça aos analistas o contexto dos incidentes nos quais eles devem se concentrar primeiro com base na severidade do alerta e na confidencialidade do ativo. 

A Central de Segurança do Azure atribui uma severidade a cada alerta para ajudar você a priorizar os alertas que devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque os recursos usando marcas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: confinamento, erradicação e recuperação – automatizar o tratamento de incidentes

**Diretriz**: automatize tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais demoram mais para serem executadas, atrasando cada incidente e reduzindo o número de incidentes que um analista pode processar. Elas também aumentam a fadiga do analista, o que aumenta o risco de erros humanos que causam atrasos, prejudicando a capacidade dos analistas de se concentrarem efetivamente em tarefas complexas. 

Use os recursos de automação de fluxo de trabalho da Central de Segurança do Azure e do Azure Sentinel para disparar ações automaticamente ou executar um guia estratégico para responder aos alertas de segurança recebidos. O guia estratégico executa ações, como enviar notificações, desabilitar contas e isolar redes problemáticas. 

- [Configurar a automação de fluxo de trabalho na Central de Segurança](../../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças na Central de Segurança do Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas de ameaças automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gerenciamento de postura e vulnerabilidades

*Para obter mais informações, confira o [Azure Security Benchmark: gerenciamento de postura e vulnerabilidades](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: estabelecer configurações seguras para os serviços do Azure 

**Orientação**: definir guardrails de segurança para equipes de infraestrutura e DevOps, facilitando a configuração dos serviços do Azure que eles usam.

Configure Azure Policy para auditar e impor configurações de seus recursos relacionados a implantações de aplicativos gerenciados.

Você pode usar plantas do Azure para automatizar a implantação e a configuração de serviços e ambientes de aplicativos, incluindo modelos de Azure Resource Manager, atribuições de RBAC do Azure e atribuições de Azure Policy, em uma única definição de Blueprint.

- [Região de aterrissagem de escala empresarial da estrutura de adoção de nuvem](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: manter configurações seguras para os serviços do Azure

**Orientação**: Use a central de segurança do Azure para monitorar seus recursos relacionados aos aplicativos gerenciados do Azure e use os efeitos de Azure Policy [Deny] e [implantar se não existir] para manter as configurações seguras.
- [Entender Azure Policy efeitos](../../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realizar uma simulação de ataque regular

**Diretriz**: conforme necessário, realize testes de penetração ou atividades de equipe vermelhas nos seus recursos do Azure e garanta a correção de todas as conclusões de segurança críticas.
Siga as Regras de Participação no Teste de Penetração do Microsoft Cloud para garantir que os testes de penetração não violem as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Teste de penetração no Azure](../../security/fundamentals/pen-testing.md)

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="backup-and-recovery"></a>Backup e recuperação

*Para obter mais informações, confira o [Azure Security Benchmark: backup e recuperação](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: ao armazenar as definições de aplicativos gerenciados em sua própria conta de armazenamento, verifique se você pode restaurar quaisquer chaves gerenciadas pelo cliente associadas que sejam usadas para a criptografia dessa conta, que são armazenadas em Azure Key Vault.

- [Traga seu próprio armazenamento para definições de aplicativos gerenciados](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Como restaurar chaves de Key Vault no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: reduzir o risco de perda de chaves

**Orientação**: se você estiver trazendo seu próprio armazenamento para suas definições de aplicativo gerenciado, verifique se você tem medidas em vigor para evitar e se recuperar da perda de chaves usadas para criptografar suas definições. Habilite a exclusão reversível e remova a proteção no Azure Key Vault que armazena as chaves gerenciadas pelo cliente para proteger as chaves contra exclusão acidental ou mal-intencionada.  

- [Traga seu próprio armazenamento para definições de aplicativos gerenciados](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governança e estratégia

*Para obter mais informações, confira o [Azure Security Benchmark: governança e estratégia](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Recomendação da arquitetura de segurança do Azure – Armazenamento, dados e criptografia](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Conceitos básicos de segurança do Azure – Segurança, criptografia e armazenamento de dados do Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – Melhores práticas de segurança e criptografia de dados do Azure](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Gerenciamento de ativos](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Proteção de dados](../../security/benchmarks/security-controls-v2-data-protection.md)

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

- [Azure Security Benchmark – Gerenciamento de postura e vulnerabilidade](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure Security Benchmark – Segurança de rede](../../security/benchmarks/security-controls-v2-network-security.md)

- [Visão geral da segurança de rede do Azure](../../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark – Gerenciamento de identidades](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Acesso privilegiado](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor prática de segurança do Azure 11 – Arquitetura: uma estratégia de segurança unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança de gerenciamento de identidade do Azure](../../security/fundamentals/identity-management-overview.md)

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

- [Azure Security Benchmark – Log e detecção de ameaças](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Resposta a incidentes](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor prática de segurança do Azure 4 – Processo: atualizar processos de resposta a incidentes para a nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de log, de decisão de relatórios e do Adoption Framework do Azure](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala empresarial, gerenciamento e monitoramento do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)