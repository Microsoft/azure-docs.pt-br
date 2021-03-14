---
title: Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: A linha de base de segurança do banco de dados do Azure para PostgreSQL-Citus (hiperescala) fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c373bb172be01594bb5642a626cad24838b66ea2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607978"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>Linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

A linha de base de segurança do Azure para o banco de dados do Azure para PostgreSQL (Citus) contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: o Firewall do servidor do banco de dados do Azure para PostgreSQL impede todo o acesso ao nó do coordenador de hiperescala (Citus) até que você especifique quais computadores têm permissão. O firewall concede acesso ao servidor com base no endereço IP de origem de cada solicitação. Para configurar seu firewall, você deve criar regras de firewall que especifiquem intervalos de endereços IP aceitáveis. Você pode criar regras de firewall no nível de servidor.

- [Como configurar regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-firewall-rules.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretriz**: Defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados às instâncias do Banco de Dados do Azure para PostgreSQL com o Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do banco de dados do Azure para PostgreSQL.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Além disso, os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por Citus (hiperescala). No Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Métricas em hiperescala (Citus)](./concepts-hyperscale-monitoring.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: o Citus (hiperescala) fornece métricas para cada nó em um grupo de servidores. As métricas fornecem informações sobre o comportamento dos recursos de suporte. Cada métrica é emitida em uma frequência de um minuto e tem até 30 dias de histórico.

Para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho Log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Além disso, os logs de ingestão por meio de Azure Monitor para agregar dados de segurança gerados por Citus (hiperescala). No Azure Monitor, use Log Analytics espaços de trabalho para consultar e executar análises e use contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros. 

- [Métricas em hiperescala (Citus)](./concepts-hyperscale-monitoring.md)

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: dentro de Azure monitor, para o espaço de trabalho log Analytics que está sendo usado para armazenar seus logs de hiperescala (Citus), defina o período de retenção de acordo com os regulamentos de conformidade de sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Armazenando logs de recursos em uma conta de armazenamento do Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analise e monitore logs de suas instâncias de hiperescala (Citus) para comportamento anormal. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Para obter mais informações sobre o Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: você pode habilitar as configurações de diagnóstico para o Citus (hiperescala) e enviar logs para um espaço de trabalho log Analytics. Você pode configurar e receber um alerta com base nas métricas de monitoramento para seus serviços do Azure. Use o Log Analytics do Azure Monitor para revisar os logs e realizar consultas nos dados de log. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Integre seu workspace do Log Analytics ao Azure Sentinel, pois ele fornece uma solução SOAR (resposta automatizada de orquestração de segurança). Assim os guias estratégicos (soluções automatizadas) podem ser criados e usados para corrigir problemas de segurança.

- [Métricas em hiperescala (Citus)](./howto-hyperscale-alert-on-metric.md)

- [Como definir configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: Mantenha um inventário das contas de usuário que têm acesso administrativo ao plano de controle (por exemplo, portal do Azure) de suas instâncias de hiperescala (Citus). Além disso, mantenha um inventário das contas administrativas que têm acesso ao plano de dados (dentro do próprio banco de dado) de suas instâncias de hiperescala (Citus).

O Citus (hiperscale) não dá suporte ao controle de acesso baseado em função interno, mas você pode criar funções personalizadas com base em operações específicas do provedor de recursos.

Além disso, o mecanismo PostgreSQL usa funções para controlar o acesso a objetos de banco de dados, e um grupo de servidores Citus (hiperescala recém-criado) vem com várias funções predefinidas. Para modificar privilégios de usuário, use comandos PostgreSQL padrão, usando uma ferramenta como PgAdmin ou psql.

- [Entender as funções personalizadas da assinatura do Azure](../role-based-access-control/custom-roles.md) 

- [Entender as operações do provedor de recursos do banco de dados do Azure para PostgreSQL](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Entender o gerenciamento de acesso do banco de dados do Azure para PostgreSQL](./concepts-security.md#access-management)

- [Como criar usuários no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./howto-hyperscale-create-users.md)

- [Como se conectar ao PostgreSQL-Citus (hiperescala) usando psql](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretriz**: O Azure AD não tem o conceito de senhas padrão. Outros recursos do Azure que exigem uma senha forçam a criação de uma senha com requisitos de complexidade e um comprimento mínimo de senha, que difere dependendo do serviço. Você é responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que são usadas para acessar suas instâncias de hiperescala (Citus). As contas de administrador para gerenciar o recurso do Azure estão ligadas a Azure Active Directory, há também contas de administrador de servidor local que existem dentro do grupo de servidores de hiperescala (Citus) para gerenciar permissões de acesso ao banco de dados. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas dentro do Azure Active Directory.

- [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md) 

- [Como criar usuários no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./howto-hyperscale-create-users.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: para acessar o portal do Azure habilite o Azure Active Directory autenticação multifator (MFA) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretriz**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-alert-on-account-login-behavior-deviation"></a>3,7: alerta sobre o desvio do comportamento de logon da conta

**Diretriz**: Use o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: use localizações nomeadas de acesso condicional para permitir o acesso ao portal e ao Azure Resource Manager somente para agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para gerenciar recursos do PostgreSQL. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Os usuários em um grupo de servidores de hiperescala (Citus) não podem ser vinculados diretamente às contas de Azure Active Directory. Para modificar privilégios de usuário para acesso de objeto de banco de dados, use comandos PostgreSQL padrão com ferramentas como PgAdmin ou psql.

- [Modificar privilégios para funções de usuário](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [Como criar e configurar uma instância do AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Orientação**: revise e reconcilie o acesso para os usuários que têm acesso ao banco de dados local, bem como por meio de Azure Active Directory para gerenciar recursos PostgreSQL.

Para usuários com acesso para gerenciar recursos do Azure do banco de dados, examine os logs do Azure Active Directory (AD) para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso a aplicativos corporativos que podem ser usados para acessar o Citus (hiperescala) e atribuições de função. O acesso do usuário deve ser revisado regularmente, como a cada 90 dias, para garantir que apenas os usuários certos tenham acesso contínuo.

- [Examine os usuários do PostgreSQL e as funções atribuídas](https://www.postgresql.org/docs/current/database-roles.html)

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: dentro do Azure Active Directory (AD), você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com qualquer ferramenta de monitoramento/Siem. 

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory e enviando os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics. 

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use a proteção de identidade e os recursos de detecção de riscos do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas no nível do Azure Active Directory (AD). Você pode habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

Você também pode ingerir logs no Azure Sentinel para uma investigação mais aprofundada.

- [Visão geral do Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; O Sistema de Proteção de Dados do Cliente ainda não tem suporte para hiperscale (Citus).

- [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: use marcas para ajudar a rastrear instâncias de hiperescala (Citus) ou recursos relacionados que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Use uma combinação de funções administrativas e regras de firewall para isolar e limitar o acesso à rede para as instâncias do banco de dados do Azure para PostgreSQL.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Entender as regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-firewall-rules.md)

- [Entender as funções em hiperescala (Citus)](./howto-hyperscale-create-users.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: as conexões de aplicativo cliente com o nó de coordenador de hiperescala (Citus) exigem o protocolo TLS 1,2. A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure, a imposição de conexões TLS é habilitada por padrão.

Em alguns casos, aplicativos de terceiros exigem um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável (. cer) para se conectar com segurança.

- [Como configurar o TLS no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-ssl-connection-security.md)

- [Aplicativos que exigem verificação de certificado para conectividade TLS](./concepts-hyperscale-ssl-connection-security.md)



**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao plano de controle de hiperescala (Citus) (por exemplo, portal do Azure). O RBAC do Azure não afeta as permissões de usuário no banco de dados.

Para modificar privilégios de usuário no nível de banco de dados, use comandos PostgreSQL padrão, usando uma ferramenta como PgAdmin ou psql.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Como configurar o acesso do usuário com o SQL para o banco de dados do Azure para PostgreSQL](./howto-hyperscale-create-users.md)


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**:  
Pelo menos uma vez por dia, o banco de dados do Azure para PostgreSQL Citus (hiperescala) usa backups de instantâneo de arquivos e do log de transações do banco de dado. Os backups permitem que você restaure um servidor para qualquer ponto no tempo dentro do período de retenção. (No momento, o período de retenção é de 35 dias para todos os clusters.) Todos os backups são criptografados usando a criptografia AES de 256 bits. A oferta de hiperescala do PostgreSQL (Citus) usa chaves gerenciadas pela Microsoft para criptografia.

- [Entender a criptografia do PostgreSQL do Azure – backups de Citus (hiperescala)](./concepts-hyperscale-backup.md)



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção de Citus (subdimensionamento) e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: atualmente não disponível; A central de segurança do Azure ainda não dá suporte à avaliação de vulnerabilidade para o banco de dados do Azure para PostgreSQL-Citus (hiperescala).

- [Cobertura de recursos para os serviços de PaaS do Azure na central de segurança do Azure](../security-center/features-paas.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo instâncias de hiperescala (Citus) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Orientação**: aplicar marcas a instâncias de Citus (hiperescala) e outros recursos relacionados, fornecendo metadados para organizá-las logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear instâncias de Citus (hiperescala) e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s).

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)


**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s).

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”. Isso pode impedir a criação e alterações em recursos em um ambiente de alta segurança, como instâncias de Citus (instância de hiperescala) contendo informações confidenciais.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de Citus (hiperescala) com Azure Policy. Use Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias do banco de dados do Azure para PostgreSQL.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização. 

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md) 



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.  Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições personalizadas de política do Azure para suas instâncias de Citus (hiperescala) e recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/index)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.  Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)



**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretriz**: Use aliases do Azure Policy no namespace “Microsoft.DBforPostgreSQL” para criar políticas personalizadas para alertar, auditar ou impor configurações do sistema. Use a política do Azure [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para o banco de dados do Azure para instâncias do PostgreSQL e recursos relacionados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: o banco de dados do Azure para PostgreSQL-Citus (hiperescala) atualmente não dá suporte diretamente a identidades gerenciadas. Ao criar o banco de dados do Azure para o servidor PostgreSQL, você deve fornecer credenciais para um usuário administrador. Você pode criar funções de usuário adicionais na interface portal do Azure.

- [Criar um Banco de Dados do Azure para PostgreSQL – Hiperescala (Citus)](./quickstart-create-hyperscale-portal.md#create-a-hyperscale-citus-server-group)

- [Criar funções de usuário adicionais](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Não disponível no momento

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure – por exemplo, Citus (hiperescala) – no entanto, ele não é executado no conteúdo do cliente.

Verifique previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de Blobs, Banco de Dados do Azure para PostgreSQL, etc. A Microsoft não consegue acessar seus dados nessas instâncias.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: banco de dados do Azure para PostgreSQL – o Citus (hiperescala) cria automaticamente backups de cada nó e os armazena em um armazenamento com redundância local. Os backups podem ser usados para restaurar seu cluster de hiperescala (Citus) para um horário especificado.

- [Como fazer backup e restaurar no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-backup.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: pelo menos uma vez por dia, o banco de dados do Azure para PostgreSQL usa backups de instantâneo de arquivos de dados e o log de transações do banco de dado. Os backups permitem que você restaure um servidor para qualquer ponto no tempo dentro do período de retenção. Atualmente, o período de retenção é de 35 dias para todos os clusters. Todos os backups são criptografados usando a criptografia AES de 256 bits.

Em regiões do Azure que dão suporte a zonas de disponibilidade, os instantâneos de backup são armazenados em três zonas de disponibilidade. Desde que pelo menos uma zona de disponibilidade esteja online, o cluster de hiperescala (Citus) é restaurável.

- [Como fazer backup e restaurar no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-backup.md)


**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: no banco de dados do Azure para PostgreSQL, a restauração de um cluster de hiperescala (Citus) cria um novo cluster a partir dos backups dos nós originais. Você pode restaurar um cluster para qualquer ponto no tempo nos últimos 35 dias. O processo de restauração cria um novo cluster na mesma região, assinatura e grupo de recursos do Azure que o original. A nova configuração de cluster é a mesma que a configuração de cluster original: o mesmo número de nós, o número de vCores, o tamanho do armazenamento e as funções de usuário.

As configurações de firewall e os parâmetros do servidor PostgreSQL não são preservados do grupo de servidores original; Eles são redefinidos para valores padrão. O firewall impedirá todas as conexões. Você precisará ajustar manualmente essas configurações após a restauração.

- [Como fazer backup e restaurar no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-backup.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: os clusters de hiperescala (Citus) excluídos não podem ser restaurados. Se você excluir o cluster, todos os nós que pertencem ao cluster serão excluídos e não poderão ser recuperados. Para proteger a pós-implantação de recursos de cluster de exclusão acidental ou alterações inesperadas, os administradores podem aproveitar os bloqueios de gerenciamento.

- [Como fazer backup e restaurar no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](./concepts-hyperscale-backup.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário. 

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações. 

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretriz**: siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)