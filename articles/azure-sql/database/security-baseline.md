---
title: Linha de base de segurança do Azure
description: Linha de base de segurança do Azure para banco de dados SQL do Azure e Azure SQL Instância Gerenciada
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d2513d822c182d4820728123b187e995da3179fe
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806199"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Linha de base de segurança do Azure para o banco de dados SQL do Azure & SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A linha de base de segurança do Azure para o banco de dados SQL do Azure contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte [Controle de segurança: Segurança de rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteger recursos usando grupos de segurança de rede ou o Firewall do Azure em sua Rede Virtual

**Orientação**: você pode habilitar o link privado do Azure para permitir o acesso aos serviços de PaaS do Azure (por exemplo, banco de dados SQL) e serviços de cliente/parceiro hospedados no Azure por meio de um ponto de extremidade privado em sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Para permitir que o tráfego alcance o Banco de Dados SQL do Azure, use as marcas de serviço do SQL para permitir o tráfego de saída por meio de Grupos de Segurança de Rede.

As regras de rede virtual permitem que o banco de dados SQL do Azure aceite somente comunicações que são enviadas de sub-redes selecionadas dentro de uma rede virtual.

Como configurar o link privado para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Como usar pontos de extremidade de serviço de rede virtual e regras para servidores:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorar e registrar a configuração e o tráfego de VNets, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede para a sub-rede em que seu banco de dados SQL do Azure está implantado.

Para VMs (máquinas virtuais) do Azure que serão conectadas ao banco de dados SQL do Azure, habilite os logs de fluxo do NSG (grupo de segurança de rede) para o NSGs protegendo essas VMs e envie os logs para uma conta de armazenamento do Azure para a auditoria de tráfego.

Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Entenda a segurança de rede fornecida pela central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Como habilitar e usar a Análise de Tráfego: 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Entenda a segurança de rede fornecida pela central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: habilite a proteção contra DDoS Standard nas redes virtuais associadas ao banco de dados SQL do Azure para proteger contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Como configurar a proteção contra DDoS: 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Entenda a inteligência de ameaças integrada da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrar pacotes de rede e logs de fluxo

**Diretrizes**: para VMs (máquinas virtuais) do Azure que serão conectadas à instância do banco de dados SQL do Azure, habilite os logs de fluxo do NSG (grupo de segurança de rede) para o NSGs que protege essas VMs e envie os logs para uma conta de armazenamento do Azure para auditoria de tráfego. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede.

Como habilitar logs de fluxo de NSG: 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Como habilitar o observador de rede:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: implantar sistemas de detecção/prevenção de intrusões (IDS/IPS) baseados em rede

**Orientação**: habilitar a ATP (proteção avançada contra ameaças) para o banco de dados SQL do Azure.  Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. A proteção avançada contra ameaças também integra alertas com a central de segurança do Azure.

Entenda e use a proteção avançada contra ameaças para o banco de dados SQL do Azure: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Ao usar pontos de extremidade de serviço para o banco de dados SQL do Azure, são necessários endereços IP públicos de saída para o banco de dados SQL do Azure: NSGs (grupos de segurança de rede) devem ser abertos para IPs do banco de dados SQL do Azure para permitir a conectividade. Você pode fazer isso usando marcas de serviço NSG para o banco de dados SQL do Azure.

Entenda as marcas de serviço com pontos de extremidade de serviço para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

Entender e usar marcas de serviço:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança de rede para seu banco de dados SQL do Azure com Azure Policy. Você pode usar o namespace "Microsoft. SQL" para definir definições de política personalizadas ou usar qualquer uma das definições de política internas projetadas para proteção de rede do servidor. Um exemplo de uma diretiva de segurança de rede interna aplicável para um servidor seria: "o banco de dados SQL deve usar um ponto de extremidade de serviço de rede virtual".

Use plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de gerenciamento de recursos do Azure, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar um Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para NSG (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

Como criar e usar marcas: 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao seu servidor. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

Como exibir e recuperar eventos do Log de Atividades do Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

como criar alertas no Azure Monitor: 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte [Controle de segurança: registro em log e monitoramento](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure. Você pode atualizar a sincronização de horário para suas implantações de computação.

Como configurar a sincronização de horário para recursos de computação do Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: habilite a auditoria do banco de dados SQL do Azure para acompanhar eventos de banco de dados e gravá-los em um log de auditoria em sua conta de armazenamento do azure, log Analytics espaço de trabalho ou hubs de eventos.

Além disso, você pode transmitir a telemetria do diagnóstico do Azure SQL no Análise de SQL do Azure, uma solução de nuvem que monitora o desempenho do banco de dados SQL do Azure e do SQL Instância Gerenciada do Azure em escala e em várias assinaturas. Pode ajudá-lo a coletar e visualizar as métricas de desempenho do Banco de Dados SQL do Azure e tem uma inteligência interna para solução de problemas de desempenho.

Como configurar a auditoria para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Como coletar logs e métricas de plataforma com o Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

Como transmitir diagnósticos para Análise de SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite a auditoria no servidor e escolha um local de armazenamento para os logs de auditoria (armazenamento do Azure, log Analytics ou Hub de eventos).

Como habilitar a auditoria para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; Esse parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: ao armazenar os logs do banco de dados SQL do Azure em um espaço de trabalho log Analytics, defina o período de retenção de log de acordo com os regulamentos de conformidade da sua organização.

Como definir parâmetros de retenção de log:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Orientação**: analisar e monitorar os logs de comportamentos anormais e examinar os resultados regularmente. Use a proteção avançada contra ameaças da central de segurança do Azure para alertar sobre atividades incomuns relacionadas à instância do banco de dados SQL do Azure. Como alternativa, configure alertas com base em valores de métrica ou entradas do log de atividades do Azure relacionadas às instâncias do banco de dados SQL do Azure.

Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como configurar alertas personalizados para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: habilitar alertas para atividade anormal

**Diretrizes**: Use a central de segurança do Azure proteção avançada contra ameaças para o banco de dados SQL do Azure para monitoramento e alertas na atividade anômala. Habilite o Azure defender para SQL para seus bancos de dados SQL. O Azure defender para SQL inclui a funcionalidade de identificando e a mitigação de vulnerabilidades de banco de dados potenciais e a detecção de atividades anormais que podem indicar uma ameaça ao seu banco de dados.

Entenda a proteção avançada contra ameaças e os alertas para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Como habilitar o Azure defender para SQL para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Como gerenciar alertas na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; para o banco de dados SQL do Azure, a solução anti-malware é gerenciada pela Microsoft na plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; O log DNS não é aplicável ao banco de dados SQL do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Orientação**: não aplicável; a auditoria de linha de comando não é aplicável ao banco de dados SQL do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte [Controle de segurança: Identidade e controle de acesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Como obter uma função de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Como obter membros de uma função de diretório no Azure AD com o PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory não tem o conceito de senhas padrão. Ao provisionar uma instância do banco de dados SQL do Azure, é recomendável que você opte por integrar a autenticação com Azure Active Directory.

Como configurar e gerenciar a autenticação Azure Active Directory com o SQL do Azure:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Entenda a identidade e o acesso à Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Orientação**: não aplicável; Embora você possa configurar Azure Active Directory autenticação para se integrar ao banco de dados SQL do Azure, não há suporte para o logon único.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a MFA (autenticação multifator) do Azure AD (Azure Active Directory) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

Como habilitar a MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Como monitorar identidade e acesso na Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use uma estação de trabalho de acesso privilegiado (Paw) com a autenticação multifator MFA configurada para fazer logon e configurar os recursos do Azure.

Saiba mais sobre estações de trabalho com acesso privilegiado: 

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Como habilitar a MFA no Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrar e alertar sobre atividades suspeitas em contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente.

Use a proteção avançada contra ameaças para o banco de dados SQL do Azure para detectar atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

Como identificar usuários do Azure AD sinalizados para atividades arriscadas:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Examine a proteção avançada contra ameaças e os alertas potenciais:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso do portal e do Azure Resource Management somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

Como configurar localizações nomeadas no Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Orientação**: criar um administrador do Azure Active Directory (AD do Azure) para seu servidor.

Como configurar e gerenciar a autenticação Azure Active Directory com o SQL do Azure:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Como criar e configurar uma instância do Azure AD:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso dos usuários pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

Como usar as revisões de acesso de identidade do Azure: 

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorar tentativas de acessar contas desativadas

**Diretrizes**: Configure a autenticação do Azure Active Directory (Azure AD) com o SQL do Azure e crie configurações de diagnóstico para Azure Active Directory contas de usuário, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho log Analytics. Configure os alertas desejados no espaço de trabalho Log Analytics.

Como configurar e gerenciar a autenticação Azure Active Directory com o SQL do Azure:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Como integrar os logs de atividades do Azure ao Azure Monitor: 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use a proteção de identidade do Azure Active Directory (AD do Azure) e as detecções de risco para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Além disso, você pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

Como exibir as entradas de risco do Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Como configurar e habilitar políticas de risco de proteção de identidade:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar dados do cliente, o Azure sistema de proteção de dados do cliente fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente.

Entender Sistema de Proteção de Dados do Cliente:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte [Controle de segurança: proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por vnet/sub-rede, marcados adequadamente e protegidos em um NSG ou firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser isolados. Usar link privado; implante o banco de dados SQL do Azure dentro de sua vnet e conecte-se de forma privada usando pontos de extremidade privados.

Como criar assinaturas adicionais do Azure: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: 

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Como configurar o link privado para o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: para bancos de dados no banco de dados SQL do Azure armazenando ou processando informações confidenciais, marque o banco de dados e os recursos relacionados como confidenciais usando marcas. Configure o link privado em conjunto com as marcas de serviço do grupo de segurança de rede em suas instâncias do banco de dados SQL do Azure para evitar o vazamento de informações confidenciais.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

Como configurar o link privado e NSGs para impedir que os dados vazamento em suas instâncias do banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Entender a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o banco de dados SQL do Azure protege seus dados Criptografando dados em movimento com a segurança da camada de transporte. O banco de dados SQL impõe a criptografia (SSL/TLS) em todos os momentos para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de Encrypt ou TrustServerCertificate na cadeia de conexão.

Entender a criptografia do SQL do Azure em trânsito:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: Use o recurso de descoberta e classificação de dados do banco de dados SQL do Azure. A descoberta e a classificação de dados fornecem recursos avançados incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular &amp; a proteção de informações confidenciais em seus bancos.

Como usar a descoberta e a classificação de dados para o banco de dado SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Diretrizes**: Use Azure Active Directory (AD do Azure) para autenticar e controlar o acesso às instâncias do banco de dados SQL do Azure.

Como integrar o banco de dados SQL do Azure com o Azure Active Directory para autenticação:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Como controlar o acesso no banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: a Microsoft gerencia a infraestrutura subjacente para o banco de dados SQL do Azure e implementou controles estritos para evitar a perda ou a exposição dos clientes.

Entender a proteção de dados do cliente no Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: TDE (Transparent Data Encryption) ajuda a proteger o banco de dados SQL do Azure, o SQL instância gerenciada do Azure e o Azure data warehouse contra a ameaça de atividades offline mal-intencionadas criptografando os dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, o TDE está habilitado para todos os bancos de dados implantados recentemente no banco de dados SQL e no SQL Instância Gerenciada. A chave de criptografia TDE pode ser gerenciada pela Microsoft ou pelo cliente.

Como gerenciar a Transparent Data Encryption e usar suas próprias chaves de criptografia:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do banco de dados SQL do Azure e outros recursos críticos ou relacionados.

Como criar alertas para eventos do log de atividades do Azure: 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte [Controle de segurança: gerenciamento de vulnerabilidades](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: habilitar o Azure defender para SQL para o banco de dados SQL do Azure e seguir as recomendações da central de segurança do Azure sobre a execução de avaliações de vulnerabilidade em seus servidores.

Como executar avaliações de vulnerabilidade no banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Como habilitar o Azure defender para SQL:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Implantar uma solução automatizada de gerenciamento de patch de software de terceiros

**Orientação**: não aplicável; Esse parâmetro de comparação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: habilitar verificações recorrentes periódicas para suas instâncias do banco de dados SQL do Azure; Isso irá configurar uma avaliação de vulnerabilidade para executar automaticamente uma verificação no banco de dados uma vez por semana. Um resumo dos resultados da verificação será enviado aos endereços de email fornecidos. Compare os resultados para verificar se as vulnerabilidades foram corrigidas.

Como exportar um relatório de avaliação de vulnerabilidade na central de segurança do Azure:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

Entender a pontuação segura da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte [Controle de segurança: inventário e gerenciamento de ativos](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usar a descoberta de ativos do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo o banco de dados SQL do Azure) em suas assinaturas.  Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

Como criar consultas com o Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Como exibir suas assinaturas do Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Entenda o RBAC do Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Como criar assinaturas adicionais do Azure: 

https://docs.microsoft.com/azure/billing/billing-create-subscription

Como criar Grupos de Gerenciamento: 

https://docs.microsoft.com/azure/governance/management-groups/create

Como criar e usar marcas:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: manter um inventário de recursos e títulos de software aprovados do Azure

**Diretrizes**: definir a lista de recursos aprovados do Azure e o software aprovado para seus recursos de computação

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como criar consultas com o Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoramento da Central de Segurança do Azure**: Não aplicável

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

Como configurar e gerenciar o Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Como negar um tipo de recurso específico com o Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-implement-approved-application-list"></a>6.10: implementar lista de aplicativos aprovados

**Orientação**: não aplicável; Essa recomendação destina-se a aplicativos executados em recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Limitar a capacidade dos usuários de interagir com o Azure Resource Manager por meio de scripts

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativo ou a recursos de computação que hospedam aplicativos Web ou de área de trabalho.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte [Controle de segurança: configuração segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: Use Azure Policy ou as recomendações da central de segurança do Azure para o banco de dados SQL do Azure para manter as configurações de segurança para todos os recursos do Azure.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender os efeitos do Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implantar ferramentas de gerenciamento de configuração do sistema

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. SQL" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar o Azure Policy: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar o monitoramento automatizado de configuração para serviços do Azure

**Orientação**: Aproveite a central de segurança do Azure para executar verificações de linha de base do banco de dados SQL do Azure.

Como corrigir recomendações na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use Azure Key Vault para armazenar chaves de criptografia para Transparent Data Encryption do banco de dados SQL do Azure (TDE).

Como proteger dados confidenciais que estão sendo armazenados no Azure SQL Database e armazenar as chaves de criptografia no Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Azure Key Vault, sem nenhuma credencial em seu código.

Tutorial: usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o SQL do Azure:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Como configurar identidades gerenciadas: 

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: implemente o verificador de credenciais para identificar as credenciais no seu código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte [Controle de segurança: defesa contra malwares](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure app Service), no entanto, ele não é executado no conteúdo do cliente.

Pré-verificar qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, banco de dados SQL do Azure, etc. A Microsoft não pode acessar seus dados nessas instâncias.

Entenda o Microsoft antimalware para os serviços de nuvem do Azure e máquinas virtuais: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft lida com o anti-malware para a plataforma subjacente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte [Controle de segurança: recuperação de dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: para proteger seu negócio contra perda de dados, o banco de dado SQL do Azure cria automaticamente backups completos semanalmente, backups de banco de dados diferenciais a cada 12 horas e backups de log de transações a cada 5-10 minutos. Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todas as camadas de serviço. Todas as camadas de serviço, exceto suporte Básico período de retenção de backup configurável para restauração pontual, até 35 dias.

Para atender aos diferentes requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção.

Entenda backups e continuidade de negócios com o banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: realizar backups completos do sistema e fazer backup das chaves gerenciadas pelo cliente

**Orientação**: o banco de dados SQL do Azure cria automaticamente os backups de banco de dados que são mantidos entre 7 e 35 dias e usa o armazenamento com redundância geográfica com acesso de leitura do Azure (ra-grs) para garantir que eles sejam preservados, mesmo que o Data Center não esteja disponível. Esses backups são criados automaticamente. Se necessário, habilite backups com redundância geográfica de longo prazo para seus bancos de dados SQL do Azure.

Se estiver usando chaves gerenciadas pelo cliente para Transparent Data Encryption, verifique se as chaves estão sendo submetidas a backup.

Entenda os backups no banco de dados SQL do Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Como fazer backup de chaves do Key Vault no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste o conteúdo de restauração em uma VLAN isolada. Teste a restauração de chaves de backup gerenciadas pelo cliente.

Como restaurar chaves do Key Vault no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Como recuperar backups de banco de dados SQL do Azure usando a restauração pontual:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: habilite a exclusão reversível em Azure Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

Como habilitar a exclusão reversível no Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte [Controle de segurança: resposta a incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Orientação**: Verifique se há planos de resposta a incidentes escritos que definem funções de pessoal, bem como fases de manipulação/gerenciamento de incidentes.

Como configurar automações de fluxo de trabalho na Central de Segurança do Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que você participa de cada alerta, para que, quando um recurso for comprometido, você possa acessá-lo imediatamente. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Alertas de segurança na central de segurança do Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

Você pode consultar a publicação do NIST: guia para testar, treinar e preparar programas para planos de ti e recursos:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretrizes**: as informações de contato de incidente de segurança serão usadas pela Microsoft para entrar em contato com você se o MSRC (Microsoft Security Response Center) descobre que seus dados foram acessados por uma entidade ilegal ou não autorizada.

Como definir o contato da segurança da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

Como configurar a exportação contínua:

https://docs.microsoft.com/azure/security-center/continuous-export

Como transmitir alertas para o Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretriz**: use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte [Controle de segurança: testes de penetração e exercícios de Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: realizar testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as constatações de segurança críticas em 60 dias

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Você pode encontrar mais informações sobre a estratégia e a execução da Microsoft de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos da nuvem gerenciada da Microsoft, aqui: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Consulte o [Parâmetro de segurança do Azure](../../security/benchmarks/overview.md)
- Saiba mais sobre as [Linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)