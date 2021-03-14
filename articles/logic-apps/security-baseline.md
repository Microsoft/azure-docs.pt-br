---
title: Linha de base de segurança do Azure para aplicativos lógicos
description: A linha de base de segurança dos aplicativos lógicos fornece orientações de procedimentos e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cf4e02f925e0c8c4024137589c0c9c4074c0aa11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596753"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Linha de base de segurança do Azure para aplicativos lógicos

A linha de base de segurança do Azure para aplicativos lógicos contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: conectores que são executados no serviço de aplicativos lógicos multilocatário "global" são implantados e gerenciados pela Microsoft. Esses conectores fornecem gatilhos e ações para acessar serviços de nuvem, sistemas locais ou ambos, incluindo o Office 365, o armazenamento de BLOBs do Azure, SQL Server, Dynamics, Salesforce, SharePoint e muito mais. Você pode usar a marca de serviço AzureConnectors ao especificar regras no grupo de segurança de rede ou no firewall do Azure para permitir o acesso a recursos relacionados.

Para aplicativos lógicos que precisam de acesso direto aos recursos em uma rede virtual do Azure, você pode criar um ISE (ambiente do serviço de integração) no qual você pode criar, implantar e executar seus aplicativos lógicos em recursos dedicados. Algumas redes virtuais do Azure usam pontos de extremidade privados (link privado do Azure) para fornecer acesso aos serviços de PaaS do Azure, como o armazenamento do Azure, Azure Cosmos DB, banco de dados SQL do Azure, serviços de parceiro ou serviços de cliente hospedados no Azure. Se seus aplicativos lógicos precisarem de acesso a redes virtuais que usam pontos de extremidade privados, você deverá criar, implantar e executar esses aplicativos lógicos dentro de um ISE.

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual. Os pontos de extremidade de acesso interno e externo também afetam se você pode exibir o histórico de execução do aplicativo lógico, incluindo as entradas e saídas de uma execução, de dentro ou de fora de sua rede virtual.

Certifique-se de que todas as implantações de sub-rede de rede virtual relacionadas ao ISE tenham um grupo de segurança de rede aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo. Ao implantar seus aplicativos lógicos em um ISE, use o link privado. O link privado do Azure permite que você acesse os serviços de PaaS do Azure e os serviços hospedados do cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual. Como alternativa, se você tiver um caso de uso específico, poderá atender a esse requisito implementando o Firewall do Azure. Para ajudar a reduzir a complexidade ao configurar regras de segurança, use marcas de serviço que representem grupos de prefixos de endereço IP para um serviço específico do Azure.

- [Entender os conectores para aplicativos lógicos](../connectors/apis-list.md)

- [Entender as marcas de serviço no Azure](../virtual-network/service-tags-overview.md)

- [Entender o acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure usando ambientes de serviço de integração (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Entender os pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Entender o link privado do Azure](../private-link/private-link-overview.md)

- [Entender o acesso ao ponto de extremidade do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como habilitar o acesso para ISE](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: se você executar aplicativos lógicos em um ambiente do serviço de integração (ISE) que usa um ponto de acesso externo, você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Entender o acesso ao ponto de extremidade do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: se seu aplicativo lógico usa um gatilho baseado em solicitação, que recebe chamadas ou solicitações de entrada, como o gatilho de solicitação ou webhook, você pode limitar o acesso para que somente clientes autorizados possam chamar seu aplicativo lógico.

Se você estiver executando aplicativos lógicos em um ambiente do serviço de integração (ISE), habilite a proteção contra DDoS Standard na rede virtual associada ao ISE para proteger contra ataques de DDoS. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

Implante o Firewall do Azure em cada um dos limites de rede da organização com inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

Use o acesso à rede just in time da central de segurança do Azure para configurar o NSGs para limitar a exposição de pontos de extremidade a endereços IP aprovados por um período limitado.

Use a proteção de rede adaptável da central de segurança do Azure para recomendar configurações de NSG que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.

- [Como proteger chamadas de entrada para aplicativos lógicos](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Como restringir endereços IP de entrada](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

- [Entender a proteção de rede adaptável da central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Entender o controle de acesso à rede just in time da central de segurança do Azure](../security-center/security-center-just-in-time.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: se você estiver executando aplicativos lógicos em um ambiente do serviço de integração (ISE) que usa um ponto de acesso externo, você pode usar um NSG (grupo de segurança de rede) para reduzir o risco de vazamento de dados. Habilite logs de fluxo NSG e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Para fornecer mais proteção e informações sobre o tráfego de rede, você pode consultar os logs de acesso, que são gerados somente se você os tiver habilitado em cada instância do gateway de aplicativo. Você pode usar esse log para exibir os padrões de acesso do Gateway de Aplicativo e analisar informações importantes. Isso inclui o IP do chamador, a URL solicitada, a latência da resposta, o código de retorno, os bytes de entrada e saída.

Caso contrário, você pode aproveitar uma solução de terceiros do Marketplace para atender a esse requisito.

- [Entender o acesso ao ponto de extremidade do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como integrar o gerenciamento de API em uma VNET interna com o gateway de aplicativo](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Como entender os logs de acesso do WAF](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.  Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: se você estiver executando aplicativos lógicos em um ambiente do serviço de integração (ISE), implante aplicativo Azure gateway.

- [Como integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Como configurar o gateway de aplicativo para usar HTTPS](../application-gateway/create-ssl-portal.md) 

- [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../application-gateway/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: para recursos que precisam de acesso às instâncias de aplicativos lógicos do Azure, use as marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, LogicApps, LogicAppsManagement) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede relacionados a suas instâncias de aplicativos lógicos do Azure com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. Logic" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de suas instâncias de aplicativos lógicos do Azure. Você também pode fazer uso de definições de política internas, como:

- Os logs de diagnóstico nos Aplicativos Lógicos devem ser habilitados

- A Proteção contra DDoS Standard deve ser habilitada

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: para regras NSG individuais, use o campo "Descrição" para especificar a necessidade de negócios e/ou duração (etc.) para qualquer regra que permita o tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Lista de definições de Azure Policy para aplicativos lógicos](./policy-reference.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às instâncias de aplicativos lógicos do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como aplicativos lógicos do Azure para carimbos de data/hora nos logs.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: para obter informações de depuração mais ricas sobre seus aplicativos lógicos durante o tempo de execução, você pode configurar e usar logs de Azure monitor para registrar e armazenar informações sobre dados de tempo de execução e eventos, como eventos de gatilho, eventos de execução e eventos de ação em um espaço de trabalho log Analytics. O Azure Monitor ajuda você a monitorar os ambientes locais e na nuvem para que você possa manter com mais facilidade a disponibilidade e o desempenho deles. Usando logs de Azure Monitor, você pode criar consultas de log que ajudam a coletar e examinar essas informações. Você também pode usar esses dados de diagnóstico com outros serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como configurar logs de Azure Monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](monitor-logic-apps-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: para obter informações de depuração mais ricas sobre seus aplicativos lógicos durante o tempo de execução, você pode configurar e usar logs de Azure monitor para registrar e armazenar informações sobre dados de tempo de execução e eventos, como eventos de gatilho, eventos de execução e eventos de ação em um espaço de trabalho log Analytics. O Azure Monitor ajuda você a monitorar os ambientes locais e na nuvem para que você possa manter com mais facilidade a disponibilidade e o desempenho deles. Usando logs de Azure Monitor, você pode criar consultas de log que ajudam a coletar e examinar essas informações. Você também pode usar esses dados de diagnóstico com outros serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como configurar logs de Azure Monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](monitor-logic-apps-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: depois de criar e executar um aplicativo lógico, você pode verificar o status de execução do aplicativo lógico, o histórico de execuções, o histórico de gatilho e o desempenho. Para monitoramento de eventos em tempo real e depuração mais avançada, configure o log de diagnóstico para seu aplicativo lógico usando logs de Azure Monitor. Esse serviço do Azure ajuda a monitorar seus ambientes locais e de nuvem para que você possa manter a disponibilidade e o desempenho com mais facilidade. Em seguida, você pode localizar e exibir eventos, como eventos de gatilho, eventos de execução e eventos de ação. Ao armazenar essas informações em logs de Azure Monitor, você pode criar consultas de log que ajudam a localizar e analisar essas informações. Você também pode usar esses dados de diagnóstico com outros serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure.

No Azure Monitor, defina o período de retenção de log para logs associados às instâncias dos aplicativos lógicos do Azure de acordo com os regulamentos de conformidade da sua organização.

- [Como monitorar o status de execução, examinar o histórico de gatilho e configurar alertas para aplicativos lógicos do Azure](monitor-logic-apps.md)

- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: para configurar o registro em log para seu aplicativo lógico, você pode habilitar log Analytics ao criar seu aplicativo lógico ou pode instalar a solução de gerenciamento de aplicativos lógicos em seu espaço de trabalho log Analytics para aplicativos lógicos existentes. Essa solução fornece informações agregadas para suas execuções de aplicativo lógico e inclui detalhes específicos, como status, tempo de execução, status de reenvio e IDs de correlação. Em seguida, para habilitar o registro em log e criar consultas para essas informações, configure Azure Monitor logs.

Você também pode habilitar as configurações de diagnóstico do log de atividades do Azure e enviar os logs para um espaço de trabalho Log Analytics. Execute consultas em Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos dados do log de atividades que podem ter sido coletados para os aplicativos lógicos do Azure.

Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como configurar logs de Azure Monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](monitor-logic-apps-log-analytics.md)

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como coletar e analisar os logs de atividades do Azure em Log Analytics no Azure Monitor](../azure-monitor/essentials/activity-log.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança do Azure com log Analytics para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; Os aplicativos lógicos do Azure não processam nem produzem logs relacionados a anti-malware.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: Habilitar o registro em log de consultas DNS

**Orientação**: não aplicável; O aplicativo lógico do Azure não processa nem produz logs relacionados ao DNS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: Habilitar o registro em log de auditoria de linha de comando

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure Active Directory (AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Para acessar facilmente outros recursos protegidos pelo Azure AD (Azure Active Directory) e autenticar sua identidade sem precisar entrar, o aplicativo lógico pode usar uma identidade gerenciada (anteriormente conhecida como Identidade de Serviço Gerenciada ou MSI), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos.

Cada ponto de extremidade de solicitação em um aplicativo lógico tem uma assinatura de acesso compartilhado (SAS) na URL do ponto de extremidade. Se você compartilhar a URL do ponto de extremidade para um gatilho baseado em solicitação com outras partes, poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de validade. Dessa forma, você pode reverter tranquilamente as chaves ou restringir o acesso para disparar seu aplicativo lógico com base em um período específico.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Autenticar o acesso a recursos do Azure usando identidades gerenciadas em Aplicativos Lógicos do Azure](create-managed-service-identity.md)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Como proteger o acesso e os dados nos aplicativos lógicos do Azure usando SAS](logic-apps-securing-a-logic-app.md#sas)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory e aplicativos lógicos do Azure não têm o conceito de senhas padrão.

Se a autenticação básica estiver sendo usada, será necessário especificar um nome de usuário e uma senha. Ao criar essas credenciais, certifique-se de configurar uma senha forte para autenticação.

Se você estiver usando a infraestrutura como código, evite armazenar senhas no código e, em vez disso, use Azure Key Vault para armazenar e recuperar credenciais.

- [Como proteger e acessar dados em aplicativos lógicos](logic-apps-securing-a-logic-app.md)

- [Como definir e recuperar um segredo de Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seus cofres de serviços de recuperação por meio de chamadas à API.

Muitos conectores também exigem que você primeiro crie uma conexão com o serviço ou sistema de destino e forneça credenciais de autenticação ou outros detalhes de configuração para poder usar um gatilho ou uma ação em seu aplicativo lógico. Por exemplo, você deve autorizar uma conexão com uma conta do Twitter para acessar dados ou postar em seu nome.

Para conectores que usam Azure Active Directory (Azure AD) OAuth, criar uma conexão significa entrar no serviço, como o Office 365, Salesforce ou GitHub, em que seu token de acesso é criptografado e armazenado com segurança em um repositório de segredo do Azure. Outros conectores, como FTP e SQL, exigem uma conexão com detalhes de configuração, como o endereço do servidor, o nome de usuário e a senha. Esses detalhes de configuração de conexão também são criptografados e armazenados com segurança.

- [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente no Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Fluxo de trabalho dispara informações de API](/rest/api/logic/workflowtriggers)

- [Entender a configuração do conector](../connectors/apis-list.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretriz**: Habilite a autenticação multifator (MFA) do Azure Active Directory (AD)e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretriz**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretriz**: Use o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: Use localizações nomeadas de acesso condicional para permitir o acesso ao portal do Azure somente a agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

Além disso, cada ponto de extremidade de solicitação em um aplicativo lógico tem uma assinatura de acesso compartilhado (SAS) na URL do ponto de extremidade. Você pode restringir seu aplicativo lógico para aceitar solicitações somente de determinados endereços IP.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Entenda como restringir os endereços IP de entrada nos aplicativos lógicos](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para suas instâncias de aplicativos lógicos do Azure. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Onde há suporte em aplicativos lógicos, use uma identidade gerenciada para acessar facilmente outros recursos protegidos pelo Azure Active Directory (AD do Azure) e autentique sua identidade sem entrar, em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos.

Os Aplicativos Lógicos do Azure dão suporte a identidades gerenciadas atribuídas pelo sistema e atribuídas pelo usuário. Seu aplicativo lógico pode usar a identidade atribuída ao sistema ou uma única identidade atribuída ao usuário, que você pode compartilhar em um grupo de aplicativos lógicos, mas não ambas. No momento, somente gatilhos e ações internos específicos dão suporte a identidades gerenciadas, não conectores ou conexões gerenciadas, por exemplo:

-  HTTP

-  Funções do Azure

-  Gerenciamento de API do Azure

-  Serviços de Aplicativo do Azure 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autenticar o acesso a recursos do Azure usando identidades gerenciadas em Aplicativos Lógicos do Azure](create-managed-service-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use o Azure Active Directory (AD) como o sistema de autenticação e autorização central para suas instâncias de aplicativos lógicos do Azure. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com o Azure Sentinel ou um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de identidade e proteção contra riscos do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. 

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: atualmente não disponível; O Sistema de Proteção de Dados do Cliente ainda não tem suporte para aplicativos lógicos do Azure.

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: conectores que são executados no serviço de aplicativos lógicos multilocatário "global" são implantados e gerenciados pela Microsoft. Esses conectores fornecem gatilhos e ações para acessar serviços de nuvem, sistemas locais ou ambos, incluindo o Office 365, o armazenamento de BLOBs do Azure, SQL Server, Dynamics, Salesforce, SharePoint e muito mais.

Para aplicativos lógicos que precisam de acesso direto aos recursos em uma rede virtual do Azure, você pode criar um ISE (ambiente do serviço de integração) no qual você pode criar, implantar e executar seus aplicativos lógicos em recursos dedicados. Algumas redes virtuais do Azure usam pontos de extremidade privados (link privado do Azure) para fornecer acesso aos serviços de PaaS do Azure, como o armazenamento do Azure, Azure Cosmos DB ou banco de dados SQL do Azure, serviços de parceiro ou serviços de cliente hospedados no Azure. Se seus aplicativos lógicos precisarem de acesso a redes virtuais que usam pontos de extremidade privados, você deverá criar, implantar e executar esses aplicativos lógicos dentro de um ISE.

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual.

Além disso, implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do Azure RBAC (controle de acesso baseado em função).

- [Entender os conectores para aplicativos lógicos](../connectors/apis-list.md)

- [Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md) 

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: atualmente não disponível; os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para aplicativos lógicos do Azure.

Aproveite uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências ao alertar os profissionais de segurança de informações. 

A Microsoft gerencia a infraestrutura subjacente para os aplicativos lógicos do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Nos aplicativos lógicos do Azure, todos os dados durante a execução de um aplicativo lógico são criptografados durante o trânsito usando TLS (segurança de camada de transporte) e em repouso. Quando você exibe o histórico de execução do aplicativo lógico, os Aplicativos Lógicos autenticam seu acesso e fornecem links de entradas e saídas para as solicitações e respostas de cada execução. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, convém impedir que outras pessoas exibam e acessem esses dados. Por exemplo, se o seu aplicativo lógico obtiver um segredo do Azure Key Vault para usar ao autenticar uma ação HTTP, oculte esse segredo da exibição.

O gatilho de solicitação dá suporte apenas ao protocolo TLS 1,2 para solicitações de entrada. Certifique-se de que todos os clientes que se conectam aos recursos do Azure sejam capazes de negociar o TLS 1,2 ou superior. Chamadas de saída usando o conector HTTP dão suporte ao protocolo TLS 1,0, 1,1 e 1,2. 

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Proteger o acesso e os dados em aplicativos lógicos do Azure-chamadas de entrada para gatilhos baseados em solicitação](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Proteger o acesso e os dados em aplicativos lógicos do Azure-chamadas de saída para outros serviços e sistemas](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Entender a criptografia de dados em repouso com o Azure](../security/fundamentals/encryption-atrest.md)

- [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure](customer-managed-keys-integration-service-environment.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: em aplicativos lógicos do Azure, muitos gatilhos e ações têm configurações que você pode habilitar para proteger entradas, saídas ou ambas ocultando os dados do histórico de execução de um aplicativo lógico.

A Microsoft gerencia a infraestrutura subjacente para os aplicativos lógicos do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Proteger o acesso aos dados do histórico de execução](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Orientação**: você pode permitir que somente usuários ou grupos específicos executem tarefas específicas, como gerenciar, editar e exibir aplicativos lógicos. Para controlar suas permissões, use o controle de acesso baseado em função do Azure (RBAC do Azure) para que você possa atribuir funções personalizadas ou internas aos membros em sua assinatura do Azure:

- Colaborador de Aplicativo Lógico: Permite o gerenciamento de aplicativos lógicos, mas você não pode alterar o acesso a eles.
- Operador de Aplicativo Lógico: Permite a leitura, habilitação e desabilitação de aplicativos lógicos, mas você não pode editá-los ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar Bloqueio de Recursos do Azure. Essa funcionalidade ajuda a evitar que outras pessoas alterem ou excluam recursos de produção.

- [Proteger o acesso às operações de aplicativos lógicos do Azure](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. A Microsoft gerencia a infraestrutura subjacente para os aplicativos lógicos do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Proteção de dados do cliente do Microsoft Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: os aplicativos lógicos do Azure dependem do armazenamento do Azure para armazenar e criptografar automaticamente os dados em repouso. Essa criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Por padrão, o armazenamento do Azure usa chaves gerenciadas pela Microsoft para criptografar seus dados.

Quando você cria um ambiente do serviço de integração (ISE) para hospedar seus aplicativos lógicos e deseja obter mais controle sobre as chaves de criptografia usadas pelo armazenamento do Azure, você pode configurar, usar e gerenciar sua própria chave usando Azure Key Vault. Esse recurso também é conhecido como "Bring Your Own Key" (BYOK) e sua chave é chamada de "chave gerenciada pelo cliente".

- [Criptografar dados em repouso para ambientes de serviço de integração nos aplicativos lógicos do Azure](customer-managed-keys-integration-service-environment.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em aplicativos lógicos do Azure, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte aos aplicativos lógicos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte aos aplicativos lógicos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte aos aplicativos lógicos do Azure.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas.  Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável que você crie e use Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário de recursos aprovados do Azure (como conectores) e softwares aprovados para recursos de computação de acordo com suas necessidades organizacionais.

Observação: devido às políticas de privacidade e dados do Google, você pode usar o conector do Gmail somente com serviços aprovados pelo Google. Essa situação está evoluindo e pode afetar outros conectores do Google no futuro.

- [Lista de todos os conectores de aplicativos lógicos](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Entender problemas e limitações para conectores do gmail](/connectors/gmail/#known-issues-and-limitations)

- [Mais informações sobre a política de privacidade do Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. 

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

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

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: recursos relacionados aos seus aplicativos lógicos que são necessários para operações de negócios, mas podem incorrer em um risco maior para a organização, devem ser isolados em sua própria máquina virtual e/ou rede virtual e protegidos suficientemente com um firewall do Azure ou grupo de segurança de rede.

Aplicativos lógicos que são necessários para operações de negócios, mas podem incorrer em um risco maior para a organização, devem ser isolados sempre que possível por meio de grupos de recursos separados com permissões específicas e limites de RBAC do Azure.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md) 

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md) 

- [Como proteger o acesso a aplicativos lógicos por meio do RBAC do Azure](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para suas instâncias de aplicativos lógicos do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Logic" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de aplicativos lógicos. Por exemplo, você pode impedir que outras pessoas criem ou usem conexões com recursos em que você deseja restringir o acesso.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

Além disso, use parâmetros protegidos para proteger dados confidenciais e segredos.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Bloquear conexões criadas por conectores em aplicativos lógicos do Azure](block-connections-connectors.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Como implantar modelos de Azure Resource Manager para aplicativos lógicos do Azure](logic-apps-deploy-azure-resource-manager-templates.md)

- [Entender os parâmetros de ação segura](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Defina e implemente configurações de segurança padrão para suas instâncias de aplicativos lógicos do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Logic" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias de aplicativos lógicos. Por exemplo, você pode impedir que outras pessoas criem ou usem conexões com recursos em que você deseja restringir o acesso.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

Além disso, certifique-se de proteger os dados no histórico de execução usando ofuscação.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

- [Bloquear conexões criadas por conectores em aplicativos lógicos do Azure](block-connections-connectors.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Como implantar modelos de Azure Resource Manager para aplicativos lógicos do Azure](logic-apps-deploy-azure-resource-manager-templates.md)

- [Proteger o acesso a entradas e saídas do histórico de execução](logic-apps-securing-a-logic-app.md#obfuscate)

- [Acesso seguro a entradas de parâmetro](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

Além disso, Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam/excedam os requisitos de segurança para sua organização.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Logic" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Logic" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use Azure Policy [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: proteger entradas e saídas no histórico de execução do aplicativo lógico usando ofuscação. Se você implantar em ambientes diferentes, considere a possibilidade de parametrizar os valores na definição de fluxo de trabalho do aplicativo lógico que variam de acordo com esses ambientes. Dessa forma, você pode evitar dados embutidos em código usando um modelo do Azure Resource Manager para implantar seu aplicativo lógico, proteger dados confidenciais definindo parâmetros protegidos e passar esses dados como entradas separadas por meio dos parâmetros do modelo usando um arquivo de parâmetro. Você pode usar Key Vault para armazenar dados confidenciais e usar parâmetros de modelo protegidos que recuperam esses valores de Key Vault na implantação. Em seguida, você pode fazer referência ao cofre de chaves e aos segredos em seu arquivo de parâmetro. 

Quando você cria um ambiente do serviço de integração (ISE) para hospedar seus aplicativos lógicos e deseja obter mais controle sobre as chaves de criptografia usadas pelo armazenamento do Azure, você pode configurar, usar e gerenciar sua própria chave usando Azure Key Vault. Esse recurso também é conhecido como "Bring Your Own Key" (BYOK) e sua chave é chamada de "chave gerenciada pelo cliente".

- [Proteger entradas e saídas no histórico de execuções em aplicativos lógicos do Azure](logic-apps-securing-a-logic-app.md#obfuscate)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Proteger o acesso a entradas de parâmetro nos aplicativos lógicos do Azure](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Passar valores de parâmetros seguros durante a implantação usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure](customer-managed-keys-integration-service-environment.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: para acessar facilmente outros recursos protegidos pelo Azure Active Directory (AD do Azure) e autenticar sua identidade sem entrar, seu aplicativo lógico pode usar uma identidade gerenciada (antigamente identidade de serviço gerenciada ou msi), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais, porque você não precisa fornecer ou trocar segredos.

No momento, somente gatilhos e ações internos específicos dão suporte a identidades gerenciadas, não conectores ou conexões gerenciadas, por exemplo:

- HTTP
- Funções do Azure
- Gerenciamento de API do Azure
- Serviços de Aplicativo do Azure

- [Como autenticar o acesso aos recursos do Azure usando identidades gerenciadas em aplicativos lógicos do Azure](create-managed-service-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: proteger entradas e saídas no histórico de execução do aplicativo lógico usando ofuscação. Se você implantar em ambientes diferentes, considere a possibilidade de parametrizar os valores na definição de fluxo de trabalho do aplicativo lógico que variam de acordo com esses ambientes. Dessa forma, você pode evitar dados embutidos em código usando um modelo do Azure Resource Manager para implantar seu aplicativo lógico, proteger dados confidenciais definindo parâmetros protegidos e passar esses dados como entradas separadas por meio dos parâmetros do modelo usando um arquivo de parâmetro. Você pode usar Key Vault para armazenar dados confidenciais e usar parâmetros de modelo protegidos que recuperam esses valores de Key Vault na implantação. Em seguida, você pode fazer referência ao cofre de chaves e aos segredos em seu arquivo de parâmetro. 

Você também pode implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Proteger entradas e saídas no histórico de execuções em aplicativos lógicos do Azure](logic-apps-securing-a-logic-app.md#obfuscate)

- [Recomendações de segurança para parâmetros](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Proteger o acesso a entradas de parâmetro nos aplicativos lógicos do Azure](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Passar valores de parâmetros seguros durante a implantação usando Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, aplicativos lógicos do Azure), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, backup do Azure), no entanto, ele não é executado no seu conteúdo. 

Pré-examinar todos os arquivos que estão sendo carregados em recursos que não são de computação do Azure, como serviço de aplicativo, Data Lake Storage, armazenamento de BLOBs, etc. 

Use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento. 

- [Entender o antimalware da Microsoft para serviços de nuvem e máquinas virtuais do Azure](../security/fundamentals/antimalware.md)

- [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretrizes**: implemente uma solução de Dr (recuperação de desastre) em vigor para que você possa proteger os dados, restaurar rapidamente os recursos que dão suporte a funções comerciais críticas e manter as operações em execução para manter a continuidade dos negócios (BC).

Essa estratégia de recuperação de desastres concentra-se na configuração de seu aplicativo lógico primário para fazer failover em um aplicativo lógico de backup ou em espera em um local alternativo em que os aplicativos lógicos do Azure também estão disponíveis. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário poderá assumir o trabalho. Essa estratégia requer que seu aplicativo lógico secundário e os recursos dependentes já estejam implantados e prontos no local alternativo.

Além disso, você deve expandir a definição de fluxo de trabalho subjacente do aplicativo lógico para um modelo de Azure Resource Manager. Este modelo define a infraestrutura, os recursos, os parâmetros e outras informações para provisionar e implantar seu aplicativo lógico.

- [Saiba mais sobre continuidade de negócios e recuperação de desastre para aplicativos lógicos do Azure](business-continuity-disaster-recovery-guidance.md)

- [Como automatizar a implantação para aplicativos lógicos do Azure usando modelos de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: implemente uma solução de Dr (recuperação de desastre) em vigor para que você possa proteger os dados, restaurar rapidamente os recursos que dão suporte a funções comerciais críticas e manter as operações em execução para manter a continuidade dos negócios (BC).

Essa estratégia de recuperação de desastres concentra-se na configuração de seu aplicativo lógico primário para fazer failover em um aplicativo lógico de backup ou em espera em um local alternativo em que os aplicativos lógicos do Azure também estão disponíveis. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário poderá assumir o trabalho. Essa estratégia requer que seu aplicativo lógico secundário e os recursos dependentes já estejam implantados e prontos no local alternativo.

Além disso, você deve expandir a definição de fluxo de trabalho subjacente do aplicativo lógico para um modelo de Azure Resource Manager. Este modelo define a infraestrutura, os recursos, os parâmetros e outras informações para provisionar e implantar seu aplicativo lógico.

Cada ponto de extremidade de solicitação em um aplicativo lógico tem uma assinatura de acesso compartilhado (SAS) na URL do ponto de extremidade. Se você estiver usando Azure Key Vault para armazenar seus segredos, garanta backups automatizados regulares de suas chaves e URLs.

- [Saiba mais sobre continuidade de negócios e recuperação de desastre para aplicativos lógicos do Azure](business-continuity-disaster-recovery-guidance.md)

- [Automatizar a implantação para aplicativos lógicos do Azure usando modelos de Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

- [Proteger o acesso e os dados nos aplicativos lógicos do Azure usando SAS](logic-apps-securing-a-logic-app.md#sas)

- [Como fazer backup de chaves de Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: sua estratégia de recuperação de desastres deve se concentrar na configuração de seu aplicativo lógico primário para fazer failover em um aplicativo lógico em espera ou de backup em um local alternativo onde os aplicativos lógicos do Azure também estão disponíveis. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário poderá assumir o trabalho. Essa estratégia requer que seu aplicativo lógico secundário e os recursos dependentes já estejam implantados e prontos no local alternativo.

Teste a restauração de chaves de backup gerenciadas pelo cliente. Observe que isso se aplica a aplicativos lógicos executados somente em ambientes de serviço de integração (ISE).

- [Saiba mais sobre continuidade de negócios e recuperação de desastre para aplicativos lógicos do Azure](business-continuity-disaster-recovery-guidance.md)

- [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure](customer-managed-keys-integration-service-environment.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: sua estratégia de recuperação de desastres deve se concentrar na configuração de seu aplicativo lógico primário para fazer failover em um aplicativo lógico em espera ou de backup em um local alternativo onde os aplicativos lógicos do Azure também estão disponíveis. Dessa forma, se o primário sofrer perdas, interrupções ou falhas, o secundário poderá assumir o trabalho. Essa estratégia requer que seu aplicativo lógico secundário e os recursos dependentes já estejam implantados e prontos no local alternativo. 

Proteja as chaves gerenciadas pelo cliente com backup. Observe que isso se aplica a aplicativos lógicos executados somente em ambientes de serviço de integração (ISE).

Habilite a proteção de Soft-Delete e limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Saiba mais sobre continuidade de negócios e recuperação de desastre para aplicativos lógicos do Azure](business-continuity-disaster-recovery-guidance.md)

- [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes de serviço de integração (ISEs) em aplicativos lógicos do Azure](customer-managed-keys-integration-service-environment.md)

- [Como habilitar a proteção de Soft-Delete e limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta. 

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais.  É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)