---
title: Linha de base de segurança do Azure para Azure Data Factory
description: Linha de base de segurança do Azure para Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 387c5e70d50e53f922530cfb1fa91494fa7cc624
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376302"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Linha de base de segurança do Azure para Azure Data Factory

A linha de base de segurança do Azure para Azure Data Factory contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, confira a [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: ao criar um Azure-SSIS Integration Runtime (ir), você tem a opção de associá-lo a uma rede virtual. Isso permitirá que Azure Data Factory crie determinados recursos de rede, como um NSG (grupo de segurança de rede) e um balanceador de carga. Você também tem a capacidade de fornecer seu próprio endereço IP público estático ou ter Azure Data Factory criar um para você. No NSG que é criado automaticamente pelo Azure Data Factory, a porta 3389 está aberta para todo o tráfego por padrão. Bloqueie isso para garantir que somente os administradores tenham acesso.

Self-Hosted IRs pode ser implantado em um computador local ou em uma máquina virtual do Azure dentro de uma rede virtual. Verifique se sua implantação de sub-rede de rede virtual tem um NSG configurado para permitir somente acesso administrativo. A Azure-SSIS IR não permitiu a saída da porta 3389 por padrão na regra de firewall do Windows em cada nó IR para proteção. Você pode proteger seus recursos configurados pela rede virtual associando um NSG à sub-rede e Configurando regras estritas.

Onde o link privado está disponível, use pontos de extremidade privados para proteger os recursos que estão sendo vinculados ao pipeline de Azure Data Factory, como o SQL Server do Azure. Com o link privado, o tráfego entre sua rede virtual e o serviço atravessa a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

* [Como criar um Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [Como criar e configurar um IR de infravermelho auto-hospedado](./create-self-hosted-integration-runtime.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Ingressar um Azure-SSIS IR em uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [Entender o link privado do Azure](../private-link/private-link-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede para a rede virtual e o grupo de segurança de rede associado à sua implantação do Integration Runtime.

Além disso, habilite os logs de fluxo do NSG (grupo de segurança de rede) para o NSG protegendo sua implantação do Integration Runtime e envie os logs para uma conta de armazenamento do Azure para a auditoria de tráfego.

Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

* [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilite a proteção contra DDoS Standard nas redes virtuais associadas à sua implantação de Integration Runtime para proteção contra ataques de negação de serviço distribuídos. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

* [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: habilitar os logs de fluxo do NSG (grupo de segurança de rede) para o NSG protegendo sua implantação do Integration Runtime e enviar logs para uma conta de armazenamento do Azure para auditoria de tráfego.

Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

* [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: se você quiser inspecionar o tráfego de saída de Azure-SSIS ir, poderá rotear o tráfego iniciado de Azure-SSIS ir para o dispositivo de firewall local por meio do túnel de força do ExpressRoute do Azure ou para uma NVA (solução de virtualização de rede) do Azure Marketplace que dá suporte a recursos de IDS/IPS. Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado.

* [Ingressar um Azure-SSIS Integration Runtime em uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: não aplicável; Essa recomendação é destinada ao serviço de aplicativos do Azure ou a recursos de computação que hospedam aplicativos Web.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede no NSG (grupo de segurança de rede) ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, DataFactoryManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego de entrada para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

* [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

* [Entender Azure Data Factory marcas de serviço específicas](./join-azure-ssis-integration-runtime-virtual-network.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para configurações de rede e recursos de rede associados às suas instâncias do Azure data Factory com Azure Policy. Use Azure Policy aliases nos namespaces "Microsoft. datafactory" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias de Azure Data Factory. Você também pode fazer uso de definições de política internas relacionadas à rede ou às instâncias do Azure data Factory, como:
- A Proteção contra DDoS Standard deve ser habilitada

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network) 

* [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para recursos relacionados à segurança de rede e ao fluxo de tráfego para suas instâncias de Azure data Factory para fornecer metadados e organização lógica.

Use qualquer uma das definições do Azure Policy integrado relacionadas à marcação, como “Exigir marca e seu valor” para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às suas instâncias de Azure data Factory. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como Azure data Factory para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure data Factory. Em Azure Monitor, você pode consultar o espaço de trabalho do Log Analytics que está configurado para receber seus logs de atividade do Azure Data Factory. Use Contas do Armazenamento do Azure para armazenamento de log de longo prazo/arquivamento ou Hubs de Eventos para exportar dados para outros sistemas.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros. Você também pode integrar Azure Data Factory com o Git para aproveitar vários benefícios de controle do código-fonte, como a capacidade de controlar/auditar alterações e a capacidade de reverter alterações que introduzem bugs.

* [Como definir as configurações de diagnóstico](../azure-monitor/platform/diagnostic-settings.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Controle do código-fonte no Azure Data Factory](./source-control.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, hubs de eventos do Azure ou conta de armazenamento do Azure para arquivamento. Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Use as configurações de diagnóstico para configurar os logs de diagnóstico para recursos que não são de computação no Azure Data Factory, como métricas e dados de execução de pipeline. Azure Data Factory armazena dados de execução de pipeline por 45 dias. Para manter esses dados por um período de tempo maior, salve os logs de diagnóstico em uma conta de armazenamento para auditoria ou inspeção manual e especifique o tempo de retenção em dias. Você também pode transmitir os logs para os hubs de eventos do Azure ou enviar os logs para um espaço de trabalho Log Analytics para análise.

* [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/platform/activity-log.md)

* [Entender Azure Data Factory logs de diagnóstico](./monitor-using-azure-monitor.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: se você estiver executando o Integration Runtime em uma VM (máquina virtual) do Azure, poderá usar Azure monitor para coletar dados da máquina virtual. A instalação da extensão de VM Log Analytics permite que Azure Monitor colete dados de suas VMs do Azure. A central de segurança do Azure pode fornecer monitoramento de log de eventos de segurança para máquinas virtuais. Dado o volume de dados gerado pelo log de eventos de segurança, ele não é armazenado por padrão.

Se sua organização quiser manter os dados do log de eventos de segurança, ele poderá ser armazenado em uma camada de coleta de dados, no ponto em que pode ser consultado em Log Analytics.

* [Como coletar dados de máquinas virtuais do Azure no Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Habilitando a coleta de dados na central de segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: Habilitar configurações de diagnóstico para Azure data Factory. Se optar por armazenar logs em um workspace do Log Analytics, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas do Armazenamento do Azure para armazenamento de longo prazo/arquivamento.

* [Como habilitar os logs de diagnóstico no Azure Data Factory](./monitor-using-azure-monitor.md)

* [Como definir parâmetros de retenção de log para workspaces do Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: habilite as configurações de diagnóstico para Azure data Factory e envie logs para um espaço de trabalho log Analytics. Use Log Analytics para analisar e monitorar seus logs para comportamento anormal e examinar os resultados regularmente. Certifique-se também de habilitar as configurações de diagnóstico para quaisquer repositórios de dados relacionados às implantações do Azure Data Factory. Consulte a linha de base de segurança de cada serviço para obter orientação sobre como habilitar as configurações de diagnóstico.

Se você estiver executando o Integration Runtime em uma VM (máquina virtual) do Azure, habilite as configurações de diagnóstico para a VM também.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

* [Esquema de Log Analytics](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [Como coletar dados de uma máquina virtual do Azure com Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: você pode gerar alertas sobre as métricas com suporte no data Factory acessando a &amp; seção métricas de alertas em Azure monitor.

Defina as configurações de diagnóstico para Azure Data Factory e envie logs para um espaço de trabalho Log Analytics. No workspace do Log Analytics, configure alertas para quando um conjunto predefinido de condições ocorrer. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

Além disso, certifique-se de habilitar as configurações de diagnóstico para serviços relacionados aos armazenamentos de dados. Você pode consultar a linha de base de segurança de cada serviço para obter diretrizes.

* [Alertas no Azure Data Factory](./monitor-visually.md#alerts)

* [Página de todas as métricas com suporte](../azure-monitor/platform/metrics-supported.md)

* [Como configurar alertas no workspace do Log Analytics](../azure-monitor/platform/alerts-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, poderá usar o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais e configurar suas máquinas virtuais para registrar eventos em uma conta de armazenamento do Azure. Configure um espaço de trabalho Log Analytics para ingerir os eventos das contas de armazenamento e criar alertas quando apropriado. Siga as recomendações na central de segurança do Azure: "aplicativos de computação &amp; ".

* [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Como habilitar o monitoramento em nível de convidado para máquinas virtuais](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Orientação**: não aplicável; Azure Data Factory não processa nem produz logs relacionados ao DNS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Diretrizes**: se você estiver executando o Integration Runtime em uma VM (máquina virtual) do Azure, poderá habilitar o log de auditoria de linha de comando. A central de segurança do Azure fornece monitoramento de log de eventos de segurança para VMs do Azure. A central de segurança provisiona a Microsoft Monitoring Agent em todas as VMs do Azure com suporte e quaisquer novas criadas se o provisionamento automático estiver habilitado ou se você puder instalar o agente manualmente. O agente habilita o evento de criação de processo 4688 e o campo CommandLine dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da Central de Segurança.

* [Coleta de dados na Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: em Azure data Factory, certifique-se de acompanhar e reconciliar o acesso do usuário regularmente. Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções colaborador ou proprietário, ou um administrador da assinatura do Azure.

Além disso, no nível do locatário, Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos que têm acesso administrativo ao plano de controle de suas instâncias de Azure Data Factory.

Embora o Azure AD seja o método recomendado para administrar o acesso do usuário, tenha em mente que, se você estiver executando Integration Runtime em uma VM (máquina virtual) do Azure, sua VM também poderá ter contas locais. As contas locais e de domínio devem ser examinadas e gerenciadas, normalmente com uma superfície mínima. Além disso, aconselhamos que o Privileged Identity Manager seja revisado pelo recurso just in time para reduzir a disponibilidade de permissões administrativas.

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

* [Informações sobre o Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

* [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [Informações para contas locais](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o Azure data Factory usa o Azure Active Directory (AD) para fornecer acesso ao portal do Azure, bem como ao console do Azure data Factory. No entanto, o Azure AD não tem o conceito de senhas padrão; você é responsável por alterar ou não permitir senhas padrão para qualquer aplicativo personalizado ou de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas para acesso ao plano de controle do Azure (Portal do Azure), bem como ao console do Azure data Factory. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas no Azure AD.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:
- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, as contas de administrador nas máquinas virtuais do Azure também poderão ser configuradas com o PIM (Privileged Identity Manager) do Azure. O Azure Privileged Identity Manager fornece várias opções, como a elevação just-in-time, a autenticação multifator e as opções de delegação para que as permissões só estejam disponíveis para quadros de tempo específicos e exijam uma segunda pessoa para aprovar.

* [Entender a identidade e o acesso da central de segurança do Azure](../security-center/security-center-identity-access.md)

* [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Informações sobre o Privileged Identity Manager](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que seu aplicativo ou função pode usar para acessar e interagir com seus cofres de serviços de recuperação.

* [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Informações da API dos serviços de recuperação do Azure](/rest/api/recoveryservices/)

* [Informações sobre a API REST para Azure Data Factory](/rest/api/datafactory/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretriz**: habilite a autenticação multifator (MFA) do Azure Active Directory e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretriz**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

Se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, poderá, além disso, integrar sua VM ao Azure Sentinel. O Microsoft Azure Sentinel é uma solução escalonável e nativa da nuvem que oferece SIEM (Gerenciamento de Eventos de Informações de Segurança) e SOAR (Resposta Automatizada para Orquestração de Segurança). O Azure Sentinel oferece análise inteligente de segurança e inteligência contra ameaças em toda a empresa, fornecendo uma única solução para detecção de alertas, visibilidade de ameaças, procura proativa e resposta a ameaças.

* [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

* [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

* [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: um data Factory pode ser associado a uma identidade gerenciada para recursos do Azure que representa o data Factory específico. Você pode usar essa identidade gerenciada para autenticação do banco de dados SQL do Azure. O factory designado pode acessar dados do banco de dados e copiá-los para o banco de dados usando essa identidade.

Se você estiver executando seu Integration Runtime (IR) em uma máquina virtual do Azure, poderá usar identidades gerenciadas para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código. Seu código em execução em uma máquina virtual pode usar a identidade gerenciada para solicitar tokens de acesso para serviços que dão suporte à autenticação do Azure AD.

* [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

* [Copiar e transformar dados no banco de dados SQL do Azure usando Azure Data Factory](./connector-azure-sql-database.md)

* [Como configurar e gerenciar a autenticação Azure Active Directory com o banco de dados SQL do Azure](../azure-sql/database/authentication-aad-configure.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

Se estiver executando a integração de tempo de execução em uma máquina virtual do Azure, você precisará examinar os grupos de segurança e os usuários locais para garantir que não haja contas inesperadas que possam comprometer o sistema.

* [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

* [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

* [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (AD), auditoria e risco de registro de eventos, que permitem a integração com qualquer ferramenta Siem/Monitoring. Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

Se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, integre a VM ao Azure sentinela. O Microsoft Azure Sentinel é uma solução escalonável e nativa da nuvem que oferece SIEM (Gerenciamento de Eventos de Informações de Segurança) e SOAR (Resposta Automatizada para Orquestração de Segurança). O Azure Sentinel oferece análise inteligente de segurança e inteligência contra ameaças em toda a empresa, fornecendo uma única solução para detecção de alertas, visibilidade de ameaças, procura proativa e resposta a ameaças.

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](../event-hubs/authorize-access-azure-active-directory.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para seus recursos de Azure data Factory, como o banco de dados SQL do Azure ou máquinas virtuais do Azure. Para o desvio do comportamento de logon da conta no plano de controle (o portal do Azure), use Azure AD Identity Protection e recursos de detecção de risco para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

* [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Configurar e gerenciar autenticação do Azure Active Directory com SQL](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [Habilitar a autenticação do Azure Active Directory para o Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar dados do cliente, o Azure sistema de proteção de dados do cliente fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. Observe que, embora o Lockbox do Azure não esteja disponível para Azure Data Factory em si, a Lockbox do Azure dá suporte ao banco de dados SQL do Azure e às máquinas virtuais do Azure.

* [Entender Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

Use o recurso de classificação e descoberta de dados do banco de dados SQL do Azure. A descoberta e a classificação de dados fornecem recursos avançados incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular &amp; a proteção de informações confidenciais em seus bancos.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como usar a descoberta e a classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os tempos de execução de integração devem ser separados por mapeamentos de rede virtual (VNet) e marcados adequadamente.

 Você também pode usar pontos de extremidade privados para executar o isolamento de rede. Um Ponto de Extremidade Privado do Azure é um adaptador de rede que conecta você de maneira privada e segura a um serviço com tecnologia do Link Privado do Azure. O Ponto de Extremidade Privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Entender o link privado](../private-link/private-endpoint-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: para fontes de dados (como o banco de dado SQL do Azure) armazenando ou processando informações confidenciais para sua implantação do Azure data Factory, marque os recursos relacionados como confidenciais usando marcas.

Onde o link privado está disponível, use pontos de extremidade privados para proteger os recursos que estão sendo vinculados ao pipeline de Azure Data Factory. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você também pode reduzir o risco de vazamento de dados Configurando um conjunto estrito de regras de saída em um grupo de segurança de rede (NSG) e associando esse NSG à sua sub-rede.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Entender o link privado do Azure](../private-link/private-link-overview.md)

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: se o armazenamento de dados de nuvem oferecer suporte a HTTPS ou TLS, todas as transferências de dados entre serviços de movimentação de dados no data Factory e um armazenamento de dados de nuvem serão por meio do canal seguro HTTPS ou TLS. A versão do TLS usada é a 1.2.

Todas as conexões com o banco de dados SQL do Azure e o Azure Synapse Analytics exigem Criptografia (SSL/TLS), enquanto os dados estão em trânsito para e do banco. Ao criar um pipeline usando JSON, adicione a propriedade criptografia e defina-a como verdadeira na cadeia de conexão. Para Armazenamento do Azure, é possível usar HTTPS na cadeia de conexão.

* [Noções básicas sobre criptografia em trânsito no Azure Data Factory](./data-movement-security-considerations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: se você usar Azure data Factory para copiar e transformar suas instâncias do banco de dados SQL do Azure, use o recurso de descoberta e classificação de banco de dados SQL do Azure. A descoberta e a classificação de dados fornecem recursos avançados incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular &amp; a proteção de informações confidenciais em seus bancos.

Os recursos de classificação e descoberta de dados ainda não estão disponíveis para outros serviços do Azure.

* [Como usar a descoberta e a classificação de dados para o Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao plano de controle de Azure data Factory (o portal do Azure).

Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções colaborador ou proprietário, ou um administrador da assinatura do Azure.

Para suas Data Factory fontes de dados, como o banco de dado SQL do Azure, consulte a linha de base de segurança para esse serviço para obter mais informações sobre o RBAC do Azure.

* [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: Recomendamos que você habilite o mecanismo de criptografia de dados para todos os repositórios de dados relacionados às implantações do Azure data Factory. Você pode consultar a linha de base de segurança para esse serviço para obter mais informações sobre a criptografia de dados em repouso.

Se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, os discos virtuais em Máquinas Virtuais do Windows (VM) serão criptografados em repouso usando a criptografia do lado do servidor ou o ADE (Azure Disk Encryption). O Azure Disk Encryption aproveita o recurso BitLocker do Windows para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada. A criptografia do lado do servidor com chaves gerenciadas pelo cliente aprimora o ADE, permitindo usar quaisquer tipos de sistema operacional e imagens para as VMs, criptografando dados no serviço de armazenamento.

Você pode armazenar credenciais ou valores secretos em um Azure Key Vault e usá-los durante a execução do pipeline para passar para suas atividades. Você também pode armazenar credenciais para armazenamentos de dados e computações em um Azure Key Vault. O Azure Data Factory recupera as credenciais ao executar uma atividade que usa o armazenamento de dados/computação.

* [Noções básicas sobre criptografia em repouso no Azure Data Factory](./data-movement-security-considerations.md)

* [Criptografia do lado do servidor de Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption para VMs do Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Como usar os segredos de Azure Key Vault em atividades de pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Como credenciais no Azure Key Vault](./store-credentials-in-key-vault.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para Azure data Factory e recursos relacionados.

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/platform/alerts-activity-log.md)

* [Log da análise do Armazenamento do Azure](../storage/common/storage-analytics-logging.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: se você estiver usando o banco de dados SQL do Azure como um armazenamento de dados, habilite a segurança de dados avançada para o Azure SQL Database e siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em seus servidores SQL do Azure.

Se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em suas VMs. Use a solução recomendada de segurança do Azure ou de terceiros para executar avaliações de vulnerabilidade para suas máquinas virtuais.

* [Como executar avaliações de vulnerabilidade no banco de dados SQL do Azure](../azure-sql/database/sql-vulnerability-assessment.md)

* [Como habilitar a segurança de dados avançada](../azure-sql/database/azure-defender-for-sql.md)

* [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, use a solução de gerenciamento de atualizações do Azure para gerenciar atualizações e patches para suas VMs. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Solução Gerenciamento de Atualizações no Azure](../automation/update-management/overview.md)

* [Gerenciar atualizações e patches para as VMs do Azure](../automation/update-management/manage-updates-for-vm.md)

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretrizes**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, poderá usar uma solução de gerenciamento de patches de terceiros. Você pode usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações e patches para suas máquinas virtuais. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

* [Solução Gerenciamento de Atualizações no Azure](../automation/update-management/overview.md)

* [Gerenciar atualizações e patches para as VMs do Azure](../automation/update-management/manage-updates-for-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Orientação**: se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, exporte os resultados da verificação em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram corrigidas. Ao usar a recomendação de gerenciamento de vulnerabilidade sugerida pela central de segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricas.

* [Entender o verificador de vulnerabilidades integrada para máquinas virtuais](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: se você estiver executando o Integration Runtime em uma máquina virtual do Azure, poderá usar o verificador de vulnerabilidades nativas. O verificador de vulnerabilidades incluído na Central de Segurança do Azure é fornecido pela Qualys. O verificador do Qualys é a ferramenta líder para a identificação em tempo real de vulnerabilidades em suas máquinas virtuais do Azure.

Quando a Central de Segurança identifica as vulnerabilidades, apresenta as conclusões e informações relacionadas como recomendações. As informações relacionadas incluem etapas de correção, CVEs relacionadas, pontuações de CVSS e muito mais. Você pode exibir as vulnerabilidades identificadas para uma ou mais assinaturas ou para uma máquina virtual específica.

* [Verificador de vulnerabilidades integrada para máquinas virtuais](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

* [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, aproveite o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. A Automação do Azure fornece controle total durante a implantação, operações e encerramento de cargas de trabalho e de recursos.

Observação: o nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, o cliente precisou habilitar o diagnóstico em nível de convidado e colocar os logs de eventos do Windows em um espaço de trabalho Log Analytics.

* [Uma introdução à Automação do Azure](../automation/automation-intro.md)

* [Como habilitar o inventário de VM do Azure](../automation/automation-tutorial-installed-software.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, a automação do Azure fornecerá controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Você pode usar Controle de Alterações para identificar todos os softwares instalados em máquinas virtuais. Você pode implementar seu próprio processo ou usar a configuração de estado da automação do Azure para remover software não autorizado.

* [Uma introdução à Automação do Azure](../automation/automation-intro.md)

* [Controlar alterações no ambiente com a solução Controle de Alterações](../automation/change-tracking/overview.md)

* [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Orientação**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, use os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em VMS.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: o controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure que ajuda a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux). Implemente uma solução de terceiros se isso não atender aos requisitos da sua organização.

Observe que isso só se aplicará se o Integration Runtime estiver em execução em uma máquina virtual do Azure.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Orientação**: se você estiver executando sua integração de tempo de execução em uma máquina virtual do Azure, dependendo do tipo de scripts, você poderá usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure. Você também pode aproveitar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

* [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: aplicativos de alto risco implantados em seu ambiente do Azure podem ser isolados usando rede virtual, sub-rede, assinaturas, grupos de gerenciamento, etc. e suficientemente protegidos com um firewall do Azure, o WAF (firewall do aplicativo Web) ou o NSG (grupo de segurança de rede).

* [Redes virtuais e máquinas virtuais no Azure](../virtual-machines/network-overview.md)

* [O que é o Firewall do Azure?](../firewall/overview.md)

* [O que é o Firewall do Aplicativo Web do Azure?](../web-application-firewall/overview.md)

* [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md)

* [O que é a Rede Virtual do Azure?](../virtual-network/virtual-networks-overview.md)

* [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

* [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para Azure Data Factory com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. datafactory" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de Azure Data Factory.

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: se você estiver executando sua integração de tempo de execução em uma máquina virtual do Azure, use a recomendação da central de segurança do Azure [corrigir vulnerabilidades em configurações de segurança em suas máquinas virtuais] para manter as configurações de segurança em todos os recursos de computação.

* [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md)

* [Como corrigir as recomendações da central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

* [Informações sobre como criar modelos de Azure Resource Manager](../virtual-machines/windows/ps-template.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Orientação**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, observe que há várias opções para manter uma configuração segura para VMs para implantação:
- Modelos de Azure Resource Manager: são arquivos baseados em JSON usados para implantar uma VM do portal do Azure, e o modelo personalizado precisará ser mantido. A Microsoft realiza a manutenção nos modelos de base.
- VHD (disco rígido virtual) personalizado: em algumas circunstâncias, pode ser necessário ter arquivos VHD personalizados usados, como ao lidar com ambientes complexos que não podem ser gerenciados por outros meios. -Configuração de estado da automação do Azure: depois que o sistema operacional base for implantado, isso poderá ser usado para um controle mais granular das configurações e imposto por meio da estrutura de automação.

Para a maioria dos cenários, os modelos de VM base da Microsoft combinados com a configuração de estado desejado da automação do Azure podem ajudar na reunião e manutenção dos requisitos de segurança.

* [Informações sobre como baixar o modelo de VM](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informações sobre como criar modelos de Azure Resource Manager](../virtual-machines/windows/ps-template.md)

* [Como carregar um VHD de VM personalizado para o Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

* [Documentação do Azure Repos](/azure/devops/repos/index)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Orientação**: se estiver usando imagens personalizadas, use o Azure RBAC (controle de acesso baseado em função) para garantir que somente usuários autorizados possam acessar as imagens. Para imagens de contêiner, armazene-as no registro de contêiner do Azure e aproveite o RBAC do Azure para garantir que somente usuários autorizados possam acessar as imagens.

A função colaborador de Data Factory pode ser usada para criar e gerenciar fábricas de dados, bem como recursos filho dentro deles.

* [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Entender o RBAC do Azure para registro de contêiner](../container-registry/container-registry-roles.md)

* [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Funções e permissões do Azure Data Factory](./concepts-roles-permissions.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. datafactory" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Orientação**: essa recomendação pode ser aplicada se o Integration Runtime estiver em execução em uma máquina virtual do Azure. A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](../automation/automation-dsc-onboarding.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. datafactory" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: essa recomendação pode ser aplicada se o Integration Runtime estiver em execução em uma máquina virtual do Azure. A configuração de estado de automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](../automation/automation-dsc-onboarding.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

Você também pode armazenar credenciais ou valores secretos em um Azure Key Vault e usá-los durante a execução do pipeline para passar para suas atividades. Verifique se a exclusão reversível está habilitada.

* [Como integrar com identidades gerenciadas do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

* [Usar segredos de Azure Key Vault em atividades de pipeline](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Exclusão reversível no Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: ao criar um data Factory, uma identidade gerenciada pode ser criada junto com a criação de fábrica. A identidade gerenciada é um aplicativo gerenciado registrado para Azure Active Directory e representa esse data factory específico.

* [Identidade gerenciada para Azure Data Factory](./data-factory-service-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usar software antimalware gerenciado centralmente

**Diretrizes**: se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, poderá usar o Microsoft antimalware para Azure máquinas virtuais do Windows para monitorar e defender continuamente seus recursos.

* [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, o Serviço de Aplicativo do Azure), no entanto, ele não é executado no seu conteúdo.

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc.

Use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento.

* [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: quando implantado, o Microsoft antimalware para Azure instalará automaticamente as atualizações de assinatura, plataforma e mecanismo mais recentes por padrão. Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. O sistema operacional Windows pode estar mais protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de proteção avançada contra ameaças do Microsoft defender, que se integra à central de segurança do Azure.

* [Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure](../security/fundamentals/antimalware.md)

* [Proteção Avançada contra Ameaças do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, habilite o backup do Azure e configure a VM, bem como a frequência e o período de retenção desejados para backups automáticos.

Para qualquer um de seus armazenamentos de dados, consulte a linha de base de segurança desse serviço para obter recomendações sobre como executar backups regulares e automatizados.

* [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)

* [Fazer backup de uma VM do Azure usando as configurações da VM](../backup/backup-azure-vms-first-look-arm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure, habilite o backup do Azure e as VMs do Azure de destino, bem como os períodos de frequência e retenção desejados. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

Para qualquer um de seus armazenamentos de dados, consulte a linha de base de segurança desse serviço para obter recomendações sobre como executar backups regulares e automatizados.

* [Uma visão geral do backup de VM do Azure](../backup/backup-azure-vms-introduction.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: se você estiver executando seu Integration Runtime em uma máquina virtual do Azure, garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste o conteúdo de restauração em uma VLAN isolada. Testar periodicamente a restauração de chaves de backup gerenciadas pelo cliente.

Para qualquer um de seus armazenamentos de dados, consulte a linha de base de segurança desse serviço para obter orientação sobre como validar backups.

* [Como recuperar arquivos do backup de máquina virtual do Azure](../backup/backup-azure-restore-files-from-vm.md)

* [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: se você estiver executando seu Integration Runtime em uma VM (máquina virtual) do Azure e fizer a VM com o backup do Azure, sua VM será criptografada em repouso com criptografia do serviço de armazenamento (SSE). O backup do Azure também pode fazer backup de VMs do Azure que são criptografadas usando Azure Disk Encryption. O Azure Disk Encryption se integra com as chaves de criptografia do BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com Azure Key Vault chaves de criptografia de chave (KEKs). Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

* [Exclusão reversível para VMs](../backup/backup-azure-security-feature-cloud.md)

* [Visão geral de exclusão reversível do Azure Key Vault](../key-vault/general/soft-delete-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

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

**Diretrizes**: 

* [Siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)