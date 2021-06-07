---
title: Linha de base de segurança do Azure para o Data Lake Analytics
description: A linha de base de segurança do Data Lake Analytics fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7569f98b1a721ecc9bd8e41a7d55cfbb80a2bc88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564612"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Linha de base de segurança do Azure para o Data Lake Analytics

Essa linha de base de segurança aplica-se às diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Data Lake Analytics. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Data Lake Analytics. Os **controles** não aplicáveis ao Data Lake Analytics foram excluídos.

 
Para ver como o Data Lake Analytics é mapeado completamente para o Azure Security Benchmark, confira o [arquivo de mapeamento completo da linha de base de segurança do Data Lake Analytics](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: use configurações de firewall para o Data Lake Analytics a fim de limitar intervalos de IP externos e permitir o acesso de seus clientes locais e serviços de terceiros. A configuração das definições do firewall está disponível por meio do portal, das APIs REST ou do PowerShell.

- [Visão geral das regras de firewall](/rest/api/datalakeanalytics/firewallrules) 

- [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar as comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: use configurações de firewall para o Data Lake Analytics a fim de limitar intervalos de IP externos e permitir o acesso de seus clientes locais e serviços de terceiros.  A configuração das definições do firewall está disponível por meio do portal, das APIs REST ou do PowerShell.

- [Visão geral das regras de firewall](/rest/api/datalakeanalytics/firewallrules) 

- [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: faça a ingestão de logs via Azure Monitor para agregar dados de segurança, como diagnóstico de "auditoria" e de "solicitações" do Data Lake Analytics. No Azure Monitor, use um Workspace do Log Analytics para consultar e executar análises. Use também Contas do Armazenamento do Azure para armazenamento de longo prazo/arquivamento, opcionalmente com recursos de segurança como armazenamento imutável e retenção imposta. 

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a uma solução de gerenciamento de evento e informações do sistema de terceiros.

- [Acessando os logs de diagnóstico do Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Como coletar logs e métricas de plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como coletar logs de host interno de Máquina Virtual do Azure com o Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md) 

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite configurações de diagnóstico para o Data Lake Analytics acessar logs de auditoria e de solicitações. Isso inclui dados como origem do evento, data, usuário, carimbo de data/hora e outros elementos úteis. 

- [Como coletar logs e métricas de plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Entender o registro em log e os diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy – Microsoft.DataLakeAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.DataLakeAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.datalakeanalytics-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: no Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de Armazenamento do Azure para armazenamento de longo prazo e arquivamento.

- [Alterar o período de retenção de dados no Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Como configurar a política de retenção de logs de conta de Armazenamento do Azure](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: analise e monitore comportamento anormal de logs e examine regularmente os resultados de seus recursos do Data Lake Analytics. Use o Workspace do Log Analytics do Azure Monitor para analisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a uma solução de gerenciamento de evento e informações do sistema de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para obter mais informações sobre o Workspace do Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: habilite configurações de diagnóstico para o Data Lake Analytics e envie logs para um Workspace do Log Analytics. Integre seu Workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução de resposta automatizada de orquestração de segurança (SOAR). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança. 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como alertar sobre dados de log do Log Analytics](../azure-monitor/alerts/tutorial-response.md)  

- [Acessando os logs de diagnóstico do Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: o Azure AD (Azure Active Directory) tem funções internas que precisam ser atribuídas explicitamente e podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o Data Lake Analytics não tem o conceito de senhas padrão, pois a autenticação é fornecida com o Azure AD (Azure Active Directory) e é protegida pelo RBAC (controle de acesso baseado em função) do Azure.

- [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas.

Você também pode habilitar um acesso just-in-time usando o Privileged Identity Management e o Azure Resource Manager do Azure AD (Azure Active Directory).

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: usar o SSO (logon único) do Azure Active Directory

**Diretrizes**: sempre que possível, use o SSO do Azure AD (Azure Active Directory) em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de identidade e acesso da Central de Segurança do Azure.

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure AD (Azure Active Directory) e siga as recomendações do gerenciamento de identidades e acesso da Central de Segurança do Azure para ajudar a proteger seus recursos do Data Lake Analytics.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como uma estação de trabalho com acesso privilegiado, ou PAW) para tarefas administrativas que exijam privilégios elevados.

- [Entender de que modo funcionam as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator do Azure AD (Azure Active Directory)](/security/compass/privileged-access-devices)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use os relatórios de segurança do Azure AD (Azure Active Directory) para gerar logs e alertas quando atividades suspeitas ou não seguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: use as localizações nomeadas do Azure AD (Azure Active Directory) para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Azure Active Directory) como o sistema de autenticação e autorização central. O RBAC (controle de acesso baseado em função) do Azure fornece controle refinado sobre o acesso de um cliente a recursos do Data Lake Analytics.

 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure AD (Azure Active Directory) fornece logs para ajudar na descoberta de contas obsoletas. Além disso, use as revisões de acesso e identidade do Azure AD para gerenciar associações de grupo, acesso aos aplicativos empresariais e atribuições de função com eficiência. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: habilite as configurações de diagnóstico para o Data Lake Analytics e o Azure AD (Azure Active Directory), enviando todos os logs para um workspace do Log Analytics. Configure os alertas desejados (como tentativas de acessar segredos desabilitados) no Log Analytics.

- [Integrar logs do Azure AD aos logs do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alertar sobre desvios de comportamento de entrada na conta

**Diretrizes**: use os recursos de proteção de identidade e proteção contra riscos do Azure AD (Azure Active Directory) para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a recursos do Data Lake Analytics. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

- [Como exibir entradas suspeitas do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas para ajudar a controlar os recursos do Data Lake Analytics que armazenam ou processam informações confidenciais. 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas separadas, grupos de gerenciamento para domínios de segurança individuais, como ambiente, confidencialidade de dados. Você pode restringir o Data Lake Analytics para controlar o nível de acesso aos recursos do Data Lake Analytics que seus aplicativos e ambientes empresariais exigirem. Quando as regras de firewall forem configuradas, somente dados de solicitação de aplicativos do conjunto especificado de redes poderão acessar os recursos do Data Lake Analytics. Você pode controlar o acesso ao Azure Data Lake Analytics por meio do RBAC (controle de acesso baseado em função) do Azure.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Gerenciar o controle de acesso baseado em função do Azure](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

- [Regras de firewall](/rest/api/datalakeanalytics/firewallrules)

- [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: os recursos de prevenção de perda de dados ainda não estão disponíveis para recursos do Azure Data Lake Analytics. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

- [Como proteger o Armazenamento do Azure](../storage/blobs/security-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: recursos do Microsoft Azure negociarão o TLS 1.2 por padrão. Verifique se todos os clientes que se conectam ao Data Lake Analytics podem negociar usando o protocolo TLS 1.2 ou superior.

- [Lista de operações de exemplo](/rest/api/datalakeanalytics/operations/list)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados ainda não estão disponíveis para recursos do Azure Data Lake Analytics. Se necessário, implemente uma solução de terceiros para fins de conformidade. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Diretrizes**: use o RBAC (controle de acesso baseado em função) do Azure para controlar como os usuários interagem com o serviço.

- [Gerenciar o RBAC do Azure](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: os dados são armazenados na conta do Data Lake Storage Gen1 padrão.  Para dados em repouso, o Data Lake Storage Gen1 dá suporte a criptografia transparente "ativada por padrão".

- [Criptografia de dados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: use o Azure Monitor com o log de atividades do Azure para criar alertas para quando alterações ocorrerem nas instâncias de produção de recursos do Azure Data Lake Analytics.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: siga as recomendações da Central de Segurança do Azure para proteger seus recursos do Azure Data Lake Analytics.

A Microsoft realiza o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Azure Data Lake Analytics.

- [Entender as recomendações da Central de Segurança do Azure](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

- [Publicação NIST – Sistema de pontuação de vulnerabilidade comum](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: use o Azure Resource Graph para consultar e descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos e assim por diante) em suas assinaturas. Certifique-se de conceder as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Azure Resource Graph Explorer, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure Data Lake Analytics. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Há informações adicionais disponíveis nos links referenciados.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos nas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Há informações adicionais disponíveis nos links referenciados

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.DataLakeAnalytics" para criar políticas personalizadas a fim de auditar ou impor a configuração do serviço do Azure Data Lake Analytics. Você também usar as definições de política internas relacionadas ao Azure Data Lake Analytics, como:

- Os logs de diagnóstico no Data Lake Analytics devem ser habilitados

Há informações adicionais disponíveis nos links referenciados

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use os efeitos [negar] e [implantar se não existir] do Azure Policy para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: use o Azure Repos para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos do Azure Resource Manager, scripts de Desired State Configuration etc. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure AD (Azure Active Directory), se integrados ao Azure DevOps, ou ao Azure AD, se integrado ao TFS.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace “Microsoft.DataLakeAnalytics” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Use os efeitos [auditoria], [negar] e [implantar se não existir] do Azure Policy para impor automaticamente as configurações para seus recursos do Azure Data Lake Analytics.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, confira [Azure Security Benchmark: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Data Lake Analytics), mas ele não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos do Azure, como Serviço de Aplicativo, Data Lake Analytics, Armazenamento de Blobs etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: os logs de trabalhos do Data Lake Analytics e a saída de dados são armazenados no serviço subjacente do Data Lake Storage Gen1.  Você pode usar vários métodos para copiar dados, incluindo ADLCopy, Azure PowerShell ou Azure Data Factory.  Você também pode usar a Automação do Azure para fazer backup de dados regularmente de maneira automática.

- [Gerenciar recursos do Armazenamento de Dados do Azure Data Lake Gen1 usando o Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Copiar dados de Azure Storage Blobs para o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

- [Visão geral da Automação do Azure](../automation/automation-intro.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: os logs de trabalhos do Data Lake Analytics e a saída de dados são armazenados no serviço subjacente do Data Lake Storage Gen1.  Você pode usar vários métodos para copiar dados, incluindo ADLCopy, Azure PowerShell ou Azure Data Factory.  

- [Gerenciar recursos do Armazenamento de Dados do Azure Data Lake Gen1 usando o Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Copiar dados de Azure Storage Blobs para o Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a restauração de dados de seus dados de backup para testar a integridade dos dados.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: os backups do Data Lake Analytics armazenados em seu Data Lake Storage Gen1 ou no Armazenamento do Azure são compatíveis com a criptografia por padrão e não podem ser desativados. Você deve tratar seus backups como dados confidenciais e aplicar os controles de proteção de dados e acesso relevantes como parte dessa linha de base.  

- [Protegendo dados armazenados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md)

- [Autorizar acesso a dados no Armazenamento do Azure](../storage/common/storage-auth.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Guia de tratamento de incidentes de segurança do computador do NIST](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade é baseada no grau de confiança da Central de Segurança nas conclusões ou na análise usada para emitir o alerta, bem como no nível de confiança em uma possível ação mal-intencionada por trás da atividade que gerou o alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretrizes**: conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano de resposta conforme necessário. 

- [Publicação do NIST – Guia para programas de teste, treinamento e exercícios para planos de TI e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" com base em alertas de segurança e recomendações a fim de proteger seus recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de participação para testes de penetração do Microsoft Cloud para verificar se os testes de penetração não estão violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)