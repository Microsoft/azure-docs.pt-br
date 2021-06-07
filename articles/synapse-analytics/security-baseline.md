---
title: Linha de base de segurança do Azure para Synapse Analytics
description: A linha de base de segurança do Synapse Analytics fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586745"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Linha de base de segurança do Azure para Synapse Analytics

Essa linha de base de segurança aplica-se às diretrizes da [versão de avaliação de segurança do Azure, versões 1,0](../security/benchmarks/overview-v1.md) para Synapse Analytics. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Synapse Analytics. Os **controles** não aplicáveis à análise de Synapse foram excluídos.

 
Para ver como o Synapse Analytics é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de análise de Synapse completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: proteja seu SQL Server do Azure para uma rede virtual por meio de link privado. O link privado do Azure permite que você acesse os serviços de PaaS do Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft.

Como alternativa, ao se conectar ao pool do SQL do Synapse, restrinja o escopo da conexão de saída para o banco de dados SQL usando um grupo de segurança de rede. Desabilite todo o tráfego de serviço do Azure para o banco de dados SQL por meio do ponto de extremidade público, configurando permitir que os serviços do Azure Certifique-se de que nenhum endereço IP público seja permitido nas regras de firewall.

- [Entender o link privado do Azure](../private-link/private-link-overview.md)

- [Entender o link privado para o Azure Synapse SQL](../azure-sql/database/private-endpoint-overview.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: ao se conectar ao seu pool SQL dedicado e você tiver habilitado os logs de fluxo do NSG (grupo de segurança de rede), os logs serão enviados para uma conta de armazenamento do Azure para a auditoria de tráfego.

Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o SQL Synapse do Azure. A ATP detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar vários alertas, como "injeção de SQL potencial" e "acesso de localização incomum". ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

Habilite a proteção contra DDoS Standard nas redes virtuais associadas ao SQL Synapse do Azure para proteção contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Entender o ATP para o Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

- [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Visão geral do ADS](../azure-sql/database/azure-defender-for-sql.md)

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: ao se conectar ao seu pool SQL dedicado e você tiver habilitado os logs de fluxo do NSG (grupo de segurança de rede), envie os logs para uma conta de armazenamento do Azure para a auditoria de tráfego. Você também pode enviar logs de fluxo para um espaço de trabalho Log Analytics ou transmiti-los para os hubs de eventos.  Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o SQL Synapse do Azure. A ATP detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar vários alertas, como "injeção de SQL potencial" e "acesso de localização incomum". ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio do portal central de anúncios do SQL. ATP também integra alertas com a central de segurança do Azure.

- [Entender o ATP para o Azure Synapse SQL](../azure-sql/database/threat-detection-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Ao usar um ponto de extremidade de serviço para seu pool SQL dedicado, são necessários endereços IP públicos de saída para o banco de dados SQL do Azure: NSGs (grupos de segurança de rede) devem ser abertos para IPs do banco de dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando marcas de serviço NSG para o banco de dados SQL do Azure.

- [Entender as marcas de serviço com pontos de extremidade de serviço para o banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança de rede para recursos relacionados ao seu pool SQL dedicado com o Azure Policy. Você pode usar o namespace "Microsoft. SQL" para definir definições de política personalizadas ou usar qualquer uma das definições de política internas projetadas para proteção de rede do banco de dados SQL do Azure/servidor. Um exemplo de uma política de segurança de rede interna aplicável para o servidor do banco de dados SQL do Azure seria: "SQL Server deve usar um ponto de extremidade de serviço de rede virtual".

Use plantas do Azure para simplificar implantações de grande escala do Azure empacotando artefatos de ambiente-chave, como modelos de gerenciamento de recursos do Azure, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para NSG (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao seu pool SQL dedicado. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: uma política de auditoria pode ser definida para um banco de dados específico ou como uma política de servidor padrão no Azure (que hospeda o Azure Synapse). Uma política de servidor se aplica a todos os bancos de dados existentes e recém-criados no servidor.

Se a opção auditoria de servidor estiver habilitada, ela sempre será aplicada ao banco de dados. O banco de dados será auditado, independentemente das configurações de auditoria do banco de dados.

Ao habilitar a auditoria, você pode gravá-las em um log de auditoria em sua conta de armazenamento do Azure, no espaço de trabalho Log Analytics ou nos hubs de eventos.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como configurar a auditoria para seus recursos do SQL Azure](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite a auditoria no nível de servidor do SQL do Azure para seu pool SQL dedicado e escolha um local de armazenamento para os logs de auditoria (armazenamento do Azure, log Analytics ou hubs de eventos).

A auditoria pode ser habilitada no nível do banco de dados ou do servidor e sugerida para ser habilitada apenas no nível do servidor, a menos que você precise configurar um coletor de dados separado ou retenção para um banco de dado específico.

- [Como habilitar a auditoria para o banco de dados SQL do Azure](../azure-sql/database/auditing-overview.md)

- [Como habilitar a auditoria para o servidor](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Diferenças nas políticas de auditoria no nível do servidor versus no nível do banco de dados](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: ao armazenar logs relacionados ao seu pool SQL dedicado em uma conta de armazenamento, log Analytics espaço de trabalho ou hubs de eventos, defina o período de retenção de log de acordo com os regulamentos de conformidade de sua organização.

- [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Como definir parâmetros de retenção de log em um espaço de trabalho Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Capturar eventos de streaming em hubs de eventos](../event-hubs/event-hubs-capture-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Orientação**: analisar e monitorar os logs de comportamentos anormais e examinar os resultados regularmente. Use a proteção avançada contra ameaças para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para alertar sobre atividades incomuns relacionadas ao banco de dados SQL. Como alternativa, configure alertas com base em valores de métrica ou entradas do log de atividades do Azure relacionadas ao banco de dados SQL.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

- [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Como configurar alertas personalizados para o banco de dados SQL do Azure](../azure-sql/database/alerts-insights-configure-portal.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: Use a ATP (proteção avançada contra ameaças) para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para monitorar e alertar a atividade anômala. ATP faz parte da oferta do ADS (segurança de dados avançada) e pode ser acessada e gerenciada por meio de anúncios do SQL central no Portal. O ADS inclui a funcionalidade para descobrir e classificar dados confidenciais, identificandor e reduzir possíveis vulnerabilidades de banco de dado e detectar atividades anormais que podem indicar uma ameaça ao seu banco de dados.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

- [Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

- [Como habilitar a segurança de dados avançada para o banco de dado SQL do Azure](../azure-sql/database/azure-defender-for-sql.md)

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Orientação**: os usuários são autenticados com o Azure Active Directory (Azure AD) ou a autenticação do SQL.

Ao implantar o SQL do Azure pela primeira vez, você especifica um logon de administrador e uma senha associada para esse logon. Essa conta administrativa é chamada de administrador do servidor. Você pode identificar as contas de administrador de um banco de dados abrindo o portal do Azure e navegando até a guia Propriedades do servidor ou instância gerenciada. Você também pode configurar uma conta de administrador do Azure AD com permissões administrativas completas, isso será necessário se você quiser habilitar a autenticação do Azure AD.

Para operações de gerenciamento, use as funções internas do Azure que devem ser atribuídas explicitamente. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Autenticação para banco de dados SQL](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Criar contas para usuários não administrativos](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Usar uma conta do Azure AD para autenticação](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como gerenciar logons existentes e contas de administrador no SQL Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Funções internas do Azure](../role-based-access-control/built-in-roles.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Ao provisionar um pool SQL dedicado, é recomendável que você opte por integrar a autenticação ao Azure AD. Com esse método de autenticação, o usuário envia um nome de conta de usuário e solicita que o serviço use as informações de credenciais armazenadas no Azure AD.

- [Como configurar e gerenciar a autenticação do Azure AD com o Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Entender a autenticação no SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas que se conectam por meio do Azure Active Directory (Azure AD).

Para identificar as contas de administrador de um banco de dados, abra o portal do Azure e vá até a guia Propriedades do seu servidor ou instância gerenciada.

- [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md)

- [Como gerenciar logons existentes e contas de administrador no SQL Azure](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Orientação**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seu data warehouse no plano de controle (Portal do Azure) por meio de chamadas à API.

- [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações da API REST do SQL Synapse do Azure](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Entender a autenticação multifator no SQL do Azure](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho com acesso privilegiado) com autenticação multifator configurada para fazer logon e configurar recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Use a proteção avançada contra ameaças para o banco de dados SQL do Azure em conjunto com a central de segurança do Azure para detectar e alertar sobre atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A auditoria do SQL Server permite criar auditorias de servidor, que podem conter especificações de auditoria de servidor para eventos no nível do servidor e especificações de auditoria de banco de dados para eventos no nível do banco de dados. Os eventos auditados podem ser gravados nos logs de eventos ou nos arquivos de auditoria.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](../security-center/security-center-identity-access.md)

- [Examinar a proteção avançada contra ameaças e os alertas potenciais](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

- [Entender SQL Server auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso do portal e do Azure Resource Management somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Crie um administrador do Azure Active Directory (Azure AD) para o servidor do banco de dados SQL do Azure em seu pool SQL dedicado.

- [Como configurar e gerenciar a autenticação do Azure AD com o Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso dos usuários pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

- [Como usar as revisões de acesso](../active-directory/governance/access-reviews-overview.md)

- [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Configure a autenticação do Azure Active Directory (Azure AD) com o SQL do Azure e habilite as configurações de diagnóstico para contas de usuário do Azure AD, enviando logs de auditoria e logs de entrada para um espaço de trabalho do log Analytics. Configure os alertas desejados no Log Analytics.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

- [Como usar as revisões de acesso](../active-directory/governance/access-reviews-overview.md)

- [Como configurar e gerenciar a autenticação do Azure AD com o banco de dados SQL do Azure](../azure-sql/database/authentication-aad-configure.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Além disso, e ingerir dados no Azure Sentinel para uma investigação mais detalhada.

Ao usar a autenticação SQL, crie usuários de banco de dados independentes no banco de dados. Certifique-se de que você coloque um ou mais usuários de banco de dados em uma função de banco de dados personalizada com permissões específicas apropriadas para esse grupo de usuários.

- [Como exibir as entradas de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/connect-data-sources.md)

- [Entender logons e contas de usuário no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar os dados relacionados ao Azure SQL Database em seu pool SQL dedicado, o Azure sistema de proteção de dados do cliente fornece uma interface para você examinar e aprovar ou rejeitar solicitações de acesso a dados.

- [Entender Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

A classificação de descoberta de dados &amp; é incorporada ao SQL Synapse do Azure. Fornece as funcionalidades avançadas para descobrir, classificar, rotular e relatar os dados confidenciais nos seus bancos de dados.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Entender a classificação da descoberta de dados &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um grupo de segurança de rede ou firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser isolados. Usar link privado; implante sua SQL Server do Azure dentro de uma rede virtual e conecte-se com segurança usando o link privado.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como configurar o Link Privado para o Banco de Dados SQL do Azure](../azure-sql/database/private-endpoint-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: para qualquer banco de dados SQL do Azure em seu pool SQL dedicado que armazena ou processa informações confidenciais, marque o banco de dados e os recursos relacionados como confidenciais usando marcas. Configure o link privado em conjunto com as marcas de serviço do NSG (grupo de segurança de rede) em suas instâncias do banco de dados SQL do Azure para evitar o vazamento de informações confidenciais.

Além disso, a proteção avançada contra ameaças para o banco de dados SQL do Azure, o Azure SQL Instância Gerenciada e o Azure Synapse detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Como configurar o link privado e NSGs para impedir que os dados vazamento em suas instâncias do banco de dados SQL do Azure](../azure-sql/database/private-endpoint-overview.md)

- [Entender a proteção avançada contra ameaças para o banco de dados SQL do Azure](../azure-sql/database/threat-detection-overview.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: Use o recurso classificação de descoberta de dados SQL do Azure Synapse &amp; . &amp;A classificação de descoberta de dados fornece recursos avançados incorporados ao banco de dado SQL do Azure para descobrir, classificar, rotular &amp; a proteção dos dados confidenciais em seus bancos.

A classificação de descoberta de dados faz &amp; parte da oferta de segurança de dados avançada, que é um pacote unificado para recursos avançados de segurança do SQL. &amp;A classificação de descoberta de dados pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

Além disso, você pode configurar uma política de DDM (máscara de dados dinâmicos) no portal do Azure. O mecanismo de recomendações do DDM sinaliza determinados campos do banco de dados como campos potencialmente confidenciais que podem ser bons candidatos para mascaramento.

- [Como usar a descoberta e a classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Entender o mascaramento de dados dinâmicos para SQL Synapse do Azure](../azure-sql/database/dynamic-data-masking-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso do RBAC do Azure para controlar o acesso aos recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso aos bancos de dados SQL do Azure em seu pool SQL dedicado.

A autorização é controlada pelas associações de função e permissões no nível do objeto do banco de dados da conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários.

- [Como integrar o Azure SQL Server com o Azure Active Directory (Azure AD) para autenticação](../azure-sql/database/authentication-aad-overview.md)

- [Como controlar o acesso no Azure SQL Server](../azure-sql/database/logins-create-manage.md)

- [Entender a autorização e a autenticação no SQL do Azure](../azure-sql/database/logins-create-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: a TDE (Transparent Data Encryption) ajuda a proteger o SQL Synapse do Azure contra a ameaça de atividades offline mal-intencionadas criptografando os dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. No Azure, a configuração padrão para TDE é que o DEK é protegido por um certificado de servidor interno. Como alternativa, você pode usar o TDE gerenciado pelo cliente, também conhecido como suporte a Bring Your Own Key (BYOK) para TDE. Nesse cenário, o protetor de TDE que criptografa o DEK é uma chave assimétrica gerenciada pelo cliente, que é armazenada em um Azure Key Vault gerenciado e de Propriedade do cliente (sistema de gerenciamento de chaves externas baseado em nuvem do Azure) e nunca deixa o cofre de chaves.

- [Entender a Transparent Data Encryption gerenciada por serviço](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Entender a Transparent Data Encryption gerenciada pelo cliente](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [Como ativar o TDE usando sua própria chave](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias de produção de pools de SQL Synapse e outros recursos críticos ou relacionados.

Além disso, você pode configurar alertas para bancos de dados no pool Synapse do SQL usando o portal do Azure. Os alertas podem enviar a você um email ou chamar um webhook quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atinge o limite.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Como criar alertas para o SQL Synapse do Azure](../azure-sql/database/alerts-insights-configure-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: habilite a segurança de dados avançada e siga as recomendações da central de segurança do Azure sobre a execução de avaliações de vulnerabilidade em banco de dados SQL do Azure.

- [Como executar avaliações de vulnerabilidade em bancos de dados SQL do Azure](../azure-sql/database/sql-vulnerability-assessment.md)

- [Como habilitar a segurança de dados avançada](../azure-sql/database/azure-defender-for-sql.md)

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: a avaliação de vulnerabilidade é um serviço de verificação interno do Azure Synapse SQL. O serviço emprega uma base de dados de conhecimento de regras que sinaliza vulnerabilidades de segurança. Ele realça os desvios das práticas recomendadas, como configurações incorretas, permissões excessivas e dados confidenciais não protegidos.  A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal do ADS (segurança de dados avançado) do SQL central.

- [Gerenciar e exportar verificações de avaliação de vulnerabilidade no portal de anúncios do SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

A classificação de descoberta de dados &amp; é incorporada ao SQL Synapse do Azure. Fornece as funcionalidades avançadas para descobrir, classificar, rotular e relatar os dados confidenciais nos seus bancos de dados.

- [Entender a pontuação segura da central de segurança do Azure](../security-center/secure-score-security-controls.md)

- [Entender a classificação da descoberta de dados &amp;](../azure-sql/database/data-discovery-and-classification-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos relacionados ao seu pool SQL dedicado em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos do Azure, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: defina uma lista de recursos aprovados do Azure relacionados ao seu pool SQL dedicado.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para posicionar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. SQL" para criar políticas personalizadas para auditar ou impor a configuração de recursos relacionados ao seu pool SQL dedicado. Você também pode fazer uso de definições de política internas para bancos de dados/servidor do Azure, como:

- Implantar a Detecção de Ameaças em servidores SQL
- O SQL Server deve usar um ponto de extremidade de serviço de rede virtual

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: não aplicável; O SQL Synapse do Azure não tem configurações de segurança configuráveis.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Aproveite a central de segurança do Azure para executar verificações de linha de base para todos os recursos relacionados ao seu pool SQL dedicado.

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Transparent Data Encryption (TDE) com chaves gerenciadas pelo cliente no Azure Key Vault permite a criptografia da chave de criptografia de banco de dados gerada automaticamente (Dek) com uma chave assimétrica gerenciada pelo cliente, chamada protetor TDE. Isso também é geralmente chamado de suporte a BYOK (Bring Your Own Key) para Transparent Data Encryption. No cenário BYOK, o protetor de TDE é armazenado em um Azure Key Vault gerenciado e de Propriedade do cliente. Além disso, verifique se a exclusão reversível está habilitada no Azure Key Vault.

- [Como habilitar o TDE com a chave gerenciada pelo cliente de Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [Como habilitar a exclusão reversível no Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

- [Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o SQL Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: implemente o verificador de credenciais para identificar as credenciais no seu código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Synapse SQL); no entanto, ele não é executado no conteúdo do cliente.

Examine previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, SQL Server do Azure, etc. A Microsoft não pode acessar seus dados nessas instâncias.

- [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: os instantâneos do seu pool SQL dedicado são automaticamente feitos durante o dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL dedicado dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias. Observe que você também pode disparar instantâneos manualmente, se necessário.

- [Backup e restauração no pool SQL dedicado](sql-data-warehouse/backup-and-restore.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: os instantâneos de seus data warehouse são automaticamente feitos durante o dia criando pontos de restauração que estão disponíveis por sete dias. Esse período de retenção não pode ser alterado. O pool SQL dedicado dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. É possível restaurar seu data warehouse na região primária com base em qualquer um dos instantâneos tirados nos últimos sete dias. Observe que você também pode disparar instantâneos manualmente, se necessário.

Se você estiver usando uma chave gerenciada pelo cliente para criptografar sua chave de criptografia de banco de dados, verifique se o backup da chave está sendo feito.

- [Backup e restauração no pool SQL dedicado](sql-data-warehouse/backup-and-restore.md)

- [Como fazer backup de chaves de Azure Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: teste periodicamente seus pontos de restauração para garantir que seus instantâneos sejam válidos. Para restaurar um pool SQL dedicado existente de um ponto de restauração, você pode usar o portal do Azure ou o PowerShell. Teste a restauração de chaves de backup gerenciadas pelo cliente.

- [Como restaurar chaves de Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Backup e restauração no pool SQL dedicado](sql-data-warehouse/backup-and-restore.md)

- [Como restaurar um pool SQL dedicado existente](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: no banco de dados SQL do Azure, você pode configurar um banco de dados único ou em pool com uma EPD (política de retenção de backup) de longo prazo para reter automaticamente os backups de banco de dados em contêineres de armazenamento de BLOBs do Azure separados por até 10 anos. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2.

Por padrão, os dados em uma conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Se você estiver gerenciando suas próprias chaves com Key Vault, verifique se a exclusão reversível está habilitada.

- [Gerenciar a retenção de backup de longo prazo do Banco de Dados SQL do Azure](../azure-sql/database/long-term-backup-retention-configure.md)

- [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A gravidade se baseia em quão confiante a central de segurança está na localização ou na métrica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Você pode consultar a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma entidade ilegal ou não autorizada.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem gerenciada da Microsoft, serviços e aplicativos, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
