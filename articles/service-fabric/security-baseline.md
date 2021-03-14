---
title: Linha de base de segurança do Azure para Service Fabric
description: A linha de base de segurança Service Fabric fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b2d86083077b95312415099f64a13509ed60091b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582642"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Linha de base de segurança do Azure para Service Fabric

A linha de base de segurança do Azure para Service Fabric contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).



## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: Verifique se todas as implantações de sub-rede da rede virtual têm um grupo de segurança de rede aplicado com controles de acesso à rede específicos para as portas e fontes confiáveis do seu aplicativo.

* [Implantar Firewall do Azure usando um modelo](../firewall/deploy-template.md)

* [Criar redes de perímetro usando NSGs (grupos de segurança de rede) do Azure](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [Como integrar seu cluster de Service Fabric do Azure a uma rede virtual existente](./service-fabric-patterns-networking.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e corrija as recomendações de proteção de rede para a rede virtual, a sub-rede e o grupo de segurança de rede que estão sendo usados para proteger o Cluster Service Fabric do Azure. Habilite logs de fluxo do NSG (grupo de segurança de rede) e envie logs para uma conta de armazenamento do Azure para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho do Azure Log Analytics e usar o Análise de Tráfego do Azure para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens do Azure Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar incorretas configurações de rede.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar e usar o Azure Análise de Tráfego](../network-watcher/traffic-analytics.md)

* [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: forneça um gateway de front-end para fornecer um único ponto de entrada para usuários, dispositivos ou outros aplicativos. O gerenciamento de API do Azure integra-se diretamente com o Service Fabric, permitindo que você proteja o acesso aos serviços de back-end, Evite ataques de DOS usando a limitação e verifique as chaves de API, tokens JWT, certificados e outras credenciais.

Considere implantar o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics.

* [Service Fabric com visão geral do Gerenciamento de API do Azure](./service-fabric-api-management-overview.md)

* [Integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: para proteger contra ataques de DDoS, habilite a proteção padrão do DDoS do Azure na rede virtual em que o cluster do Azure Service Fabric está implantado. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

* [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretrizes**: habilitar os logs de fluxo do NSG (grupo de segurança de rede) para o NSG anexado à sub-rede que está sendo usada para proteger o cluster de Service Fabric do Azure. Registre os logs de fluxo do NSG em uma conta de armazenamento do Azure para gerar registros de fluxo. Se necessário para investigar a atividade anômala, habilite a captura de pacotes do observador de rede do Azure.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

* [Usar a análise de tráfego para visualizar os logs de fluxo do NSG](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo. Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: implante o Gateway de aplicativo Azure para aplicativos Web com HTTPS/SSL habilitado para certificados confiáveis.

* [Como implantar o gateway de aplicativo](../application-gateway/quick-create-portal.md)

* [Como configurar o gateway de aplicativo para usar HTTPS](../application-gateway/create-ssl-portal.md)

* [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../application-gateway/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em NSG (grupos de segurança de rede) que são anexados à sub-rede em que o cluster do Azure Service Fabric está implantado. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

* [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

* [Práticas recomendadas de rede Service Fabric](./service-fabric-best-practices-networking.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para recursos de rede relacionados ao cluster de Service Fabric do Azure. Use aliases de Azure Policy nos namespaces "Microsoft. onfabric" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cluster de Service Fabric do Azure.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

* [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para NSGs (grupo de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados ao cluster de Service Fabric do Azure. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI (interface de linha de comando) do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados às implantações de Service Fabric do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para componentes de Cluster Service Fabric do Azure, você pode atualizar a sincronização de horário para suas implantações de computação.

* [Como configurar a sincronização de horário para recursos de computação do Azure](../virtual-machines/windows/time-sync.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: você pode integrar seu cluster de Service Fabric do Azure para Azure monitor para agregar dados de segurança gerados pelo cluster. Consulte exemplos de problemas de diagnóstico e soluções com Service Fabric.

* [Configurar a integração de logs de Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs de Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Diagnosticando cenários comuns de Service Fabric](./service-fabric-diagnostics-common-scenarios.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite Azure monitor para o Cluster Service Fabric, direcione-o para um espaço de trabalho log Analytics. Isso registrará em log as informações relevantes do cluster e as métricas do sistema operacional para todos os nós do Cluster Service Fabric do Azure.

* [Configurar a integração de logs de Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs de Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente de Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics pesquisas de log](../azure-monitor/logs/log-query-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretrizes**: integrar o cluster de Service Fabric do Azure ao Azure monitor. Verifique se o espaço de trabalho Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

* [Configurar a integração de logs de Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs de Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente de Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Como configurar Log Analytics período de retenção do espaço de trabalho](../azure-monitor/logs/manage-cost-storage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: integrar o cluster de Service Fabric do Azure ao Azure monitor. Verifique se o espaço de trabalho Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

* [Configurar a integração de logs de Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs de Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente de Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Como configurar Log Analytics período de retenção do espaço de trabalho](../azure-monitor/logs/manage-cost-storage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: use consultas de espaço de trabalho do Azure log Analytics para consultar logs de Service Fabric do Azure.

* [Log Analytics pesquisas de log](../azure-monitor/logs/log-query-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use o espaço de trabalho do Azure log Analytics para monitorar e alertar sobre atividades anormais em logs de segurança e eventos relacionados ao cluster de Service Fabric do Azure.

* [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: por padrão, o Windows Defender é instalado no windows Server 2016. Consulte a documentação do antifêmea para regras de configuração se você não estiver usando o Windows Defender. Não há suporte para o Windows Defender no Linux.

* [Para obter detalhes, consulte Windows Defender antivírus no Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros para registro em log DNS.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Orientação**: configurar manualmente o registro em log do console em uma base por nó.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Orientação**: manter o registro da conta administrativa local que é criada durante o provisionamento de cluster do cluster de Service Fabric do Azure, bem como quaisquer outras contas que você criar. Além disso, se a integração do Azure AD for usada, o Azure AD terá funções internas que devem ser explicitamente atribuídas e, portanto, podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: ao provisionar um cluster, o Azure exige que você crie novas senhas para o portal da Web. Não há nenhuma senha padrão a ser alterada; no entanto, você pode especificar senhas diferentes para acesso ao portal da Web.

* [Criar no portal do Azure](./service-fabric-cluster-creation-via-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: integre a autenticação do Azure Service Fabric com o Azure Active Directory. Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas.

Além disso, você pode usar as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Configurar a autenticação de cliente do Azure Active Directory](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: sempre que possível, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

* [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: Habilite a MFA do Azure AD e siga as recomendações de gerenciamento de acesso e identidade da Central de Segurança do Azure.

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Orientação**: Use PAWs (estações de trabalho com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar seus clusters de Service Fabric do Azure e recursos relacionados.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretriz**: Use o Privileged Identity Management (PIM) do Azure Active Directory (AD) para geração de logs e alertas quando atividades suspeitas ou inseguras ocorrem no ambiente. Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

* [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

* [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o AAD (Azure Active Directory) como o sistema de autenticação e autorização central para proteger o acesso a pontos de extremidade de gerenciamento de clusters do Azure Service Fabric. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também Salts, hashes e armazena com segurança as credenciais do usuário.

* [Como criar e configurar uma instância do AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Azure Active Directory de instalação para autenticação de cliente do Service Fabric](./service-fabric-cluster-creation-setup-aad.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Orientação**: Use a autenticação do Azure Active Directory (AAD) com o cluster de Service Fabric do Azure. O AAD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo.

* [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: alerta sobre o desvio do comportamento de logon da conta

**Orientação**: Use Azure Active Directory (AAD) de entrada e logs de auditoria para monitorar tentativas de acesso a contas desativadas; esses logs podem ser integrados a qualquer ferramenta de monitoramento/SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do AAD, enviando os logs de auditoria e os logs de entrada para um espaço de trabalho Log Analytics do Azure. Configure os alertas desejados no espaço de trabalho Log Analytics do Azure.

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de identidade e proteção contra riscos do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

* [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível; Sistema de Proteção de Dados do Cliente ainda não tem suporte para o Service Fabric do Azure.

* [Lista de serviços com suporte Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Orientação**: use marcas em recursos relacionados às implantações de Cluster Service Fabric do Azure para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por VNet/sub-rede, marcados adequadamente e protegidos por um NSG ou um firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser suficientemente isolados. Para máquinas virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desligá-los quando não estiverem em uso.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alerta ou alerta e negar com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: implante uma ferramenta automatizada em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia tais transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de controles de proteção de dados robustos e

técnicas.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografar todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure são capazes de negociar o TLS 1,2 ou superior.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

* [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Cenários de segurança do cluster do Service Fabric](./service-fabric-cluster-security.md)

* [Guia de solução de problemas Service Fabric para configuração de TLS](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: para clusters de Service Fabric do Azure que armazenam ou processam informações confidenciais, marque o cluster e os recursos relacionados como confidenciais usando marcas. Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: Use a criptografia em repouso em todos os recursos do Azure. A Microsoft recomenda permitir que o Azure gerencie suas chaves de criptografia, no entanto, há a opção de gerenciar suas próprias chaves em algumas instâncias.

* [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

* [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

* [Habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure no Windows](./service-fabric-enable-azure-disk-encryption-windows.md)

* [Habilitar a criptografia de disco para nós de Cluster Service Fabric do Azure no Linux](./service-fabric-enable-azure-disk-encryption-linux.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para os recursos críticos do Azure.

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: execute regularmente o Service Fabric serviço de análise de falha e os serviços de caos para simular falhas em todo o cluster para avaliar a robustez e a confiabilidade de seus serviços.

Siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em suas máquinas virtuais do Azure e imagens de contêiner.

Use uma solução de terceiros para executar avaliações de vulnerabilidade em dispositivos de rede e aplicativos Web. Ao realizar verificações remotas, não use uma única conta administrativa permanente. Considere a implementação da metodologia de provisionamento JIT para a conta de verificação. As credenciais para a conta de verificação devem ser protegidas, monitoradas e usadas somente para verificação de vulnerabilidade.

* [Introdução ao Service Fabric serviço de análise de falha](./service-fabric-testability-overview.md)

* [Induzir caos controlado em clusters de Service Fabric](./service-fabric-controlled-chaos.md)

* [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: habilitar atualizações automáticas de imagem do sistema operacional nos conjuntos de dimensionamento de máquinas virtuais do cluster do Azure Service Fabric.

Como alternativa, para testar patches do sistema operacional antes de passar para a produção, use o gatilho manual para atualizações de imagem do so do seu conjunto de dimensionamento. Observe que a opção de gatilho manual não fornece reversão interna. Monitore OS patches do sistema operacional usando o Gerenciamento de Atualizações da automação do Azure.

* [Gerenciamento de patches para nós de Cluster Service Fabric](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Atualizações automáticas de imagem do sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Como atualizar as VMs com o modelo mais recente do conjunto de dimensionamento](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Visão geral do Gerenciamento de Atualizações da Automação do Azure](../automation/update-management/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Orientação**: habilitar atualizações automáticas de imagem do sistema operacional nos conjuntos de dimensionamento de máquinas virtuais do cluster do Azure Service Fabric. O POA (aplicativo de orquestração de patch) é uma solução alternativa destinada a clusters Service Fabric hospedados fora do Azure. POA pode ser usado com clusters do Azure, com alguma sobrecarga de hospedagem adicional.

* [Gerenciamento de patches para nós de Cluster Service Fabric](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Atualizações automáticas de imagem do sistema operacional com conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Como configurar o agendamento de aplicação de patch do sistema operacional para clusters Service Fabric](./service-fabric-patch-orchestration-application.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: exporte os resultados da verificação em intervalos consistentes e compare os resultados para verificar se as vulnerabilidades foram corrigidas. Ao usar as recomendações de gerenciamento de vulnerabilidade sugeridas pela central de segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricas.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

* [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

* [Como criar e usar Marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: implemente uma solução de terceiros para monitorar nós de cluster para aplicativos de software não aprovados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas e protocolos, etc.), incluindo clusters de Service Fabric do Azure, em suas assinaturas. Remova os recursos do Azure não aprovados que você descobrir. Para nós de Cluster Service Fabric do Azure, implemente uma solução de terceiros para remover ou alertar sobre software não aprovado.

* [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: para nós de Cluster Service Fabric do Azure, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para restringir quais serviços você pode provisionar em seu ambiente.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretrizes**: para nós de Cluster Service Fabric do Azure, implemente uma solução de terceiros para impedir a execução de tipos de arquivo não autorizados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

* [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Orientação**: Use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.

* [Por exemplo, como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Orientação**: software necessário para operações de negócios, mas pode incorrer em um risco maior para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e é suficientemente protegido com um firewall do Azure ou grupo de segurança de rede.

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. onfabric" para criar políticas personalizadas para auditar ou impor a configuração de rede do Cluster Service Fabric.

* [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretrizes**: as imagens do sistema operacional Service Fabric do Azure são gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operacional de nós de cluster.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: Use Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras para seus clusters de Service Fabric do Azure e recursos relacionados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: imagens do sistema operacional de Cluster Service Fabric do Azure gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação da configuração de estado no nível do sistema operacional.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se estiver usando definições de Azure Policy personalizadas, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Orientação**: se estiver usando imagens personalizadas, use o Azure RBAC (controle de acesso baseado em função) para garantir que somente usuários autorizados possam acessar as imagens. Para imagens de contêiner, armazene-as no registro de contêiner do Azure e aproveite o RBAC do Azure para garantir que somente usuários autorizados possam acessar as imagens.

* [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Entender o RBAC do Azure para registro de contêiner](../container-registry/container-registry-roles.md)

* [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. onfabric" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação IaaS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. onfabric" para criar políticas personalizadas para auditar ou impor a configuração de seu cluster de Service Fabric.

* [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para configurações do sistema operacional e do Docker para contêineres.

* [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidade de serviço gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

* [Usando identidades gerenciadas para o Azure com Service Fabric](./concepts-managed-identity.md)

* [Configurar o suporte de identidade gerenciada para um novo cluster Service Fabric](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [Usar identidade gerenciada com um aplicativo Service Fabric](./how-to-managed-identity-service-fabric-app-code.md)

* [Suporte do KeyVaultReference para aplicativos Service Fabric](./service-fabric-keyvault-references.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: identidades gerenciadas podem ser usadas em clusters de Service Fabric implantados pelo Azure e para aplicativos implantados como recursos do Azure. As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

* [Usando identidades gerenciadas para o Azure com Service Fabric](./concepts-managed-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Orientação**: se estiver usando qualquer código relacionado à implantação de Service Fabric do Azure, você poderá implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Use Azure Key Vault para girar Service Fabric certificados de cluster automaticamente.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Gerenciamento de certificados em clusters Service Fabric](./cluster-security-certificate-management.md#certificate-rotation)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usar software antimalware gerenciado centralmente

**Orientação**: por padrão, o Windows Defender antivírus está instalado no windows Server 2016. A interface do usuário é instalada por padrão em alguns SKUs, mas não é necessária.

Confira a documentação sobre antimalware para obter as regras de configuração, caso não esteja usando o Windows Defender. O Windows Defender não tem suporte no Linux.

* [Entender o Windows Defender antivírus no Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Service Fabric), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Service Fabric), no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: o serviço de backup e restauração no Service Fabric permite um backup fácil e automático de informações armazenadas em serviços com estado. Fazer backup de dados de aplicativo em uma base periódica é fundamental para proteger contra a indisponibilidade de serviço e perda de dados. O Service Fabric fornece um serviço opcional de backup e restauração, que permite que você configure o backup periódico dos Reliable Services com estado (inclusive Serviços de Ator) sem a necessidade de escrever código adicional. Ele também facilita a restauração dos backup obtidos anteriormente.

* [Backup e restauração periódicos em um cluster de Service Fabric do Azure](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: habilite o serviço de restauração de backup no Cluster Service Fabric e crie políticas de backup para fazer backup dos serviços com estado periodicamente e sob demanda. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

* [Backup e restauração periódicos em um cluster de Service Fabric do Azure](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [Noções básicas sobre a configuração de backup periódico no Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar a restauração do serviço de restauração de backup revisando periodicamente as informações de configuração de backup e os backups disponíveis. Teste a restauração de chaves de backup gerenciadas pelo cliente.

* [Noções básicas sobre a configuração de backup periódico no Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Backup de restauração no Azure Service Fabric](./service-fabric-backup-restore-service-trigger-restore.md)

* [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: backups de Service Fabric serviço de restauração de backup usam uma conta de armazenamento do Azure em sua assinatura. O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados de armazenamento.

Se você estiver usando chaves gerenciadas pelo cliente, verifique se Soft-Delete no Key Vault está habilitada para proteger as chaves contra exclusão acidental ou mal-intencionada.

* [Criptografia do Armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

* [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu.

* [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

* [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Guia NIST para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

* [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

* [Como configurar a exportação contínua](../security-center/continuous-export.md)

* [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

* [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para saber mais, confira [Controle de segurança: Testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

* [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)