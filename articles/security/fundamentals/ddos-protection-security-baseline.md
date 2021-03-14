---
title: Linha de base de segurança do Azure para a proteção contra DDoS do Azure Standard
description: A linha de base de segurança padrão da proteção contra DDoS do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 51d1b3dcf66c124d113d63c3c5a46d5f41fd531d
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615288"
---
# <a name="azure-security-baseline-for-azure-ddos-protection-standard"></a>Linha de base de segurança do Azure para a proteção contra DDoS do Azure Standard

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure](../benchmarks/overview.md) para a proteção contra DDoS do Azure Standard. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis à proteção contra DDoS. Os **controles** não aplicáveis à proteção contra DDoS foram excluídos. Para ver como a proteção contra DDoS é completamente mapeada para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de proteção contra DDoS completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: Habilite as configurações de diagnóstico do Log de Atividades do Azure e envie os logs para um workspace do Log Analytics, um hub de eventos do Azure ou uma conta de armazenamento do Azure para arquivar. Os logs de atividade fornecem informações sobre as operações que foram executadas nos planos de proteção contra DDoS do Azure no nível do plano de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para suas instâncias de proteção contra DDoS do Azure.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../../azure-monitor/essentials/activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: você pode selecionar qualquer uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, usando a configuração de alerta Azure monitor. Quando as condições forem atendidas, o endereço especificado receberá um email de alerta.

Habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um Log Analytics espaço de trabalho, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Os logs de atividade fornecem informações sobre as operações que foram realizadas no cache do Azure para instâncias Redis no nível do plano de controle. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para suas instâncias de proteção contra DDoS do Azure.

- [Exibir e configurar o log de diagnóstico de DDoS](../../ddos-protection/diagnostic-logging.md)

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../../azure-monitor/essentials/activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados aos planos de proteção contra DDoS do Azure de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para cofres dos serviços de recuperação.

- [Informações sobre como acessar telemetria, logs e análise de ataque para o serviço Standard de proteção contra DDoS](../../ddos-protection/telemetry.md)

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../../azure-monitor/essentials/activity-log.md)

- [Como coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../../azure-monitor/essentials/activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: configurar alertas e a análise de ataques. A proteção contra DDoS do Azure identifica e atenua ataques de DDoS sem nenhuma intervenção do usuário.

Integre um espaço de trabalho Log Analytics ao Azure Sentinel, pois ele fornece uma solução de disparar (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança. Além disso, você pode criar alertas de log personalizados em seu espaço de trabalho do Log Analytics usando Azure Monitor.

- [Como configurar alertas para métricas de DDoS](../../ddos-protection/alerts.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../../azure-monitor/alerts/alerts-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: para trabalhar com planos de proteção contra DDoS, sua conta deve ser atribuída à função de colaborador de rede ou a uma função personalizada que é atribuída às ações apropriadas.

Além disso, Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Entender as permissões na proteção contra DDoS do Azure](../../ddos-protection/manage-permissions.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretriz**: O Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar as contas administrativas dedicadas, você pode usar as recomendações da central de segurança do Azure ou de políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seus planos de proteção contra DDoS por meio de chamadas à API.

- [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações da API de proteção contra DDoS do Azure](/rest/api/virtual-network/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite Azure Active Directory autenticação multifator e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas 

**Diretrizes**: Use uma estação de trabalho segura e gerenciada pelo Azure com a autenticação multifator (MFA) do Azure ad habilitada para fazer logon e configurar suas solicitações de sistema de proteção de dados do cliente do Azure.

- [Implantar uma estação de trabalho segura e gerenciada pelo Azure](/security/compass/privileged-access-deployment)

- [Planejar uma implantação da Autenticação Multifator do Azure AD baseada em nuvem](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use o PIM (Azure Active Directory do Azure ad Privileged Identity Management) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Além disso, use a detecção de riscos do Azure AD para exibir alertas e relatórios sobre comportamento de usuário arriscado.

- [Como implantar Privileged Identity Management](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entender a detecção de riscos do Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados do Azure ad para permitir o acesso ao portal do Azure apenas de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados do Azure AD](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central, quando aplicável. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário deve ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central, quando aplicável. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com o Azure Sentinel ou um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: para o desvio do comportamento de entrada da conta no plano de controle (por exemplo, portal do Azure), use Azure ad Identity Protection e recursos de detecção de risco para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir o logon arriscado do Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: para trabalhar com planos de proteção contra DDoS do Azure, sua conta deve ser atribuída à função de colaborador de rede ou a uma função personalizada que é atribuída a ações específicas.

- [Gerenciar o controle de acesso baseado em função do Azure (RBAC do Azure) na proteção contra DDoS do Azure](../../ddos-protection/manage-ddos-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em planos de proteção contra DDoS do Azure, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável que você crie e use Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente de acordo com uma taxonomia.

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para a proteção contra DDoS do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de seus cofres dos serviços de recuperação.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Entender Azure Policy efeitos](../../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/index)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, proteção contra DDoS do Azure), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia do Microsoft Security Response Center de um incidente do SSIRP](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Teste suas suposições sobre como seus serviços responderão a um ataque gerando tráfego em relação a seus aplicativos para simular um ataque de DDoS. Não aguarde a ocorrência de um ataque real. A Microsoft estabeleceu uma parceria com a Ixia, uma empresa de keysight, para fornecer um gerador de tráfego de autoatendimento (BreakingPoint Cloud) que permite que os clientes da proteção contra DDoS do Azure simulem o tráfego de teste de DDoS em seus pontos de extremidade públicos do Azure.

- [Microsoft Azure a validação da proteção contra DDoS](https://www.ixiacom.com/products/breakingpoint-cloud)

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel.

Selecione qualquer uma das métricas de proteção contra DDoS disponíveis para alertá-lo quando houver uma mitigação ativa durante um ataque, usando a configuração de alerta Azure Monitor. Quando as condições são atendidas, o endereço especificado recebe um email de alerta

- [Configurar alertas para métricas de proteção contra DDoS](../../ddos-protection/alerts.md)

- [Como configurar a exportação contínua](../../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração da Microsoft do Engagement para garantir que os testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../benchmarks/security-baselines-overview.md)