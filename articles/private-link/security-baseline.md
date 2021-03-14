---
title: Linha de base de segurança do Azure para o link privado do Azure
description: A linha de base de segurança do link privado do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7dacec1521787e55ee4c7fd5f08b9e56d9dd4842
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735991"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Linha de base de segurança do Azure para o link privado do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../security/benchmarks/overview.md) para o link privado do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao link privado do Azure. Os **controles** não aplicáveis ao link privado do Azure foram excluídos. Para ver como o vínculo privado do Azure é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de rede virtual do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos de rede relacionados ao link privado. 

Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados por recursos de rede, como pontos de extremidade de link privado, redes virtuais e grupos de segurança de rede. 

Em Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento.

Além disso, habilite e integre dados para o Azure Sentinel ou um SIEM de terceiros, com base em requisitos de negócios organizacionais.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Registro em log e monitoramento para link privado](private-link-overview.md#logging-and-monitoring)

- [Log de diagnóstico para um grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: Habilitar logs de atividades de Azure monitor, quais operações de log são executadas em recursos de link privado, como, quem iniciou a operação, quando a operação ocorreu, o status da operação e outras informações úteis de auditoria. 

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Registro em log e monitoramento para link privado](private-link-overview.md#logging-and-monitoring)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: para logs relacionados ao link privado, defina seu período de retenção de log Analytics espaço de trabalho de acordo com as regulamentações de conformidade de sua organização no Azure monitor. Use contas de armazenamento do Azure para qualquer armazenamento de longo prazo/arquivamento de logs.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log.

Outra opção é habilitar e integrar dados ao Azure Sentinel ou um SIEM de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Compreender o workspace do Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança configurada com um espaço de trabalho log Analytics para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros com base em seus requisitos de negócios organizacionais.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: use funções de administrador internas do Azure Active Directory (Azure AD), que podem ser explicitamente atribuídas e consultadas. Execute o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use os recursos de gerenciamento de identidade e acesso da central de segurança para monitorar o número de contas administrativas.

Além disso, habilite o acesso just-in-time/apenas o suficiente usando Azure Active Directory (Azure AD) Privileged Identity Management funções privilegiadas para serviços da Microsoft e Azure Resource Manager.

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: sempre que possível, use o logon único com Azure Active Directory em vez de configurar credenciais autônomas individuais por serviço.

- [Logon único em aplicativos no Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator (MFA) do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Orientação**: Use uma Paw (estação de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos de rede do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: utilize o recurso de detecções de risco do Azure Active Directory (AD do Azure) para exibir alertas e relatórios sobre o comportamento de um usuário arriscado. Ingerir alertas de detecção de riscos da central de segurança em Azure Monitor e configure alertas/notificações personalizados usando grupos de ação.

- [Noções básicas sobre as detecções de riscos da central de segurança do Azure (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como configurar grupos de ação para alertas e notificações personalizados](../azure-monitor/alerts/action-groups.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.  

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Revisar e reconciliar regularmente o acesso do usuário

**Diretriz**: O Azure Active Directory fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use a atividade de entrada do Azure Active Directory (Azure AD), a auditoria e as fontes de log de eventos de risco para integrar com qualquer ferramenta de monitoramento/Siem.

Simplifique esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário para seus recursos de rede. 

Ingerir dados no Azure Sentinel para investigação adicional.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. 

Restrinja o nível de acesso aos recursos do Azure com seus aplicativos e ambientes corporativos com base nos requisitos de negócios. 

Controle o acesso aos recursos do Azure por meio do Azure RBAC (controle de acesso baseado em função).

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure em redes virtuais são capazes de negociar o TLS 1,2 ou superior. O link privado não interfere nos protocolos subjacentes. Use as práticas recomendadas para outras ofertas usadas pelos clientes.

Siga as recomendações da central de segurança para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a dados e recursos, caso contrário, use métodos de controle de acesso específicos de serviço.

- [Familiarize-se com uma breve descrição e a ID exclusiva das funções internas do Azure aqui](../role-based-access-control/built-in-roles.md)

Invoque o PowerShell cmd "Get-AzRoleDefinition" ou "AZ role Definition List" para recuperar uma lista de funções em seu ambiente.

Examine suas opções para controlar a exposição de seu serviço por meio da configuração de "visibilidade". em link privado. Essas configurações de visibilidade decidem se um consumidor pode se conectar ao seu serviço. 

Torne seu serviço particular para consumo de outras redes virtuais (somente permissões de RBAC do Azure). Restrinja a exposição a um conjunto limitado de assinaturas confiáveis ou torne-o público para que todas as assinaturas do Azure possam solicitar conexões no serviço de link privado.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Propriedades do serviço de vínculo privado](private-link-service-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor alertas do log de atividades para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure, como serviços de vínculo privado e pontos de extremidade. 

- [Log de diagnóstico para um grupo de segurança de rede](private-link-overview.md#logging-and-monitoring)

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos de rede, como serviços de link privado e pontos de extremidade em suas assinaturas. 

Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure usando metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear links privados e recursos relacionados. 

Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário de recursos e software aprovados do Azure para recursos de computação com base nas suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use também o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas. Isso pode ajudar em ambientes com alta segurança, como aqueles com contas de armazenamento.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources"></a>6,7: remover recursos do Azure não aprovados

**Orientação**: o cliente pode impedir a criação ou o uso de recursos com Azure Policy conforme exigido pelas políticas da empresa do cliente. Você pode implementar seu próprio processo para remover recursos não autorizados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de seus recursos de rede do Azure junto com as definições de Azure Policy internas.

Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON). Esse artefato deve ser revisado para garantir que as configurações atendam ou ultrapassem os requisitos de segurança para sua organização.

Implemente as recomendações da central de segurança como uma linha de base de configuração segura para os recursos do Azure.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use modelos de Azure Resource Manager e Azure Policy para configurar com segurança os recursos do Azure associados ao link privado e recursos relacionados.  

Os modelos de Azure Resource Manager são arquivos baseados em JavaScript Object Notation (JSON) usados para implantar recursos do Azure, todos os modelos personalizados precisarão ser armazenados e mantidos com segurança em um repositório de código. 

Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Informações sobre como criar modelos de Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreendendo os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Exemplos de modelo de Azure Resource Manager para pontos de extremidade privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager e scripts de configuração de estado desejado. 

Conceda ou negue permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure) se integrados ao Azure DevOps para seu acesso ou Active Directory se integrados ao Team Foundation Server.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. 

Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure. 

Além disso, utilize as definições de política internas relacionadas a quaisquer recursos específicos gerenciados em suas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use Azure Policy para auditar configurações de recursos do Azure. Por exemplo, Azure Policy pode ser usado para detectar recursos, que não estão configurados com um ponto de extremidade privado.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos internos de política do Azure para link privado](../governance/policy/samples/built-in-policies.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretrizes**: Use Azure Resource Manager para implantar serviços de vínculo privado, pontos de extremidade e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos, que podem ser usados como backups para restaurar pontos de extremidade de link privado e recursos relacionados. 

Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo de Azure Resource Manager para pontos de extremidade privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use Azure Resource Manager para implantar serviços de vínculo privado, pontos de extremidade e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos, que podem ser usados como backups para restaurar pontos de extremidade de link privado e recursos relacionados.  

Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.  

Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

- [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Exemplos de modelo de Azure Resource Manager para pontos de extremidade privados](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

- [Introdução à automação do Azure](../automation/automation-intro.md)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: valide a capacidade de executar periodicamente a implantação de modelos de Azure Resource Manager regularmente para uma assinatura isolada de acordo com seus requisitos de negócios. 

Além disso, valide restaurações do backup de chaves gerenciadas pelo cliente.

- [Implantar recursos com modelos ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o DevOps do Azure para armazenar e gerenciar com segurança seu código, como modelos de Azure Resource Manager. 

Conceda ou negue permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure) se integrados ao Azure DevOps para acesso a esses recursos ou no Active Directory se integrado ao Team Foundation Server.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. 

Garanta planos de resposta a incidentes escritos que definam todas as funções de pessoal e fases de manipulação ou gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade a cada alerta para ajudar a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure, especialmente aqueles que processam dados confidenciais.  

O cliente é responsável por priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. 

Examine os incidentes, poste a ocorrência para garantir que os problemas foram resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exportar alertas e recomendações da central de segurança usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. 

Além disso, use o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel, conforme exigido por suas operações de negócios.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas com aplicativos lógicos sobre alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. 

Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)