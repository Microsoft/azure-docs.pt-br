---
title: Linha de base de segurança do Azure para Máquinas Virtuais do Linux Máquinas Virtuais do Windows
description: A linha de base de segurança Máquinas Virtuais do Windows fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ef5cddc131e25a1966ca86c3c8650452f73be69e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879673"
---
# <a name="azure-security-baseline-for-windows-virtual-machines"></a>Linha de base de segurança do Azure para Máquinas Virtuais do Windows

A linha de base de segurança do Azure para Máquinas Virtuais do Windows contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: ao criar uma VM (máquina virtual) do Azure, você deve criar uma rede virtual (vnet) ou usar uma vnet existente e configurar a VM com uma sub-rede. Verifique se todas as sub-redes implantadas têm um grupo de segurança de rede aplicado com controles de acesso à rede específicos para suas portas e fontes confiáveis de aplicativos.

Como alternativa, se você tiver um caso de uso específico para um firewall centralizado, o Firewall do Azure também poderá ser usado para atender a esses requisitos.

* [Redes virtuais e máquinas virtuais no Azure](../network-overview.md)

* [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar e configurar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretrizes**: Use a central de segurança do Azure para identificar e seguir as recomendações de proteção de rede para ajudar a proteger seus recursos de VM (máquina virtual) do Azure no Azure. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento para a auditoria de tráfego para as VMs para atividades incomuns.

* [Como habilitar logs de fluxo de NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: se estiver usando sua VM (máquina virtual) para hospedar aplicativos Web, use um NSG (grupo de segurança de rede) na sub-rede da VM para limitar o tráfego de rede, as portas e os protocolos que têm permissão para se comunicar. Siga uma abordagem de rede com privilégios mínimos ao configurar o NSGs para permitir somente o tráfego necessário para seu aplicativo.

Você também pode implantar o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics.

* [Criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal do Azure](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

* [Informações sobre grupos de segurança de rede](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilitar a proteção padrão de DDoS (negação de serviço distribuído) nas redes virtuais para proteger contra ataques de DDoS. Usando a inteligência de ameaças integrada da central de segurança do Azure, você pode monitorar as comunicações com endereços IP mal-intencionados conhecidos. Configure adequadamente o Firewall do Azure em cada um de seus segmentos de rede virtual, com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

Você pode usar o acesso à rede just in time da central de segurança do Azure para limitar a exposição de Máquinas Virtuais do Windows aos endereços IP aprovados por um período limitado. Além disso, use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

* [Como configurar a proteção contra DDoS](../../ddos-protection/manage-ddos-protection.md)

* [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../../security-center/azure-defender.md)

* [Entender a proteção de rede adaptável da central de segurança do Azure](../../security-center/security-center-adaptive-network-hardening.md)

* [Entender o controle de acesso à rede just in time da central de segurança do Azure](../../security-center/security-center-just-in-time.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: você pode registrar os logs de fluxo do NSG em uma conta de armazenamento para gerar registros de fluxo para suas máquinas virtuais do Azure. Ao investigar a atividade anômala, você pode habilitar a captura de pacotes do observador de rede para que o tráfego de rede possa ser examinado quanto a atividades incomuns e inesperadas.

* [Como habilitar logs de fluxo de NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar o Observador de Rede](../../network-watcher/network-watcher-create.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: combinando as capturas de pacote fornecidas pelo observador de rede e uma ferramenta de IDs de código-fonte aberto, você pode executar a detecção de invasão de rede para uma ampla gama de ameaças. Além disso, você pode implantar o Firewall do Azure nos segmentos de rede virtual conforme apropriado, com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

* [Executar a detecção de intrusão de rede com o observador de rede e ferramentas de código-fonte aberto](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com o Firewall do Azure](../../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: você pode implantar aplicativo Azure gateway para aplicativos Web com HTTPS/SSL habilitado para certificados confiáveis. Com o gateway de Aplicativo Azure, você direciona o tráfego da Web do aplicativo para recursos específicos atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end, como máquinas virtuais do Windows.

* [Como implantar o gateway de aplicativo](../../application-gateway/quick-create-portal.md)

* [Como configurar o gateway de aplicativo para usar HTTPS](../../application-gateway/create-ssl-portal.md)

* [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../../application-gateway/overview.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir controles de acesso à rede em grupos de segurança de rede ou firewall do Azure configurados para suas máquinas virtuais do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

* [Entender e usar marcas de serviço](../../virtual-network/service-tags-overview.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para VMs (máquinas virtuais) do Azure usando o Azure Policy. Você também pode usar plantas do Azure para simplificar implantações de VM do Azure de grande escala empacotando artefatos de ambiente-chave, como modelos de Azure Resource Manager, atribuições de função e atribuições de Azure Policy, em uma única definição de Blueprint. Você pode aplicar o plano gráfico às assinaturas e habilitar o gerenciamento de recursos por meio do controle de versão do Blueprint.

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Exemplos de Azure Policy para rede](../../governance/policy/samples/built-in-policies.md#network)

* [Como criar um blueprint do Azure](../../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: você pode usar marcas para NSG (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego configurados para suas máquinas virtuais do Windows. Para regras NSG individuais, use o campo "Descrição" para especificar a necessidade de negócios e/ou a duração de qualquer regra que permita o tráfego de/para uma rede.

* [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o log de atividades do Azure para monitorar alterações em configurações de recursos de rede relacionadas às suas máquinas virtuais. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em configurações de rede ou recursos críticos.

Use Azure Policy para validar (e/ou corrigir) as configurações do recurso de rede relacionado a Máquinas Virtuais do Windows.

* [Como exibir e recuperar eventos do log de atividades do Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Exemplos de Azure Policy para rede](../../governance/policy/samples/built-in-policies.md#network)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para recursos do Azure, no entanto, você tem a opção de gerenciar as configurações de sincronização de tempo para seu máquinas virtuais do Windows.

* [Como configurar a sincronização de horário para recursos de computação do Azure](./time-sync.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: a central de segurança do Azure fornece monitoramento de log de eventos de segurança para máquinas virtuais do Windows. Dado o volume de dados gerado pelo log de eventos de segurança, ele não é armazenado por padrão.

* [Se sua organização quiser manter os dados do log de eventos de segurança da máquina virtual, ele poderá ser armazenado em uma camada de coleta de dados, em que ponto ele pode ser consultado em Log Analytics. Há diferentes camadas: mínima, comum e todas, que são detalhadas no link a seguir](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: os logs de atividades podem ser usados para auditar operações e ações executadas em recursos de máquina virtual. O log de atividades contém todas as operações de gravação (PUT, POST, DELETE) para seus recursos, exceto operações de leitura (GET). Os logs de atividades podem ser usados para encontrar um erro ao solucionar problemas ou para monitorar como um usuário em sua organização modificou um recurso.

Habilite a coleta de dados de diagnóstico do SO convidado implantando a extensão de diagnóstico em suas máquinas virtuais (VM). Você pode usar a extensão de diagnóstico para coletar dados de diagnóstico como logs de aplicativo ou contadores de desempenho de uma máquina virtual do Azure.

Para obter visibilidade avançada dos aplicativos e serviços com suporte nas máquinas virtuais, você pode habilitar o Azure Monitor para VMs e o Application insights. Com o Application Insights, você pode monitorar seu aplicativo e capturar a telemetria, como solicitações HTTP, exceções, etc., para que você possa correlacionar os problemas entre as VMs e seu aplicativo.

Além disso, habilite Azure Monitor para acesso aos seus logs de auditoria e de atividades que incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

* [Como coletar logs e métricas de plataforma com Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

* [Visão geral do agente do log Analytics](../../azure-monitor/platform/log-analytics-agent.md)

* [Extensão da máquina virtual do log Analytics para Windows](../extensions/oms-windows.md)

* [Exibir e recuperar eventos do log de atividades do Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Visão geral do Application Insights](../../azure-monitor/app/app-insights-overview.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: Use a central de segurança do Azure para fornecer monitoramento de log de eventos de segurança para máquinas virtuais do Azure. Dado o volume de dados gerado pelo log de eventos de segurança, ele não é armazenado por padrão.

Se sua organização quiser manter os dados do log de eventos de segurança da máquina virtual, ela poderá ser armazenada em um espaço de trabalho Log Analytics na camada de coleta de dados desejada configurada na central de segurança do Azure.

* [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md)

* [Para capturar os dados do syslog para monitoramento, será necessário habilitar a extensão Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Orientação**: Verifique se as contas de armazenamento ou os espaços de trabalho log Analytics usados para armazenar logs de máquina virtual têm o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

* [Como monitorar máquinas virtuais no Azure](../../azure-monitor/insights/monitor-vm-azure.md)

* [Como configurar Log Analytics período de retenção do espaço de trabalho](../../azure-monitor/platform/manage-cost-storage.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilite o agente de log Analytics, também conhecido como Microsoft Monitoring Agent (MMA) e configure-o para enviar logs para um espaço de trabalho log Analytics. O agente do Windows envia dados coletados de fontes diferentes para seu espaço de trabalho do Log Analytics no Azure Monitor, bem como quaisquer logs ou métricas exclusivos, conforme definido em uma solução de monitoramento.

Analise e monitore logs de comportamento anormal e Examine regularmente os resultados. Use Azure Monitor para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros para monitorar e examinar seus logs.

* [Visão geral do agente do log Analytics](../../azure-monitor/platform/log-analytics-agent.md)

* [Extensão da máquina virtual do log Analytics para Windows](../extensions/oms-windows.md)

* [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [Compreender o workspace do Log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md)

* [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: Use a central de segurança do Azure configurada com um espaço de trabalho log Analytics para monitoramento e alerta em atividade anômala encontrada em logs de segurança e eventos para suas máquinas virtuais do Azure.

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros para configurar alertas para atividades anormais.

* [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [Como gerenciar alertas na central de segurança do Azure](../../security-center/security-center-managing-and-responding-alerts.md)

* [Como alertar sobre dados de log do log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: você pode usar o Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais e configurar suas máquinas virtuais para registrar eventos em uma conta de armazenamento do Azure. Configure um espaço de trabalho Log Analytics para ingerir os eventos das contas de armazenamento e criar alertas quando apropriado. Siga as recomendações na central de segurança do Azure: "aplicativos de computação &amp; ".

* [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../../security/fundamentals/antimalware.md)

* [Como habilitar o monitoramento em nível de convidado para máquinas virtuais](../../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros do Azure Marketplace para a solução de registro em log DNS de acordo com suas necessidades de organização.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Orientação**: a central de segurança do Azure fornece monitoramento de log de eventos de segurança para VMs (máquinas virtuais) do Azure. A central de segurança provisiona a Microsoft Monitoring Agent em todas as VMs do Azure com suporte e quaisquer novas criadas se o provisionamento automático estiver habilitado ou se você puder instalar o agente manualmente. O agente habilita o evento de criação de processo 4688 e o campo CommandLine dentro do evento 4688. Novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da Central de Segurança.

* [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: embora Azure Active Directory seja o método recomendado para administrar o acesso do usuário, as máquinas virtuais do Azure podem ter contas locais. As contas locais e de domínio devem ser examinadas e gerenciadas, normalmente com uma superfície mínima. Além disso, aproveite o Azure Privileged Identity Management para contas administrativas usadas para acessar os recursos de máquinas virtuais.

* [As informações para contas locais estão disponíveis em](../../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Informações sobre o Privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Máquinas Virtuais do Windows e Azure Active Directory não têm o conceito de senhas padrão. Cliente responsável por aplicativos de terceiros e serviços do Marketplace que podem usar senhas padrão.

**Monitoramento da Central de Segurança do Azure**: Não disponível

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas que têm acesso às suas máquinas virtuais. Use o gerenciamento de acesso e identidade da central de segurança do Azure para monitorar o número de contas administrativas. Todas as contas de administrador usadas para acessar os recursos de máquina virtual do Azure também podem ser gerenciadas pelo PIM (Privileged Identity Management do Azure). O Azure Privileged Identity Management fornece várias opções, como a elevação just-in-time, exigindo a autenticação multifator antes de assumir uma função e opções de delegação para que as permissões só estejam disponíveis para intervalos de tempo específicos e exijam um Aprovador.

* [Entender a identidade e o acesso da central de segurança do Azure](../../security-center/security-center-identity-access.md)

* [Informações sobre o Privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: sempre que possível, o cliente pode usar o SSO com Azure Active Directory em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Logon único em aplicativos no Azure Active Directory](../../active-directory/manage-apps/what-is-single-sign-on.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: Habilite a MFA do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

* [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA configurada para fazer logon e configurar recursos do Azure.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: Use Azure ad PRIVILEGED Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use as detecções de risco do Azure AD para ver alertas e relatórios sobre o comportamento do usuário suspeito. Opcionalmente, o cliente pode ingerir alertas de detecção de riscos da central de segurança do Azure em Azure Monitor e configurar alertas/notificações personalizados usando grupos de ação.

* [Como implantar o Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Noções básicas sobre as detecções de riscos da central de segurança do Azure (atividade suspeita)](../../active-directory/identity-protection/overview-identity-protection.md)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como configurar grupos de ação para alertas e notificações personalizados](../../azure-monitor/platform/action-groups.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: Use Azure Active Directory políticas de acesso condicional e locais nomeados para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

* [Como configurar localizações nomeadas no Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário. Você pode usar identidades gerenciadas para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código. Seu código em execução em uma máquina virtual pode usar sua identidade gerenciada para solicitar tokens de acesso para serviços que dão suporte à autenticação do Azure AD.

* [Como criar e configurar uma instância do Azure AD](../../active-directory-domain-services/tutorial-create-instance.md)

* [Identidades gerenciadas para visão geral de recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use Azure Active Directory as revisões de acesso de identidade para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. Ao usar as máquinas virtuais do Azure, você precisará examinar os grupos de segurança locais e os usuários para garantir que não haja contas inesperadas que possam comprometer o sistema.

* [Como usar as revisões de acesso de identidade do Azure](../../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: definir configurações de diagnóstico para Azure Active Directory enviar os logs de auditoria e os logs de entrada para um espaço de trabalho log Analytics. Além disso, use Azure Monitor para examinar os logs e executar consultas em dados de log de máquinas virtuais do Azure.

* [Compreender o workspace do Log Analytics](../../azure-monitor/log-query/log-analytics-tutorial.md)

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Como realizar consultas personalizadas no Azure Monitor](../../azure-monitor/log-query/get-started-queries.md)

* [Como monitorar máquinas virtuais no Azure](../../azure-monitor/insights/monitor-vm-azure.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Orientação**: Use os recursos de proteção de risco e identidade do Azure Active Directory para configurar respostas automatizadas para ações suspeitas detectadas relacionadas aos recursos da sua conta de armazenamento. Você deve habilitar respostas automatizadas por meio do Azure Sentinel para implementar as respostas de segurança da sua organização.

* [Como exibir entradas suspeitas do Azure Active Directory](../../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: nos casos em que um terceiro precisa acessar os dados do cliente (como durante uma solicitação de suporte), use sistema de proteção de dados do cliente para que as máquinas virtuais do Azure revisem e aprovem ou rejeitem solicitações de acesso a dados do cliente.

* [Noções básicas sobre Sistema de Proteção de Dados do Cliente](../../security/fundamentals/customer-lockbox-overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas para ajudar a controlar as máquinas virtuais do Azure que armazenam ou processam informações confidenciais.

* [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por rede virtual/sub-rede, marcados adequadamente e protegidos em um NSG (grupo de segurança de rede) ou por um firewall do Azure. Para máquinas virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desligá-los quando não estiverem em uso.

* [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

* [Como criar uma Rede Virtual](../../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar o Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alerta ou alerta e negar com o Firewall do Azure](../../firewall/threat-intel.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: implemente uma solução de terceiros em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia tais transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial para proteger contra exposição e perda de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: os dados em trânsito para, de e entre as máquinas virtuais (VM) que executam o Windows são criptografados de várias maneiras, dependendo da natureza da conexão, como ao se conectar a uma VM em uma sessão RDP ou SSH.

A Microsoft usa o protocolo TLS para proteger dados quando está viajando entre os serviços de nuvem e os clientes.

* [Criptografia em trânsito em VMs](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: Use uma ferramenta de descoberta ativa de terceiros para identificar todas as informações confidenciais armazenadas, processadas ou transmitidas pelos sistemas de tecnologia da organização, incluindo aquelas localizadas no local ou em um provedor de serviços remoto e atualize o inventário de informações confidenciais da organização.

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: usando o Azure RBAC (controle de acesso baseado em função), você pode separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários em sua VM que eles precisam para executar seus trabalhos. Em vez de apresentar todas as permissões irrestritas na VM, você pode permitir que apenas determinadas ações. Você pode configurar o controle de acesso para a VM no portal do Azure, usando a CLI do Azure ou o Azure PowerShell.

* [RBAC do Azure](../../role-based-access-control/overview.md)

* [Funções internas do Azure](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: implemente uma ferramenta de terceiros, como uma solução de prevenção contra perda de dados baseada em host automatizada, para impor controles de acesso para atenuar o risco de violações de dados.

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: discos virtuais em máquinas virtuais do Windows (VM) são criptografados em repouso usando a criptografia do lado do servidor ou o Ade (Azure Disk Encryption). O Azure Disk Encryption aproveita o recurso BitLocker do Windows para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada. A criptografia do lado do servidor com chaves gerenciadas pelo cliente aprimora o ADE, permitindo usar quaisquer tipos de sistema operacional e imagens para as VMs, criptografando dados no serviço de armazenamento.

* [Criptografia do lado do servidor de discos gerenciados pelo Azure](../disk-encryption.md)

* [Azure Disk Encryption para VMs do Windows](./disk-encryption-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em máquinas virtuais e recursos relacionados.

* [Como criar alertas para eventos do log de atividades do Azure](../../azure-monitor/platform/alerts-activity-log.md)

* [Como criar alertas para eventos do log de atividades do Azure](../../azure-monitor/platform/alerts-activity-log.md)

* [Log da análise do Armazenamento do Azure](../../storage/common/storage-analytics-logging.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em suas máquinas virtuais do Azure. Use a solução recomendada de segurança do Azure ou de terceiros para executar avaliações de vulnerabilidade para suas máquinas virtuais.

* [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: Use a solução de gerenciamento de atualizações do Azure para gerenciar atualizações e patches para suas máquinas virtuais. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

* [Solução Gerenciamento de Atualizações no Azure](../../automation/update-management/overview.md)

* [Gerenciar atualizações e patches para suas VMs](../../automation/update-management/manage-updates-for-vm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Orientação**: você pode usar uma solução de gerenciamento de patches de terceiros. Você pode usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações e patches para suas máquinas virtuais. Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Ferramentas como System Center Updates Publisher (Updates Publisher) permitem que você publique atualizações personalizadas no Windows Server Update Services (WSUS). Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

* [Solução Gerenciamento de Atualizações no Azure](../../automation/update-management/overview.md)

* [Gerenciar atualizações e patches para suas VMs](../../automation/update-management/manage-updates-for-vm.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: exporte os resultados da verificação em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram corrigidas. Ao usar a recomendação de gerenciamento de vulnerabilidade sugerida pela central de segurança do Azure, o cliente pode dinamizar o portal da solução selecionada para exibir os dados de verificação históricas.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use as classificações de risco padrão (Pontuação segura) fornecidas pela central de segurança do Azure.

* [Entender a pontuação segura da central de segurança do Azure](../../security-center/secure-score-security-controls.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (incluindo máquinas virtuais) em suas assinaturas. Verifique se você tem permissões (leitura) apropriadas em seu locatário e é capaz de enumerar todas as assinaturas do Azure, bem como os recursos nas suas assinaturas.

* [Como criar consultas com o Azure Graph](../../governance/resource-graph/first-query-portal.md)

* [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Entender o RBAC do Azure](../../role-based-access-control/overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente de acordo com uma taxonomia.

* [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e rastrear máquinas virtuais e recursos relacionados. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

* [Como criar assinaturas adicionais do Azure](../../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Orientação**: você deve criar um inventário de recursos aprovados do Azure e software aprovado para seus recursos de computação. Você também pode usar controles de aplicativo adaptáveis, um recurso da central de segurança do Azure para ajudá-lo a definir um conjunto de aplicativos que têm permissão para serem executados em grupos de computadores configurados. Esse recurso está disponível para Windows Azure e não Azure (todas as versões, clássicas ou Azure Resource Manager) e computadores Linux.

         

* [Como habilitar o controle de aplicativo adaptável](../../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Além disso, use o Azure Resource Graph para consultar/descobrir recursos em sua(s) assinatura(s). Isso pode ajudar em ambientes baseados em alta segurança, como aqueles com contas de armazenamento.

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Orientação**: a automação do Azure fornece controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Aproveite o inventário de máquina virtual do Azure para automatizar a coleta de informações sobre todos os softwares em máquinas virtuais. Observação: o nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter acesso à data de instalação e outras informações, o cliente precisou habilitar o diagnóstico em nível de convidado e colocar os logs de eventos do Windows em um espaço de trabalho Log Analytics.

Além de usar Controle de Alterações para o monitoramento de aplicativos de software, os controles de aplicativo adaptáveis na central de segurança do Azure usam o aprendizado de máquina para analisar os aplicativos em execução em seus computadores e criar uma lista de permissões dessa inteligência. Esse recurso simplifica muito o processo de configuração e manutenção de políticas de lista de permissões de aplicativo, permitindo que você evite o uso de software indesejado em seu ambiente. Você pode configurar o modo de auditoria ou o modo impor. O modo de auditoria só audita a atividade nas VMs protegidas. O modo impor impõe as regras e verifica se os aplicativos que não têm permissão de execução estão bloqueados.

* [Uma introdução à Automação do Azure](../../automation/automation-intro.md)

* [Como habilitar o inventário de VM do Azure](../../automation/automation-tutorial-installed-software.md)

* [Entender os controles de aplicativo adaptáveis](../../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: a automação do Azure fornece controle completo durante a implantação, operações e encerramento de cargas de trabalho e recursos. Você pode usar Controle de Alterações para identificar todos os softwares instalados em máquinas virtuais. Você pode implementar seu próprio processo ou usar a configuração de estado da automação do Azure para remover software não autorizado.

* [Uma introdução à Automação do Azure](../../automation/automation-intro.md)

* [Controlar alterações no ambiente com a solução Controle de Alterações](../../automation/change-tracking/overview.md)

* [Visão geral da configuração do estado de automação do Azure](../../automation/automation-dsc-overview.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Orientação**: Use os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use a política do Azure para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:-não são tipos de recursos permitidos
- Tipos de recursos permitidos

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Orientação**: o controle de aplicativo adaptável é uma solução inteligente, automatizada e de ponta a ponta da central de segurança do Azure que ajuda a controlar quais aplicativos podem ser executados em suas máquinas do Azure e não Azure (Windows e Linux). Implemente uma solução de terceiros se isso não atender aos requisitos da sua organização.

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Orientação**: dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure. Você também pode aproveitar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

* [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: aplicativos de alto risco implantados em seu ambiente do Azure podem ser isolados usando rede virtual, sub-rede, assinaturas, grupos de gerenciamento, etc. e suficientemente protegidos com um firewall do Azure, o WAF (firewall do aplicativo Web) ou o NSG (grupo de segurança de rede).

* [Redes virtuais e máquinas virtuais no Azure](../network-overview.md)

* [Visão geral do Firewall do Azure](../../firewall/overview.md)

* [Visão geral de Firewall de Aplicativo Web](../../web-application-firewall/overview.md)

* [Visão geral da segurança da rede](../../virtual-network/network-security-groups-overview.md)

* [Visão geral da rede virtual do Azure](../../virtual-network/virtual-networks-overview.md)

* [Organizar seus recursos com grupos de gerenciamento do Azure](../../governance/management-groups/overview.md)

* [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: Use Azure Policy ou a central de segurança do Azure para manter as configurações de segurança para todos os recursos do Azure. Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua empresa.

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Informações sobre como baixar o modelo de VM](/previous-versions/azure/virtual-machines/windows/download-template)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: Use a recomendação da central de segurança do Azure [corrigir vulnerabilidades em configurações de segurança em suas máquinas virtuais] para manter as configurações de segurança em todos os recursos de computação.

* [Como monitorar as recomendações da central de segurança do Azure](../../security-center/security-center-recommendations.md)

* [Como corrigir as recomendações da central de segurança do Azure](../../security-center/security-center-remediate-recommendations.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use modelos de Azure Resource Manager e políticas do Azure para configurar com segurança os recursos do Azure associados às máquinas virtuais. Azure Resource Manager modelos são arquivos baseados em JSON usados para implantar a máquina virtual junto com os recursos do Azure e o modelo personalizado precisará ser mantido. A Microsoft realiza a manutenção nos modelos de base. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

* [Informações sobre como criar modelos de Azure Resource Manager](./ps-template.md)

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

* [Compreendendo os efeitos do Azure Policy](../../governance/policy/concepts/effects.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: há várias opções para manter uma configuração segura para VMs (máquinas virtuais) do Azure para implantação:

1-modelos de Azure Resource Manager: são arquivos baseados em JSON usados para implantar uma VM do portal do Azure, e o modelo personalizado precisará ser mantido. A Microsoft realiza a manutenção nos modelos de base.

2-disco rígido virtual (VHD) personalizado: em algumas circunstâncias, pode ser necessário ter arquivos VHD personalizados usados, como ao lidar com ambientes complexos que não podem ser gerenciados por outros meios.

3-configuração de estado da automação do Azure: depois que o sistema operacional base for implantado, isso poderá ser usado para um controle mais granular das configurações e imposto por meio da estrutura de automação.

Para a maioria dos cenários, os modelos de VM base da Microsoft combinados com a configuração de estado desejado da automação do Azure podem ajudar na reunião e manutenção dos requisitos de segurança.

* [Informações sobre como baixar o modelo de VM](/previous-versions/azure/virtual-machines/windows/download-template)

* [Informações sobre a criação de modelos do ARM](./ps-template.md)

* [Como carregar um VHD de VM personalizado para o Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910&preserve-view=true)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use Azure Repos para armazenar e gerenciar com segurança seu código, como políticas personalizadas do Azure, modelos de Azure Resource Manager, scripts de configuração de estado desejado etc. Para acessar os recursos que você gerencia no Azure DevOps, como seu código, compilações e acompanhamento de trabalho, você deve ter permissões para esses recursos específicos. A maioria das permissões é concedida por meio de grupos de segurança internos, conforme descrito em permissões e acesso. Você pode conceder ou negar permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure) se integrados ao Azure DevOps ou Active Directory se integrado ao TFS.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Orientação**: se estiver usando imagens personalizadas (por exemplo, disco rígido virtual), use o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que somente usuários autorizados possam acessar as imagens.

* [Entender o RBAC do Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Como configurar o RBAC do Azure](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Aproveite Azure Policy para alertar, auditar e impor configurações do sistema para suas máquinas virtuais. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

* [Como configurar e gerenciar o Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: a configuração de estado da automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](../../automation/automation-dsc-onboarding.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Aproveite a central de segurança do Azure para executar verificações de linha de base para suas máquinas virtuais do Azure. Métodos adicionais para configuração automatizada incluem o uso da configuração de estado de automação do Azure.

* [Como corrigir recomendações na central de segurança do Azure](../../security-center/security-center-remediate-recommendations.md)

* [Introdução à Configuração de Estado da Automação do Azure](../../automation/automation-dsc-getting-started.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: a configuração de estado da automação do Azure é um serviço de gerenciamento de configuração para nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Ela permite a escalabilidade entre milhares de máquinas rápida e facilmente a partir de um local central e seguro. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado.

* [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](../../automation/automation-dsc-onboarding.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

* [Como integrar com identidades de Azure-Managed](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Como criar um Key Vault](../../key-vault/general/quick-create-portal.md)

* [Como autenticar-se no Key Vault](../../key-vault/general/authentication.md)

* [Como atribuir uma política de acesso de Key Vault](../../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

* [Como configurar identidades gerenciadas](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: Use o Microsoft antimalware para máquinas virtuais do Windows do Azure para monitorar e defender continuamente seus recursos.

* [Como configurar o Microsoft antimalware para serviços de nuvem e máquinas virtuais](../../security/fundamentals/antimalware.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Orientação**: não aplicável a máquinas virtuais do Azure, pois é um recurso de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: quando implantado, o Microsoft antimalware para Azure instalará automaticamente as atualizações de assinatura, plataforma e mecanismo mais recentes por padrão. Siga as recomendações na central de segurança do Azure: " &amp; aplicativos de computação" para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. O sistema operacional Windows pode estar mais protegido com segurança adicional para limitar o risco de ataques baseados em vírus ou malware com o serviço de proteção avançada contra ameaças do Microsoft defender, que se integra à central de segurança do Azure.

* [Como implantar o Microsoft antimalware para serviços de nuvem e máquinas virtuais do Azure](../../security/fundamentals/antimalware.md)

* [Proteção Avançada contra Ameaças do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: habilitar o backup do Azure e configurar as máquinas virtuais (VM) do Azure, bem como a frequência e o período de retenção desejados para backups automáticos.

* [Uma visão geral do backup de VM do Azure](../../backup/backup-azure-vms-introduction.md)

* [Fazer backup de uma VM do Azure usando as configurações da VM](../../backup/backup-azure-vms-first-look-arm.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: Crie instantâneos de suas máquinas virtuais do Azure ou os discos gerenciados anexados a essas instâncias usando o PowerShell ou APIs REST. Faça backup de qualquer chave gerenciada pelo cliente no Azure Key Vault.

Habilite o backup do Azure e as máquinas virtuais (VM) do Azure de destino, bem como os períodos de frequência e retenção desejados. Isso inclui o backup completo do estado do sistema. Se você estiver usando o Azure Disk Encryption, o backup de VM do Azure manipulará automaticamente o backup de chaves gerenciadas pelo cliente.

* [Backup em VMs do Azure que usam criptografia](../../backup/backup-azure-vms-encryption.md)

* [Visão geral do backup de VM do Azure](../../backup/backup-azure-vms-introduction.md)

* [Uma visão geral do backup de VM do Azure](../../backup/backup-azure-vms-introduction.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste o conteúdo de restauração em uma assinatura ou rede virtual isolada. Cliente para testar a restauração de chaves de backup gerenciadas pelo cliente.

Se você estiver usando o Azure Disk Encryption, poderá restaurar a VM do Azure com as chaves de criptografia de disco. Ao usar a criptografia de disco, você pode restaurar a VM do Azure com as chaves de criptografia de disco.

* [Backup em VMs do Azure que usam criptografia](../../backup/backup-azure-vms-encryption.md)

* [Como recuperar arquivos do backup de máquina virtual do Azure](../../backup/backup-azure-restore-files-from-vm.md)

* [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Como fazer backup e restaurar uma VM criptografada](../../backup/backup-azure-vms-encryption.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: quando você faz backup de discos gerenciados pelo Azure com o backup do Azure, as VMs são criptografadas em repouso com o criptografia do serviço de armazenamento (SSE). O backup do Azure também pode fazer backup de VMs do Azure que são criptografadas usando Azure Disk Encryption. O Azure Disk Encryption se integra com as chaves de criptografia do BitLocker (BEKs), que são protegidas em um cofre de chaves como segredos. O Azure Disk Encryption também se integra com Azure Key Vault chaves de criptografia de chave (KEKs). Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

* [Exclusão reversível para VMs](../../backup/soft-delete-virtual-machines.md)

* [Visão geral de exclusão reversível do Azure Key Vault](../../key-vault/general/soft-delete-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

* [Alertas na Central de Segurança do Azure](../../security-center/security-center-alerts-overview.md)

* [Usar marcas para organizar seus recursos do Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato de segurança da Central de Segurança do Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

* [Como configurar a exportação contínua](../../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

* [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../../security-center/workflow-automation.md)

**Monitoramento da central de segurança do Azure**: não disponível

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

* [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../../security/benchmarks/security-baselines-overview.md)