---
title: Linha de base de segurança do Azure para o Azure Synapse Analytics
description: A linha de base de segurança do Synapse Analytics fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9831f70a88aba497eb7d6a759233c3d7d7be62c6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585111"
---
# <a name="azure-security-baseline-for-azure-synapse-analytics"></a>Linha de base de segurança do Azure para o Azure Synapse Analytics

A linha de base de segurança do Azure para o Azure Synapse Analytics contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: proteja seu SQL Server do Azure para uma rede virtual por meio de link privado. O link privado do Azure permite que você acesse os serviços de PaaS do Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft.

Como alternativa, ao se conectar ao pool do SQL do Synapse, restrinja o escopo da conexão de saída para o banco de dados SQL usando um grupo de segurança de rede. Desabilite todo o tráfego de serviço do Azure para o banco de dados SQL por meio do ponto de extremidade público, configurando permitir que os serviços do Azure Certifique-se de que nenhum endereço IP público seja permitido nas regras de firewall.

* [Entender o link privado do Azure](../private-link/private-link-overview.md)

* [Entender o link privado para o Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: ao se conectar ao seu pool SQL dedicado e você tiver habilitado os logs de fluxo do NSG (grupo de segurança de rede), os logs serão enviados para uma conta de armazenamento do Azure para a auditoria de tráfego.

Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

* [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o SQL Synapse do Azure. A ATP detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar vários alertas, como "injeção de SQL potencial" e "acesso de localização incomum". ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas ao SQL Synapse do Azure para proteção contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

* [Entender o ATP para o Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

* [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

* [Visão geral do ADS](../azure-sql/database/azure-defender-for-sql.md)

* [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: ao se conectar ao seu pool SQL dedicado e você tiver habilitado os logs de fluxo do NSG (grupo de segurança de rede), envie os logs para uma conta de armazenamento do Azure para a auditoria de tráfego. Você também pode enviar logs de fluxo para um espaço de trabalho Log Analytics ou transmiti-los para os hubs de eventos. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o SQL Synapse do Azure. A ATP detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar vários alertas, como "injeção de SQL potencial" e "acesso de localização incomum". ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio do portal central de anúncios do SQL. ATP também integra alertas com a central de segurança do Azure.

* [Entender o ATP para o Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Ao usar um ponto de extremidade de serviço para seu pool SQL dedicado, são necessários endereços IP públicos de saída para o banco de dados SQL do Azure: NSGs (grupos de segurança de rede) devem ser abertos para IPs do banco de dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando marcas de serviço NSG para o banco de dados SQL do Azure.

* [Entender as marcas de serviço com pontos de extremidade de serviço para o banco de dados SQL do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#limitations)

* [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança de rede para recursos relacionados ao seu pool SQL dedicado com o Azure Policy. Você pode usar o namespace "Microsoft. SQL" para definir definições de política personalizadas ou usar qualquer uma das definições de política internas projetadas para proteção de rede do banco de dados SQL do Azure/servidor. Um exemplo de uma política de segurança de rede interna aplicável para o servidor do banco de dados SQL do Azure seria: "SQL Server deve usar um ponto de extremidade de serviço de rede virtual".

Use plantas do Azure para simplificar implantações de grande escala do Azure empacotando artefatos de ambiente-chave, como modelos de gerenciamento de recursos do Azure, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para NSG (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao seu pool SQL dedicado. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure. Você pode atualizar a sincronização de horário para suas implantações de computação.

* [Como configurar a sincronização de horário para recursos de computação do Azure](../virtual-machines/windows/time-sync.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: uma política de auditoria pode ser definida para um banco de dados específico ou como uma política de servidor padrão no Azure (que hospeda o Azure Synapse). Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

Se a opção auditoria de servidor estiver habilitada, ela sempre será aplicada ao banco de dados. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

Ao habilitar a auditoria, você pode gravá-las em um log de auditoria em sua conta de armazenamento do Azure, no espaço de trabalho Log Analytics ou nos hubs de eventos.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

* [Como configurar a auditoria para seus recursos do SQL Azure](../azure-sql/database/auditing-overview.md#server-vs-database-level)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite a auditoria no nível de servidor do SQL do Azure para seu pool SQL dedicado e escolha um local de armazenamento para os logs de auditoria (armazenamento do Azure, log Analytics ou hubs de eventos).

A auditoria pode ser habilitada no nível do banco de dados ou do servidor e sugerida para ser habilitada apenas no nível do servidor, a menos que você precise configurar um coletor de dados separado ou retenção para um banco de dado específico.

* [Como habilitar a auditoria para o banco de dados SQL do Azure](../azure-sql/database/auditing-overview.md)

* [Como habilitar a auditoria para o servidor](../azure-sql/database/auditing-overview.md#setup-auditing)

* [Diferenças nas políticas de auditoria no nível do servidor versus no nível do banco de dados](../azure-sql/database/auditing-overview.md#server-vs-database-level)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Esse parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: ao armazenar logs relacionados ao seu pool SQL dedicado em uma conta de armazenamento, log Analytics espaço de trabalho ou hubs de eventos, defina o período de retenção de log de acordo com os regulamentos de conformidade de sua organização.

* [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

* [Como definir parâmetros de retenção de log em um espaço de trabalho Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

* [Capturar eventos de streaming em hubs de eventos](../event-hubs/event-hubs-capture-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analisar e monitorar os logs de comportamentos anormais e examinar os resultados regularmente. Use a proteção avançada contra ameaças para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para alertar sobre atividades incomuns relacionadas ao banco de dados SQL. Como alternativa, configure alertas com base em valores de métrica ou entradas do log de atividades do Azure relacionadas ao banco de dados SQL.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

* [Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

* [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

* [Como configurar alertas personalizados para o banco de dados SQL do Azure](../azure-sql/database/alerts-insights-configure-portal.md?preserve-view=true&view=azps-1.4.0)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para monitorar e alertar a atividade anômala. ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio de anúncios do SQL central no Portal. O ADS inclui a funcionalidade para descobrir e classificar dados confidenciais, identificandor e reduzir possíveis vulnerabilidades de banco de dado e detectar atividades anormais que podem indicar uma ameaça ao seu banco de dados.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

* [Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

* [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

* [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; para recursos relacionados ao seu pool SQL dedicado, a solução anti-malware é gerenciada pela Microsoft na plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; nenhum log DNS é produzido por recursos relacionados ao seu pool SQL dedicado.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Orientação**: não aplicável; a auditoria de linha de comando não é aplicável ao SQL Synapse do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: os usuários são autenticados com o Azure Active Directory ou a autenticação do SQL.

Ao implantar o SQL do Azure pela primeira vez, você especifica um logon de administrador e uma senha associada para esse logon. Essa conta administrativa é chamada de administrador do servidor. Você pode identificar as contas de administrador de um banco de dados abrindo o portal do Azure e navegando até a guia Propriedades do servidor ou instância gerenciada. Você também pode configurar uma conta de administrador do Azure AD com permissões administrativas completas. isso será necessário se você quiser habilitar a autenticação Azure Active Directory.

Para operações de gerenciamento, use as funções internas do Azure que devem ser atribuídas explicitamente. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

* [Autenticação para banco de dados SQL](../azure-sql/database/security-overview.md#authentication)

* [Criar contas para usuários não administrativos](../azure-sql/database/logins-create-manage.md#create-accounts-for-non-administrator-users)

* [Usar uma conta de Azure Active Directory para autenticação](../azure-sql/database/logins-create-manage.md#create-additional-logins-and-users-having-administrative-permissions)

* [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

* [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

* [Como gerenciar logons existentes e contas de administrador no SQL Azure](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Funções internas do Azure](../role-based-access-control/built-in-roles.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory não tem o conceito de senhas padrão. Ao provisionar um pool SQL dedicado, é recomendável que você opte por integrar a autenticação com Azure Active Directory. Com esse método de autenticação, o usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure Active Directory (Azure AD).

* [Como configurar e gerenciar a autenticação Azure Active Directory com o SQL do Azure](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#active-directory-password-authentication)

* [Entender a autenticação no SQL do Azure](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas que se conectam por meio de Azure Active Directory.

Para identificar as contas de administrador de um banco de dados, abra o portal do Azure e vá até a guia Propriedades do seu servidor ou instância gerenciada.

* [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md)

* [Como gerenciar logons existentes e contas de administrador no SQL Azure](../azure-sql/database/logins-create-manage.md#existing-logins-and-user-accounts-after-creating-a-new-database)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Orientação**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seu data warehouse no plano de controle (Portal do Azure) por meio de chamadas à API.

* [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informações da API REST do SQL Synapse do Azure](./sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretriz**: Habilite a autenticação multifator (MFA) do Azure Active Directory (AD)e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

* [Entender a MFA no SQL do Azure](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho de acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente.

Use a proteção avançada contra ameaças para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para detectar e alertar sobre atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A auditoria do SQL Server permite criar auditorias de servidor, que podem conter especificações de auditoria de servidor para eventos no nível do servidor e especificações de auditoria de banco de dados para eventos no nível do banco de dados. Os eventos auditados podem ser gravados nos logs de eventos ou nos arquivos de auditoria.

* [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

* [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](../security-center/security-center-identity-access.md)

* [Examinar a proteção avançada contra ameaças e os alertas potenciais](../azure-sql/database/threat-detection-overview.md#alerts)

* [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

* [Entender SQL Server auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine?preserve-view=true&view=sql-server-ver15)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso do portal e do Azure Resource Management somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Orientação**: criar um administrador de Azure Active Directory (AD) para o servidor de banco de dados SQL do Azure em seu pool SQL dedicado.

* [Como configurar e gerenciar a autenticação do Azure AD com o Azure SQL](../azure-sql/database/authentication-aad-configure.md)

* [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use Azure Active Directory revisões de acesso para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso dos usuários pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

* [Como usar as revisões de acesso](../active-directory/governance/access-reviews-overview.md)

* [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Configure a autenticação do Azure Active Directory (AD) com o SQL do Azure e habilite as configurações de diagnóstico para Azure Active Directory contas de usuário, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho log Analytics. Configure os alertas desejados no Log Analytics.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

* [Como usar as revisões de acesso](../active-directory/governance/access-reviews-overview.md)

* [Como configurar e gerenciar a autenticação do Azure AD com o banco de dados SQL do Azure](../azure-sql/database/authentication-aad-configure.md)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Além disso, você pode embutir e ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

* [Como exibir as entradas de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../sentinel/connect-data-sources.md)

* [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar os dados relacionados ao Azure SQL Database em seu pool SQL dedicado, o Azure sistema de proteção de dados do cliente fornece uma interface para você examinar e aprovar ou rejeitar solicitações de acesso a dados.

* [Entender Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

A classificação de descoberta de dados &amp; é incorporada ao SQL Synapse do Azure. Fornece as funcionalidades avançadas para descobrir, classificar, rotular e relatar os dados confidenciais nos seus bancos de dados.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Entender a classificação da descoberta de dados &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um grupo de segurança de rede ou firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser isolados. Usar link privado; implante sua SQL Server do Azure dentro de uma rede virtual e conecte-se com segurança usando o link privado.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como configurar o Link Privado para o Banco de Dados SQL do Azure](../azure-sql/database/private-endpoint-overview.md#how-to-set-up-private-link-for-azure-sql-database)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: para qualquer banco de dados SQL do Azure em seu pool SQL dedicado que armazena ou processa informações confidenciais, marque o banco de dados e os recursos relacionados como confidenciais usando marcas. Configure o link privado em conjunto com as marcas de serviço do NSG (grupo de segurança de rede) em suas instâncias do banco de dados SQL do Azure para evitar o vazamento de informações confidenciais.

Além disso, a proteção avançada contra ameaças para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Como configurar o link privado e NSGs para impedir que os dados vazamento em suas instâncias do banco de dados SQL do Azure](../azure-sql/database/private-endpoint-overview.md)

* [Entender a proteção avançada contra ameaças para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o banco de dados SQL do Azure protege seus dados Criptografando dados em movimento com a segurança da camada de transporte. SQL Server impõe a criptografia (SSL/TLS) em todos os momentos para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de Encrypt ou TrustServerCertificate na cadeia de conexão.

* [Entender a criptografia do SQL do Azure em trânsito](../azure-sql/database/security-overview.md#information-protection-and-encryption)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: Use o recurso classificação de descoberta de dados SQL do Azure Synapse &amp; . &amp;A classificação de descoberta de dados fornece recursos avançados incorporados ao banco de dado SQL do Azure para descobrir, classificar, rotular &amp; a proteção dos dados confidenciais em seus bancos.

A classificação de descoberta de dados faz &amp; parte da oferta de segurança de dados avançada, que é um pacote unificado para recursos avançados de segurança do SQL. &amp;A classificação de descoberta de dados pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

Além disso, você pode configurar uma política de DDM (máscara de dados dinâmicos) no portal do Azure. O mecanismo de recomendações do DDM sinaliza determinados campos do banco de dados como campos potencialmente confidenciais que podem ser bons candidatos para mascaramento.

* [Como usar a descoberta e a classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

* [Entender o mascaramento de dados dinâmicos para SQL Synapse do Azure](../azure-sql/database/dynamic-data-masking-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso aos bancos de dados SQL do Azure em seu pool SQL dedicado.

A autorização é controlada pelas associações de função e permissões no nível do objeto do banco de dados da conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

* [Como integrar o Azure SQL Server com o Azure Active Directory para autenticação](../azure-sql/database/authentication-aad-overview.md)

* [Como controlar o acesso no Azure SQL Server](../azure-sql/database/logins-create-manage.md)

* [Entender a autorização e a autenticação no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: não aplicável; A Microsoft gerencia a infraestrutura subjacente para o Azure Synapse SQL e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: a TDE (Transparent Data Encryption) ajuda a proteger o SQL Synapse do Azure contra a ameaça de atividades offline mal-intencionadas criptografando os dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. No Azure, a configuração padrão para TDE é que o DEK é protegido por um certificado de servidor interno. Como alternativa, você pode usar o TDE gerenciado pelo cliente, também conhecido como suporte a Bring Your Own Key (BYOK) para TDE. Nesse cenário, o protetor de TDE que criptografa o DEK é uma chave assimétrica gerenciada pelo cliente, que é armazenada em um Azure Key Vault gerenciado e de Propriedade do cliente (sistema de gerenciamento de chaves externas baseado em nuvem do Azure) e nunca deixa o cofre de chaves.

* [Entender a Transparent Data Encryption gerenciada por serviço](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

* [Entender a Transparent Data Encryption gerenciada pelo cliente](../azure-sql/database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [Como ativar o TDE usando sua própria chave](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias de produção de pools de SQL Synapse e outros recursos críticos ou relacionados.

Além disso, você pode configurar alertas para bancos de dados no pool Synapse do SQL usando o portal do Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite.

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

* [Como criar alertas para o SQL Synapse do Azure](../azure-sql/database/alerts-insights-configure-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: habilite a segurança de dados avançada e siga as recomendações da central de segurança do Azure sobre a execução de avaliações de vulnerabilidade em seus banco de dados SQL do Azure.

* [Como executar avaliações de vulnerabilidade em seus bancos de dados SQL do Azure](../azure-sql/database/sql-vulnerability-assessment.md)

* [Como habilitar a segurança de dados avançada](../azure-sql/database/azure-defender-for-sql.md)

* [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: a avaliação de vulnerabilidade é um serviço de verificação interno do Azure Synapse SQL. O serviço emprega uma base de dados de conhecimento de regras que sinaliza vulnerabilidades de segurança. Ele realça os desvios das práticas recomendadas, como configurações incorretas, permissões excessivas e dados confidenciais não protegidos. A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal do ADS (segurança de dados avançado) do SQL central.

* [Gerenciar e exportar verificações de avaliação de vulnerabilidade no portal de anúncios do SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

A classificação de descoberta de dados &amp; é incorporada ao SQL Synapse do Azure. Fornece as funcionalidades avançadas para descobrir, classificar, rotular e relatar os dados confidenciais nos seus bancos de dados.

* [Entender a pontuação segura da central de segurança do Azure](../security-center/secure-score-security-controls.md)

* [Entender a classificação da descoberta de dados &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos relacionados ao seu pool SQL dedicado em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos do Azure, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-3.0.0)

* [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: defina uma lista de recursos aprovados do Azure relacionados ao seu pool SQL dedicado.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para posicionar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos executados em recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: qualquer recurso relacionado ao seu pool SQL dedicado que é necessário para operações de negócios, mas pode incorrer em maior risco para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e é suficientemente protegido com um firewall do Azure ou grupo de segurança de rede.

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. SQL" para criar políticas personalizadas para auditar ou impor a configuração de recursos relacionados ao seu pool SQL dedicado. Você também pode fazer uso de definições de política internas para o banco de dados/servidor do Azure, como:
- Implantar a Detecção de Ameaças em servidores SQL
- O SQL Server deve usar um ponto de extremidade de serviço de rede virtual

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

* [Documentação do Azure Repos](/azure/devops/repos/index?preserve-view=true&view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: não aplicável; O SQL Synapse do Azure não tem configurações de segurança configuráveis.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Aproveite a central de segurança do Azure para executar verificações de linha de base para todos os recursos relacionados ao seu pool SQL dedicado.

* [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Transparent Data Encryption (TDE) com chaves gerenciadas pelo cliente no Azure Key Vault permite criptografar a DEK (chave de criptografia de banco de dados) gerada automaticamente com uma chave assimétrica gerenciada pelo cliente, chamada protetor TDE. Isso também é geralmente chamado de suporte a BYOK (Bring Your Own Key) para Transparent Data Encryption. No cenário BYOK, o protetor de TDE é armazenado em um Azure Key Vault gerenciado e de Propriedade do cliente. Além disso, verifique se a exclusão reversível está habilitada no Azure Key Vault.

* [Como habilitar o TDE com a chave gerenciada pelo cliente de Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md?tabs=azure-powershell)

* [Como habilitar a exclusão reversível no Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

* [Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o SQL Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

* [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: implemente o verificador de credenciais para identificar as credenciais no seu código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Synapse SQL); no entanto, ele não é executado no conteúdo do cliente.

Examine previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, SQL Server do Azure, etc. A Microsoft não pode acessar seus dados nessas instâncias.

* [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: os instantâneos do seu pool SQL dedicado são automaticamente feitos durante o dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL dedicado dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias. Observe que você também pode disparar instantâneos manualmente, se necessário.

* [Backup e restauração no pool SQL dedicado](./sql-data-warehouse/backup-and-restore.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: os instantâneos de seus data warehouse são automaticamente feitos durante o dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL dedicado dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias. Observe que você também pode disparar instantâneos manualmente, se necessário.

Se você estiver usando uma chave gerenciada pelo cliente para criptografar sua chave de criptografia de banco de dados, verifique se o backup da chave está sendo feito.

* [Backup e restauração no pool SQL dedicado](./sql-data-warehouse/backup-and-restore.md)

* [Como fazer backup de chaves de Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: teste periodicamente seus pontos de restauração para garantir que seus instantâneos sejam válidos. Para restaurar um pool SQL dedicado existente de um ponto de restauração, você pode usar o portal do Azure ou o PowerShell. Teste a restauração de chaves de backup gerenciadas pelo cliente.

* [Como restaurar chaves de Azure Key Vault](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

* [Backup e restauração no pool SQL dedicado](./sql-data-warehouse/backup-and-restore.md)

* [Como restaurar um pool SQL dedicado existente](./sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: no banco de dados SQL do Azure, você pode configurar um banco de dados único ou em pool com uma EPD (política de retenção de backup) de longo prazo para reter automaticamente os backups de banco de dados em contêineres de armazenamento de BLOBs do Azure separados por até 10 anos. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2.

Por padrão, os dados em uma conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Se você estiver gerenciando suas próprias chaves com Key Vault, verifique se a exclusão reversível está habilitada.

* [Gerenciar a retenção de backup de longo prazo do Banco de Dados SQL do Azure](../azure-sql/database/long-term-backup-retention-configure.md)

* [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

* [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: N/D

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

* [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

* [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Você pode consultar a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal.

* [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

* [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: * [siga as regras de participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)