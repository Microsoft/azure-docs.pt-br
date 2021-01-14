---
title: Linha de base de segurança do Azure para automação do Azure
description: Linha de base de segurança do Azure para automação
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1626ee074d51048dbf45c629f261a550822d746c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200831"
---
# <a name="azure-security-baseline-for-automation"></a>Linha de base de segurança do Azure para automação

A linha de base de segurança do Azure para automação contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, confira a [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: a conta de automação do Azure ainda não dá suporte ao link privado do Azure para restringir o acesso ao serviço por meio de pontos de extremidade privados. Os Runbooks que são autenticados e executados em relação aos recursos no Azure são executados em uma área restrita do Azure e aproveitam os recursos de back-end compartilhados, que a Microsoft é responsável por isolar uns dos outros; sua rede é irrestrita e pode acessar recursos públicos. Atualmente, a automação do Azure não tem integração de rede virtual para rede privada além do suporte para Hybrid runbook Workers. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Para obter mais isolamento para seus runbooks, você pode usar Hybrid runbook Workers em execução em máquinas virtuais do Azure. Ao criar uma máquina virtual do Azure, você deve criar uma rede virtual (VNet) ou usar uma VNet existente e configurar suas VMs com uma sub-rede. Verifique se todas as sub-redes implantadas têm um grupo de segurança de rede aplicado com controles de acesso à rede específicos para suas portas e fontes confiáveis de aplicativos. Para requisitos específicos de serviço, consulte a recomendação de segurança para esse serviço específico.

Como alternativa, se você tiver um requisito específico, o Firewall do Azure também poderá ser usado para atendê-lo.

* [Redes virtuais e máquinas virtuais no Azure](../virtual-machines/network-overview.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Ambiente de execução de runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: atualmente, a automação do Azure não tem integração de rede virtual para rede privada além do suporte para Hybrid runbook Workers. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, verifique se a sub-rede que contém esses trabalhadores está habilitada com um NSG (grupo de segurança de rede) e configure os logs de fluxo para encaminhar os logs para uma conta de armazenamento para auditoria de tráfego. Você também pode encaminhar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Embora as regras NSG e as rotas definidas pelo usuário não se apliquem ao ponto de extremidade privado, os logs de fluxo do NSG e as informações de monitoramento para conexões de saída ainda têm suporte e podem ser usados.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: atualmente, a automação do Azure não tem integração de rede virtual para rede privada além do suporte para Hybrid runbook Workers. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, habilite a proteção padrão de DDoS (negação de serviço distribuído) em suas redes virtuais que hospedam seus Hybrid runbook Workers para proteger contra ataques de DDoS. Usando a inteligência de ameaças integrada da central de segurança do Azure, você pode negar comunicações com endereços IP mal-intencionados conhecidos. Configure o Firewall do Azure em cada um dos segmentos de rede virtual, com inteligência contra ameaças habilitada e configure para **alertar e negar** para tráfego de rede mal-intencionado.

Você pode usar o acesso à rede just in time da central de segurança do Azure para limitar a exposição de máquinas virtuais do Windows aos endereços IP aprovados por um período de tempo limitado. Além disso, use as recomendações de proteção de rede adaptável da central de segurança do Azure para configurações de NSG para limitar portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

* [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

* [Entender a proteção de rede adaptável da central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md)

* [Entender o controle de acesso à rede just in time da central de segurança do Azure](../security-center/security-center-just-in-time.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: atualmente, a automação do Azure não tem integração de rede virtual para rede privada além do suporte para Hybrid runbook Workers, esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, poderá registrar os logs de fluxo NSG em uma conta de armazenamento para gerar registros de fluxo para as máquinas virtuais do Azure que estão agindo como runbook Workers. Ao investigar a atividade anômala, você pode habilitar a captura de pacotes do observador de rede para que o tráfego de rede possa ser examinado quanto a atividades incomuns e inesperadas.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: atualmente, a automação do Azure não tem integração de rede virtual para rede privada além do suporte para Hybrid runbook Workers. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers hospedados em máquinas virtuais do Azure, poderá combinar capturas de pacote fornecidas pelo observador de rede e ferramentas de IDS de software livre para executar a detecção de invasão de rede para uma ampla gama de ameaças a esses computadores de trabalho. Além disso, você pode implantar o Firewall do Azure nos segmentos de rede virtual, conforme apropriado, com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

* [Executar a detecção de invasão de rede com o Observador de Rede e ferramentas de software livre](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure configurados no Azure que exigem acesso aos recursos de automação. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, GuestAndHybridManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

* [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para recursos de rede usados pela automação do Azure com o Azure Policy.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos do Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint. Você pode aplicar o plano gráfico a novas assinaturas e ajustar o controle e o gerenciamento por meio da versão.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Exemplos de Azure Policy para rede](../governance/policy/samples/built-in-policies.md#network)

* [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos de rede. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure. No entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para todos os Hybrid runbook Workers em execução em máquinas virtuais do Windows.

* [Como configurar a sincronização de horário para recursos de computação do Azure](../virtual-machines/windows/time-sync.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: encaminhe os dados de log para Azure monitor logs para agregar dados de segurança gerados pelos recursos de automação do Azure. Em Azure Monitor, use consultas de log para pesquisar e executar análises e use contas de armazenamento do Azure para armazenamento de longo prazo/arquivamento. A automação do Azure pode enviar o status do trabalho de runbook, fluxos de trabalho, dados de configuração de estado de automação, gerenciamento de atualizações e logs de controle de alterações ou de inventário para seu espaço de trabalho Log Analytics. Essas informações são visíveis na API de logs portal do Azure, Azure PowerShell e Azure Monitor, que permite executar investigações simples.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Encaminhar os dados de trabalho da Automação do Azure para os logs do Azure Monitor](./automation-manage-send-joblogs-log-analytics.md)

* [Integrar o DSC a logs de Azure Monitor](./automation-dsc-diagnostics.md)

* [Regiões compatíveis com o workspace do Log Analytics vinculado](./how-to/region-mappings.md)

* [Logs de Gerenciamento de Atualizações de consulta](./update-management/query-logs.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite o Azure monitor para acesso aos logs de auditoria e de atividades, que incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

* [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

* [Exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: ao usar a automação do Azure com os trabalhos de runbook multilocatário, esse controle não é aplicável e a plataforma manipula as máquinas virtuais subjacentes.

Ao usar o recurso Hybrid Runbook Worker, a central de segurança do Azure fornece monitoramento de log de eventos de segurança para máquinas virtuais do Windows. Se sua organização quiser manter os dados do log de eventos de segurança, ele poderá ser armazenado em uma camada de coleta de dados, no ponto em que pode ser consultado em Log Analytics. Há diferentes camadas: mínima, comum e todas, que são detalhadas no link a seguir.

* [Configurar camada de coleta de dados na central de segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina seu período de retenção de espaço de trabalho de log Analytics de acordo com os regulamentos de conformidade de sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

* [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Detalhes de retenção de dados para contas de automação](./automation-managing-data.md#data-retention)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use Azure Monitor consultas de log para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Entender as consultas de log no Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança do Azure com Azure monitor para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Como alertar sobre Azure Monitor dados de log](../azure-monitor/learn/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: ao usar a automação do Azure com trabalhos de runbook multilocatário, esse controle não é aplicável e a plataforma manipula as máquinas virtuais subjacentes.

No entanto, ao usar o recurso Hybrid Runbook Worker, você pode usar o Microsoft anti-malware para serviços de nuvem do Azure e máquinas virtuais. Configure suas máquinas virtuais para registrar eventos em uma conta de armazenamento do Azure. Configure um espaço de trabalho Log Analytics para ingerir os eventos das contas de armazenamento e criar alertas quando apropriado. Siga as recomendações na central de segurança do Azure: "aplicativos de computação &amp; ".

* [Como configurar o anti-malware da Microsoft para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

* [Como habilitar o monitoramento em nível de convidado para máquinas virtuais](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Diretrizes**: implemente uma solução de terceiros do Azure Marketplace para a solução de registro em log DNS de acordo com suas necessidades de organização.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Orientação**: ao usar a automação do Azure com os trabalhos de runbook multilocatário, esse controle não é aplicável e a plataforma manipula as máquinas virtuais subjacentes.

No entanto, ao usar o recurso Hybrid Runbook Worker, a central de segurança do Azure fornece monitoramento de log de eventos de segurança para máquinas virtuais do Azure. A central de segurança provisiona o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas se o provisionamento automático estiver habilitado. Ou você pode instalar o agente manualmente. O agente habilita o evento 4688 de criação de processo e o campo CommandLine dentro do evento 4688. Os novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da central de segurança.

* [Coleta de dados na Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: Use Azure Active Directory funções de administrador internas que podem ser atribuídas explicitamente e podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos. Sempre que usar contas Executar como da conta de automação para seus runbooks, verifique se essas entidades de serviço também são rastreadas em seu inventário, pois geralmente têm permissões elevadas. Exclua todas as contas Executar como não utilizadas para minimizar a superfície de ataque exposta.

* [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Excluir uma conta Executar como ou Executar como Clássica](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Gerenciar uma conta Executar como da Automação do Azure](./manage-runas-account.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: a conta de automação do Azure não tem o conceito de senhas padrão. Os clientes são responsáveis por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão que são executadas no topo do serviço ou de seus Hybrid runbook Workers.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas. Sempre que usar contas Executar como da conta de automação para seus runbooks, verifique se essas entidades de serviço também são rastreadas em seu inventário, pois geralmente têm permissões elevadas. Faça o escopo dessas identidades com as permissões menos privilegiadas necessárias para que seus runbooks executem com êxito seu processo automatizado. Exclua todas as contas Executar como não utilizadas para minimizar a superfície de ataque exposta.

Você também pode habilitar um acesso just-in-time/apenas o suficiente usando Azure AD Privileged Identity Management funções privilegiadas para serviços da Microsoft e Azure Resource Manager.

* [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [Excluir uma conta Executar como ou Executar como Clássica](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Gerenciar uma conta Executar como da Automação do Azure](./manage-runas-account.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: sempre que possível, use o SSO com Azure Active Directory em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Logon único em aplicativos no Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

* [Usar o Azure AD para a autenticação no Azure](./automation-use-azure-ad.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Orientação**: habilitar a MFA (autenticação multifator) do Azure AD e seguir as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3,6: usar computadores dedicados para todas as tarefas administrativas

**Diretrizes**: Use o PAWs com a autenticação multifator configurada para fazer logon e configurar recursos de conta de automação do Azure em ambientes de produção.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: Utilize as detecções de risco do Azure ad para exibir alertas e relatórios sobre comportamento de usuário arriscado. Opcionalmente, o cliente pode encaminhar alertas de detecção de risco da central de segurança do Azure para Azure Monitor e configurar alertas/notificações personalizados usando grupos de ação.

* [Noções básicas sobre as detecções de riscos da central de segurança do Azure (atividade suspeita)](../active-directory/identity-protection/overview-identity-protection.md)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como configurar grupos de ação para alertas e notificações personalizados](../azure-monitor/platform/action-groups.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: é recomendável usar locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure ad como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário. Se estiver usando Hybrid runbook Workers, você poderá aproveitar identidades gerenciadas em vez de contas Executar como para habilitar permissões seguras mais diretas.

* [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

* [Usar autenticação de runbook com identidades gerenciadas](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar associações de grupo, acesso aos aplicativos empresariais e atribuições de função com eficiência. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. Sempre que usar contas Executar como da conta de automação para seus runbooks, verifique se essas entidades de serviço também são controladas em seu inventário, pois geralmente têm permissões elevadas. Exclua todas as contas Executar como não utilizadas para minimizar a superfície de ataque exposta.

* [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

* [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

* [Excluir uma conta Executar como ou Executar como Clássica](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Gerenciar uma conta Executar como da Automação do Azure](./manage-runas-account.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração a qualquer ferramenta de Monitoramento/SIEM.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure Active Directory e enviando os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics.

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure ad para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário para o recurso de rede. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

* [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: para contas de automação do Azure, o suporte da Microsoft pode acessar metadados de recursos de plataforma durante um caso de suporte aberto sem uso de outra ferramenta.

No entanto, ao usar Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure e com as necessidades de terceiros para acessar os dados do cliente (como durante uma solicitação de suporte), use Sistema de Proteção de Dados do Cliente (versão prévia) para que as máquinas virtuais do Azure revisem e aprovem ou rejeitem solicitações de acesso a dados do cliente.

* [Noções básicas sobre Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos de automação do Azure que armazenam ou processam informações confidenciais.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Isole os ambientes usando recursos de conta de automação separados. Recursos como Hybrid runbook Workers devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um NSG (grupo de segurança de rede) ou firewall do Azure. Para máquinas virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desligá-los quando não estiverem em uso.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alerta ou alerta e negar com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: ao usar o recurso Hybrid runbook Worker, aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia tais transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure nas redes virtuais do Azure são capazes de negociar o TLS 1,2 ou superior. A automação do Azure dá suporte total e impõe a camada de transporte (TLS) 1,2 e todas as chamadas de cliente ou versões posteriores para todos os pontos de extremidade HTPS externos (por meio de WebHooks, nós DSC, Hybrid runbook Worker).

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

* [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Imposição de TLS 1,2 da automação do Azure](/azure/active-directory/hybrid/reference-connect-tls-enforcement)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Use uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos sistemas de tecnologia da organização, incluindo aquelas localizadas no local ou em um provedor de serviços remoto e atualize o inventário de informações confidenciais da organização.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: usar o Azure RBAC (controle de acesso baseado em função) para controlar o acesso aos recursos de automação do Azure usando as definições de função internas, atribua acesso para usuários que acessam seus recursos de automação seguindo um modelo de acesso menos privilegiado ou "apenas suficiente". Ao usar Hybrid runbook Workers, aproveitar identidades gerenciadas para essas máquinas virtuais para evitar o uso de entidades de serviço, ao usar os Hybrid runbook Workers de multilocatário ou híbrido, certifique-se de aplicar permissões de RBAC do Azure com escopo adequado na identidade dos runbook Workers.

* [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

* [Permissões de runbook para um Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [Gerenciar permissões e segurança de funções](./automation-role-based-access-control.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: a automação do Azure não expõe atualmente as máquinas virtuais do runbook Worker de vários locatários subjacentes, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, você precisará usar uma solução de prevenção de perda de dados baseada em host de terceiros para impor controles de acesso às suas máquinas virtuais hospedadas Hybrid Runbook Worker.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Orientação**: usar chaves gerenciadas pelo cliente com a automação do Azure. A automação do Azure dá suporte ao uso de chaves gerenciadas pelo cliente para criptografar todos os ' ativos seguros ' usados, como: credenciais, certificados, conexões e variáveis criptografadas. Aproveite as variáveis criptografadas com seus runbooks para todas as suas necessidades de pesquisa de variável persistentes para evitar a exposição não intencional.

Ao usar Hybrid runbook Workers, os discos virtuais nas máquinas virtuais são criptografados em repouso usando a criptografia do lado do servidor ou o ADE (Azure Disk Encryption). A criptografia de disco do Azure aproveita o recurso BitLocker do Windows para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada. A criptografia do lado do servidor com chaves gerenciadas pelo cliente aprimora o ADE, permitindo usar quaisquer tipos de sistema operacional e imagens para as VMs, criptografando dados no serviço de armazenamento.

* [Criptografia do lado do servidor de Azure Managed disks](../virtual-machines/disk-encryption.md)

* [Azure Disk Encryption para VMs do Windows](../virtual-machines/windows/disk-encryption-overview.md)

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Variáveis gerenciadas na automação do Azure](./shared-resources/variables.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para recursos críticos do Azure, como componentes de rede, contas de automação do Azure e runbooks.

* [Log de diagnóstico para um grupo de segurança de rede](../private-link/private-link-overview.md#logging-and-monitoring)

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: siga as recomendações da central de segurança do Azure para executar avaliações de vulnerabilidade em seus recursos do Azure

* [Recomendações de segurança na Central de Segurança do Azure](../security-center/security-center-recommendations.md)

* [Referência de recomendação da central de segurança](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: a automação do Azure não expõe atualmente as máquinas virtuais do runbook Worker de vários locatários subjacentes, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, use o Azure Gerenciamento de Atualizações para gerenciar atualizações e patches para suas máquinas virtuais. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

* [Gerenciamento de Atualizações no Azure](./update-management/overview.md)

* [Gerenciar atualizações e patches para suas VMs](./update-management/manage-updates-for-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretrizes**: a automação do Azure não expõe atualmente as máquinas virtuais do runbook Worker de vários locatários subjacentes e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, poderá usar o Azure Gerenciamento de Atualizações para gerenciar atualizações e patches para suas máquinas virtuais. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações os computadores de patch que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

* [Solução Gerenciamento de Atualizações no Azure](./update-management/overview.md)

* [Gerenciar atualizações e patches para as VMs do Azure](./update-management/manage-updates-for-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: exporte os resultados da verificação em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram corrigidas. Ao usar a recomendação de gerenciamento de vulnerabilidade sugerida pela central de segurança do Azure, o cliente pode dinamizar o portal da solução selecionada para exibir os dados de verificação históricas.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure para ajudar a priorizar a correção de vulnerabilidades descobertas.

* [Entender a pontuação segura da central de segurança do Azure](../security-center/secure-score-security-controls.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos de automação do Azure em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e controlar os recursos de automação do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil. Exclua todas as contas Executar como não utilizadas para minimizar a superfície de ataque exposta.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Excluir uma conta Executar como ou Executar como Clássica](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Gerenciar uma conta Executar como da Automação do Azure](./manage-runas-account.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Orientação**: você precisará criar um inventário de recursos aprovados do Azure e software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro de assinaturas. Isso pode ajudar em ambientes baseados em alta segurança, como aqueles com contas de armazenamento.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Azure Policy os internos de exemplo para a automação do Azure](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: a oferta de automação do Azure não expõe atualmente as máquinas virtuais do runbook Worker de vários locatários subjacentes e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem trabalhos híbridos. No entanto, é possível instalar, remover e gerenciar os módulos do PowerShell ou do Python que os runbooks podem acessar por meio do portal ou cmdlets. O módulo antigo ou não aprovado deve ser removido ou atualizado para os runbooks.

Se você estiver usando Hybrid runbook Workers apoiado pelas máquinas virtuais do Azure, a automação do Azure fornecerá controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Aproveite o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, o cliente precisou habilitar o diagnóstico em nível de convidado e colocar os logs de eventos do Windows em um espaço de trabalho Log Analytics.

* [Uma introdução à Automação do Azure](./automation-intro.md)

* [Como habilitar o inventário de VM do Azure](./automation-tutorial-installed-software.md)

* [Gerenciar o módulo na automação do Azure](./shared-resources/modules.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: o cliente pode impedir a criação ou o uso de recursos com Azure Policy conforme exigido pelas diretrizes da empresa do cliente. Você pode implementar seu próprio processo para remover recursos não autorizados. Na oferta de automação do Azure, é possível instalar, remover e gerenciar os módulos do PowerShell ou do Python que os runbooks podem acessar por meio do portal ou cmdlets. O módulo antigo ou não aprovado deve ser removido ou atualizado para os runbooks.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Gerenciar o módulo na automação do Azure](./shared-resources/modules.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Orientação**: ao usar o recurso Hybrid runbook Worker, você pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: ao usar o recurso Hybrid runbook Worker, você pode usar o recurso controles de aplicativo adaptáveis da central de segurança do Azure com suas máquinas virtuais do Hybrid Worker.

O controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure, que ajuda você a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux). Implemente uma solução de terceiros se isso não atender aos requisitos da sua organização.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use as políticas de acesso condicional do Azure para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure" de locais ou dispositivos não seguros ou não aprovados.

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Orientação**: ao usar o recurso de Hybrid runbook Worker e, dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure. Você também pode aproveitar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

* [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: aplicativos de alto risco implantados em seu ambiente do Azure podem ser isolados usando contêineres de rede e de recursos separados usando construções como redes virtuais, sub-rede, assinaturas, grupos de gerenciamento, eles podem ser suficientemente protegidos com um firewall do Azure, o WAF (firewall do aplicativo Web) ou o NSG (grupo de segurança de rede).

* [Redes virtuais e máquinas virtuais no Azure](../virtual-machines/network-overview.md)

* [Visão geral do firewall do Azure](../firewall/overview.md)

* [Visão geral do firewall do aplicativo Web do Azure](../web-application-firewall/overview.md)

* [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md)

* [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md)

* [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)

* [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de sua automação do Azure e recursos relacionados. Você também pode usar definições de Azure Policy internas.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

Você também pode usar recomendações da central de segurança do Azure como uma linha de base de configuração segura para seus recursos do Azure.

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy os internos de exemplo para a automação do Azure](./policy-reference.md)

* [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretrizes**: a automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários. Isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso Hybrid Runbook Worker, use a recomendação da central de segurança do Azure [corrigir vulnerabilidades em configurações de segurança em suas máquinas virtuais] para manter as configurações de segurança em suas máquinas virtuais.

* [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md)

* [Como corrigir as recomendações da central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use modelos de Azure Resource Manager e Azure Policy para configurar com segurança os recursos do Azure associados à automação do Azure. Os modelos de Azure Resource Manager são arquivos baseados em JSON usados para implantar recursos do Azure, e todos os modelos personalizados precisarão ser armazenados e mantidos com segurança em um repositório de código. Use o recurso de integração de controle do código-fonte para manter seus runbooks em sua conta de automação atualizados com scripts no repositório do controle do código-fonte. Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Usar a integração de controle do código-fonte](./source-control-integration.md)

* [Informações sobre como criar modelos de Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreendendo os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

* [Implantar uma conta de automação usando um modelo de Azure Resource Manager](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Policy os internos de exemplo para a automação do Azure](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: a automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso Hybrid Runbook Worker, há várias opções para manter uma configuração segura para máquinas virtuais do Azure para implantação:

- Modelos de Azure Resource Manager: são arquivos baseados em JSON usados para implantar uma VM do portal do Azure, e o modelo personalizado precisará ser mantido. A Microsoft realiza a manutenção nos modelos de base.
- VHD (disco rígido virtual) personalizado: em algumas circunstâncias, pode ser necessário ter arquivos VHD personalizados usados, como ao lidar com ambientes complexos que não podem ser gerenciados por outros meios.
- Configuração de estado da automação do Azure: depois que o sistema operacional base for implantado, isso poderá ser usado para um controle mais granular das configurações e imposto por meio da estrutura de automação.

Para a maioria dos cenários, os modelos de VM base da Microsoft combinados com a configuração de estado de automação do Azure podem ajudar a atender e a manter os requisitos de segurança.

* [Informações sobre como baixar o modelo de VM](../virtual-machines/windows/download-template.md)

* [Informações sobre a criação de modelos do ARM](../virtual-machines/windows/ps-template.md)

* [Como carregar um VHD de VM personalizado para o Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager e scripts de configuração de estado desejado. Para acessar os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory se integrados ao Azure DevOps ou Active Directory se integrado ao TFS. Use o recurso de integração de controle do código-fonte para manter seus runbooks em sua conta de automação atualizados com scripts no repositório do controle do código-fonte.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Usar a integração de controle do código-fonte](./source-control-integration.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretrizes**: a automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso Hybrid Runbook Worker, verifique se você está limitando corretamente o acesso à imagem de sistema operacional personalizada localizada em sua conta de armazenamento para que somente usuários autorizados possam acessar a imagem.

* [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure. Você também pode fazer uso de definições de política internas relacionadas aos seus recursos específicos.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Policy os internos de exemplo para a automação do Azure](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: a automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso de Hybrid Runbook Worker, use a configuração de estado de automação do Azure em runbook Workers, que é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](./automation-dsc-onboarding.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: usar Azure Policy para alertar e auditar configurações de recursos do Azure, a política pode ser usada para detectar determinado recurso não configurado com um ponto de extremidade privado.

Ao usar o recurso Hybrid Runbook Worker, aproveite a central de segurança do Azure para executar verificações de linha de base para suas máquinas virtuais do Azure. Métodos adicionais para configuração automatizada incluem a configuração de estado de automação do Azure.

* [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

* [Introdução à Configuração de Estado da Automação do Azure](./automation-dsc-getting-started.md)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy os internos de exemplo para a automação do Azure](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: a oferta de automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem trabalhos híbridos.

Ao usar o recurso de Hybrid Runbook Worker, use a configuração de estado de automação do Azure para os runbook Workers que é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](./automation-dsc-onboarding.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Usar autenticação de runbook com identidades gerenciadas](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [Como criar um Key Vault](../key-vault/general/quick-create-portal.md)

* [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

* [Como configurar identidades gerenciadas](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usar software antimalware gerenciado centralmente

**Diretrizes**: a oferta de automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso Hybrid Runbook Worker, use o antimalware da Microsoft para máquinas virtuais do Windows do Azure para monitorar e defender continuamente os recursos do runbook Worker.

* [Como configurar o anti-malware da Microsoft para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Orientação**: não aplicável; A automação do Azure como um serviço não armazena arquivos. O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, automação do Azure), no entanto, não é executado no seu conteúdo.

* [Entender o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: a automação do Azure atualmente não expõe as máquinas virtuais ou o sistema operacional do runbook Worker de vários locatários, e isso é tratado pela plataforma. Esse controle não será aplicável se você estiver usando o serviço pronto para uso sem Hybrid runbook Workers.

Ao usar o recurso Hybrid Runbook Worker, use o Microsoft antimalware para Azure para instalar automaticamente as atualizações de assinatura, plataforma e mecanismo mais recentes, por padrão, no runbook Worker. Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. O sistema operacional Windows pode estar mais protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de proteção avançada contra ameaças do Microsoft defender, que se integra à central de segurança do Azure.

* [Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure](../security/fundamentals/antimalware.md)

* [Proteção Avançada contra Ameaças do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: Use Azure Resource Manager para implantar contas de automação do Azure e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar contas de automação do Azure e recursos relacionados. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente.

Use o recurso de integração de controle do código-fonte para manter seus runbooks em sua conta de automação atualizados com scripts no repositório do controle do código-fonte.

* [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Referência de modelo de Azure Resource Manager para recursos de automação do Azure](/azure/templates/microsoft.automation/allversions)

* [Criar uma conta de Automação usando um modelo do Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

* [Introdução à automação do Azure](./automation-intro.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Usar a integração de controle do código-fonte](./source-control-integration.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use Azure Resource Manager para implantar contas de automação do Azure e recursos relacionados. Azure Resource Manager fornece a capacidade de exportar modelos que podem ser usados como backups para restaurar contas de automação do Azure e recursos relacionados. Use a automação do Azure para chamar a API de exportação do modelo Azure Resource Manager regularmente. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault. Você pode exportar seus runbooks para arquivos de script usando o portal do Azure ou o PowerShell.

* [Visão geral do Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Referência de modelo de Azure Resource Manager para recursos de automação do Azure](/azure/templates/microsoft.automation/allversions)

* [Criar uma conta de Automação usando um modelo do Azure Resource Manager](./quickstart-create-automation-account-template.md)

* [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

* [Grupos de recursos-exportar modelo](/rest/api/resources/resourcegroups/exporttemplate)

* [Introdução à automação do Azure](./automation-intro.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Backup de dados do Azure para contas de automação](./automation-managing-data.md#data-backup)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a implantação de modelos de Azure Resource Manager regularmente para uma assinatura isolada, se necessário. Teste a restauração de chaves de backup gerenciadas pelo cliente.

* [Implantar recursos com modelos ARM e portal do Azure](../azure-resource-manager/templates/deploy-portal.md)

* [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Uso de chaves gerenciadas pelo cliente para uma conta de automação](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: Use o DevOps do Azure para armazenar e gerenciar com segurança seu código, como modelos de Azure Resource Manager. Para proteger os recursos que você gerencia no Azure DevOps, você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory se integrados ao Azure DevOps ou Active Directory se integrado ao TFS.

Use o recurso de integração de controle do código-fonte para manter seus runbooks em sua conta de automação atualizados com scripts no repositório do controle do código-fonte.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Usar a integração de controle do código-fonte](./source-control-integration.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

* [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

* [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

* [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

* [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)