---
title: Linha de base de segurança do Azure para Service Fabric
description: A linha de base de segurança do Service Fabric oferece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b2d86083077b95312415099f64a13509ed60091b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582642"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Linha de base de segurança do Azure para Service Fabric

A linha de base de segurança do Azure para o Service Fabric contém recomendações que ajudarão a aprimorar a postura de segurança de sua implantação.

A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas.

Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).



## <a name="network-security"></a>Segurança de rede

*Para saber mais, confira [Controle de segurança: Segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: certifique-se de que todas as implantações de sub-rede de Rede Virtual tenham um grupo de segurança de rede aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo.

* [Implantar Firewall do Azure usando um modelo](../firewall/deploy-template.md)

* [Criar redes de perímetro usando os NSGs (Grupos de Segurança de Rede) do Azure](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [Como integrar seu cluster do Azure Service Fabric a uma rede virtual existente](./service-fabric-patterns-networking.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: monitorar e registrar em log a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: use a Central de Segurança do Azure e corrija as recomendações de proteção de rede para a rede virtual, a sub-rede e o grupo de segurança de rede que estão sendo usados para proteger o cluster do Azure Service Fabric. Habilite logs de fluxo de NSG e envie os logs para uma conta de Armazenamento do Azure para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Azure Log Analytics e use a Análise de Tráfego do Azure para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego do Azure são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e localizar com precisão configurações incorretas de rede.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar e usar a Análise de Tráfego do Azure](../network-watcher/traffic-analytics.md)

* [Noções básicas sobre a segurança de rede fornecida pela Central de Segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: forneça um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. O Gerenciamento de API do Azure integra-se diretamente com o Service Fabric, permitindo que você proteja o acesso aos serviços de back-end, evite ataques de DOS usando a limitação e verifique as chaves de API, tokens JWT, certificados e outras credenciais.

Considere implantar o WAF (Firewall de Aplicativo Web) do Azure na frente de aplicativos Web críticos para que haja uma inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingestão de logs em uma Conta de Armazenamento, Hub de Eventos ou workspace do Log Analytics.

* [Service Fabric com visão geral do Gerenciamento de API do Azure](./service-fabric-api-management-overview.md)

* [Integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [Como implantar o WAF do Azure](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: para proteção contra DDoS, habilite a proteção padrão do DDoS do Azure na rede virtual em que o cluster do Azure Service Fabric está implantado. Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

* [Como configurar a Proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

* [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: habilitar os logs de fluxo do NSG (grupo de segurança de rede) para o NSG anexado à sub-rede que está sendo usada para proteger o cluster do Azure Service Fabric. Registre os logs de fluxo do NSG em uma conta de Armazenamento do Azure para gerar registros de fluxo. Se houver necessidade de investigar atividades anômalas, habilite a captura de pacotes do Observador de Rede do Azure.

* [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

* [Usar a análise de tráfego para visualizar os logs de fluxo do NSG](../network-watcher/traffic-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com funcionalidades de inspeção de conteúdo. Se a detecção de intrusão e/ou prevenção baseada na inspeção de conteúdo não for um requisito, o Firewall do Azure com a inteligência contra ameaças poderá ser usado. A filtragem baseada em inteligência contra ameaças do Firewall do Azure pode alertar e rejeitar o tráfego de e para endereços IP e domínios conhecidos mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou negar tráfego mal-intencionado.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: implante o Gateway de Aplicativo do Azure para aplicativos Web com HTTPS/SSL habilitado para certificados confiáveis.

* [Como implantar o Gateway de Aplicativo](../application-gateway/quick-create-portal.md)

* [Como configurar o Gateway de Aplicativo para usar HTTPS](../application-gateway/create-ssl-portal.md)

* [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](../application-gateway/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede nos NSGs que estão anexados à sub-rede em que o cluster do Azure Service Fabric está implantado. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

* [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

* [Melhores práticas de rede do Service Fabric](./service-fabric-best-practices-networking.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para os recursos de rede relacionados ao cluster do Azure Service Fabric. Use aliases do Azure Policy nos namespaces "Microsoft.ServiceFabric" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do cluster do Azure Service Fabric.

Você também pode usar o Azure Blueprints para simplificar implantações do Azure em grande escala por meio do empacotamento de artefatos de ambiente importantes, como modelos do Azure Resource Manager, controles RBAC do Azure e políticas em uma definição de blueprint. Aplique facilmente o blueprint a novas assinaturas e ambientes e ajuste o controle e o gerenciamento por meio do controle de versão.

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: use Marcas para NSGs (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego associados ao cluster do Azure Service Fabric. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições integradas do Azure Policy relacionadas à marcação, como “Exigir marca e seu valor”, para que todos os recursos sejam criados com marcas e para gerar notificações quando há recursos não marcados.

Use o Azure PowerShell ou a CLI (interface de linha de comando) do Azure para pesquisar ou executar ações em recursos com base nas respectivas marcas.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o Log de Atividades do Azure para monitorar configurações de recurso de rede e detectar alterações de recursos de rede relacionadas às implantações do Azure Service Fabric. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

* [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

* [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para saber mais, confira [Controle de segurança: Registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém fontes de tempo para componentes de cluster do Azure Service Fabric. Você pode atualizar a sincronização de data/hora para suas implantações de computação.

* [Como configurar a sincronização de data/hora para recursos de computação do Azure](../virtual-machines/windows/time-sync.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: você pode integrar o cluster do Azure Service Fabric para o Azure Monitor para agregar dados de segurança gerados pelo cluster. Confira os problemas de diagnóstico de exemplo e as soluções com o Service Fabric.

* [Configurar a integração de logs do Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs do Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Diagnóstico de cenários comuns do Service Fabric](./service-fabric-diagnostics-common-scenarios.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilite o Azure Monitor para o cluster do Service Fabric, direcione-o para um workspace do Log Analytics. Isso registrará em log as informações relevantes do cluster e as métricas do SO para todos os nós de cluster do Azure Service Fabric.

* [Configurar a integração de logs do Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs do Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente do Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Pesquisas de log do Log Analytics](../azure-monitor/logs/log-query-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Coletar logs de segurança de sistemas operacionais

**Diretrizes**: integrar o cluster do Azure Service Fabric ao Azure Monitor. Verifique se o workspace do Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

* [Configurar a integração de logs do Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs do Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente do Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Como configurar o período de retenção no workspace do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: integrar o cluster do Azure Service Fabric ao Azure Monitor. Verifique se o workspace do Log Analytics usado tem o período de retenção de log definido de acordo com os regulamentos de conformidade da sua organização.

* [Configurar a integração de logs do Azure Monitor com o Service Fabric](./service-fabric-diagnostics-oms-setup.md)

* [Configurar logs do Azure Monitor para monitorar contêineres no Azure Service Fabric](./service-fabric-tutorial-monitoring-wincontainers.md)

* [Como implantar o agente do Log Analytics em seus nós](./service-fabric-diagnostics-oms-agent.md)

* [Como configurar o período de retenção no workspace do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitorar e examinar os logs

**Diretrizes**: use consultas do workspace do Azure Log Analytics para consultar os logs do Azure Service Fabric.

* [Pesquisas de log do Log Analytics](../azure-monitor/logs/log-query-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: use o workspace do Azure Log Analytics para monitorar e alertar sobre atividades anômalas em logs de segurança e eventos relacionados ao cluster do Azure Service Fabric.

* [Como gerenciar alertas na Central de Segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

* [Como alertar sobre dados de log do Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: por padrão, o Windows Defender está instalado no Windows Server 2016. Confira a documentação sobre antimalware para obter as regras de configuração, caso não esteja usando o Windows Defender. O Windows Defender não tem suporte no Linux.

* [Para obter detalhes, confira o artigo Windows Defender Antivírus no Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: implemente uma solução de terceiros para registro em log DNS.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretrizes**: configurar manualmente o registro em log do console em uma base por nó.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para saber mais, confira [Controle de segurança: Identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: mantenha o registro da conta administrativa local que é criada durante o provisionamento de cluster do cluster do Azure Service Fabric, bem como de outras contas que você criar. Além disso, se a integração do Azure AD for usada, o Azure AD terá funções internas que devem ser explicitamente atribuídas e, portanto, podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc e descobrir contas que sejam membros de grupos administrativos.

Além disso, use as recomendações de Gerenciamento de Acesso e Identidade da Central de Segurança do Azure.

* [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Como monitorar a identidade e o acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: ao provisionar um cluster, o Azure exige que você crie senhas para o portal da Web. Não há nenhuma senha padrão a ser alterada. No entanto, você pode especificar senhas diferentes para acesso ao portal da Web.

* [Criar no portal do Azure](./service-fabric-cluster-creation-via-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: integre a autenticação do Azure Service Fabric com o Azure Active Directory. Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas.

Além disso, use as recomendações de Gerenciamento de Acesso e Identidade da Central de Segurança do Azure.

* [Configurar a autenticação de cliente do Azure Active Directory](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [Como monitorar a identidade e o acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: sempre que possível, use o SSO do Azure Active Directory em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança do Azure.

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

**Diretrizes**: use PAWs (workspaces com acesso privilegiado) com a MFA (autenticação multifator) configurada para fazer logon e configurar os clusters do Azure Service Fabric e recursos relacionados.

* [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

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

**Diretrizes**: use o AAD (Azure Active Directory) como o sistema de autenticação e autorização central para proteger o acesso a pontos de extremidade de gerenciamento de clusters do Azure Service Fabric. O AAD protege os dados usando criptografia forte para dados em repouso e em trânsito. O AAD também inclui sais, hashes e armazena com segurança as credenciais do usuário.

* [Como criar e configurar uma instância do AAD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Configurar o Azure Active Directory para autenticação de cliente no Service Fabric](./service-fabric-cluster-creation-setup-aad.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: use a autenticação do AAD (Azure Active Directory) com o cluster do Azure Service Fabric. O AAD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência associações a um grupo, acesso aos aplicativos empresariais e atribuições de função. O acesso do usuário pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

* [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: use os logs de auditoria e entrada do AAD (Azure Active Directory) para monitorar tentativas de acesso a contas desativadas. Esses logs podem ser integrados a qualquer ferramenta de monitoramento/SIEM de terceiros.

Para simplificar esse processo, crie configurações de diagnóstico para contas de usuário do AAD e envie os logs de auditoria e os logs de entrada para um workspace do Azure Log Analytics. Configure os alertas desejados no workspace do Azure Log Analytics.

* [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Alertar sobre desvios de comportamento de entrada na conta

**Diretrizes**: Use os recursos de proteção de identidade e proteção contra riscos do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuários. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

* [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

* [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretrizes**: não disponível. O Sistema de Proteção de Dados do Cliente ainda não tem suporte para o Azure Service Fabric.

* [Lista de serviços com suporte do Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para saber mais, confira [Controle de segurança: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas em recursos relacionados a implantações de cluster do Azure Service Fabric para auxiliar no acompanhamento de recursos do Azure que armazenam ou processam informações confidenciais.

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os recursos devem ser separados por VNet/Sub-rede, marcados adequadamente e protegidos em um NSG ou Firewall do Azure. Os recursos que armazenam ou processam dados confidenciais devem ser suficientemente isolados. Para Máquinas Virtuais que armazenam ou processam dados confidenciais, implemente políticas e procedimentos para desligá-las quando não estiverem em uso.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

* [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

* [Como configurar alerta ou alertar e negar com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: implante uma ferramenta automatizada em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências alertando os profissionais de segurança da informação.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles

robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretrizes**: criptografe todas as informações confidenciais em trânsito. Verifique se todos os clientes que se conectam aos recursos do Azure são capazes de negociar o TLS 1.2 ou superior.

Siga as recomendações da Central de Segurança do Azure para criptografia em repouso e em trânsito, quando aplicável.

* [Entenda a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Cenários de segurança do cluster do Service Fabric](./service-fabric-cluster-security.md)

* [Guia de solução de problemas do Service Fabric para configuração de TLS](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis para os recursos de computação nem para o Armazenamento do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: para clusters do Azure Service Fabric que armazenam ou processam informações confidenciais, marque o cluster e os recursos relacionados como confidenciais usando marcas. Os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis para os recursos de computação nem para o Armazenamento do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

* [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: use a criptografia em repouso em todos os recursos do Azure. A Microsoft recomenda permitir que o Azure gerencie as chaves de criptografia. No entanto, há a opção de gerenciar as próprias chaves em algumas instâncias.

* [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

* [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

* [Habilitar a criptografia de disco para nós de cluster do Azure Service Fabric no Windows](./service-fabric-enable-azure-disk-encryption-windows.md)

* [Habilitar a criptografia de disco para nós de cluster do Azure Service Fabric no Linux](./service-fabric-enable-azure-disk-encryption-linux.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: use o Azure Monitor com o Log de Atividades do Azure para criar alertas para quando ocorrer alterações em recursos críticos do Azure.

* [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para saber mais, confira [Controle de segurança: Gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: execute regularmente o serviço de análise de falhas e os serviços de caos do Service Fabric para simular falhas em todo o cluster de modo a avaliar a robustez e a confiabilidade de seus serviços.

Siga as recomendações da Central de Segurança do Azure sobre como executar avaliações de vulnerabilidade em suas máquinas virtuais e imagens de contêiner do Azure.

Use uma solução de terceiros para executar avaliações de vulnerabilidade em dispositivos de rede e aplicativos Web. Ao realizar verificações remotas, não use uma conta administrativa perpétua individual. Considere a implementação da metodologia de provisionamento JIT para a conta de verificação. As credenciais para a conta de verificação devem ser protegidas, monitoradas e usadas somente para verificação de vulnerabilidade.

* [Introdução ao Serviço de Análise de Falhas do Service Fabric](./service-fabric-testability-overview.md)

* [Induzir caos controlado em clusters do Service Fabric](./service-fabric-controlled-chaos.md)

* [Como implementar as recomendações de avaliação de vulnerabilidade da Central de Segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: habilite atualizações automáticas de imagem do SO nos conjuntos de dimensionamento de máquinas virtuais do cluster do Azure Service Fabric.

Como alternativa, para testar patches do SO antes de passar para a produção, use o gatilho manual para atualizações de imagem do SO do seu conjunto de dimensionamento. Observe que a opção de gatilho manual não fornece reversão interna. Monitore patches do SO usando o Gerenciamento de Atualizações da Automação do Azure.

* [Gerenciamento de patches para nós de cluster do Service Fabric](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Atualizações automáticas de imagem de SO com conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Como atualizar as VMs com o modelo mais recente do conjunto de dimensionamento](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Visão geral do Gerenciamento de Atualizações da Automação do Azure](../automation/update-management/overview.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implantar uma solução automatizada de gerenciamento de patch para nomes de software de terceiros

**Diretrizes**: habilite atualizações automáticas de imagem do SO nos conjuntos de dimensionamento de máquinas virtuais do cluster do Azure Service Fabric. O POA (aplicativo de orquestração de patch) é uma solução alternativa destinada a clusters do Service Fabric hospedados fora do Azure. O POA pode ser usado com clusters do Azure, com alguma sobrecarga de hospedagem adicional.

* [Gerenciamento de patches para nós de cluster do Service Fabric](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Atualizações automáticas de imagem de SO com conjuntos de dimensionamento de máquinas virtuais do Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [Como configurar a agenda de aplicação de patch no SO para clusters do Service Fabric](./service-fabric-patch-orchestration-application.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: exporte os resultados da verificação em intervalos consistentes e compare-os para verificar se as vulnerabilidades foram corrigidas. Ao usar as recomendações de gerenciamento de vulnerabilidade sugeridas pela Central de Segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricos.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use um programa de pontuação de risco comum (por exemplo, o Sistema de pontuação de vulnerabilidade comum) ou as classificações de risco padrão fornecidas pela ferramenta de verificação de terceiros.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para saber mais, confira [Controle de segurança: Inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

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

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

* [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

* [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

* [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

Tipos de recursos não permitidos

Tipos de recursos permitidos

Use o Azure Resource Graph para consultar/descobrir recursos nas suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: implemente uma solução de terceiros para monitorar nós de cluster para aplicativos de software não aprovados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.), incluindo clusters do Azure Service Fabric em suas assinaturas. Remova os recursos do Azure não aprovados que você descobrir. Para nós de cluster do Azure Service Fabric, implemente uma solução de terceiros para remover ou alertar sobre software não aprovado.

* [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: para nós de cluster do Azure Service Fabric, implemente uma solução de terceiros para impedir a execução de software não autorizado.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="69-use-only-approved-azure-services"></a>6.9: Usar somente serviços do Azure aprovados

**Diretrizes**: use o Azure Policy para restringir os serviços que são provisionados no ambiente.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/index.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de nomes de software aprovados

**Diretrizes**: para nós de cluster do Azure Service Fabric, implemente uma solução de terceiros para impedir a execução de tipos de arquivo não autorizados.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretrizes**: use o Acesso Condicional do Azure para configurar "Bloquear acesso" para o aplicativo "Gerenciamento do Microsoft Azure", limitando assim a interação dos usuários com o Azure Resource Manager.

* [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretrizes**: use configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.

* [Por exemplo, como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: software necessário para operações empresariais, mas pode incorrer em um risco maior para a organização, deve ser isolado em máquina virtual e/ou rede virtual própria e deve ser suficientemente protegido com um Firewall do Azure ou grupo de segurança de rede.

* [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

* [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para saber mais, confira [Controle de segurança: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.ServiceFabric" para criar políticas personalizadas para auditar ou impor a configuração de rede do cluster do Service Fabric.

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras de sistema operacional

**Diretrizes**: as imagens do sistema operacional do Azure Service Fabric são gerenciadas e mantidas pela Microsoft. Cliente responsável pela implementação de configurações seguras para o sistema operacional de nós de cluster.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras nos clusters do Azure Service Fabric e nos recursos relacionados.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras de sistema operacional

**Diretrizes**: as imagens do sistema operacional do cluster do Azure Service Fabric são gerenciadas e mantidas pela Microsoft. O cliente é responsável pela implementação da configuração de estado no nível do SO.

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se estiver usando as definições personalizadas do Azure Policy, use o Azure DevOps ou o Azure Repos para armazenar e gerenciar seu código com segurança.

* [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazene imagens personalizadas do sistema operacional com segurança

**Diretrizes**: se estiver usando imagens personalizadas, use o RBAC (controle de acesso baseado em função) do Azure para garantir que somente usuários autorizados possam acessar as imagens. Para imagens de contêiner, armazene-as no Registro de Contêiner do Azure e aproveite o RBAC do Azure para garantir que somente usuários autorizados possam acessar as imagens.

* [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Entender o RBAC do Azure para Registro de Contêiner](../container-registry/container-registry-roles.md)

* [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.ServiceFabric" para criar políticas personalizadas para alertas, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: não aplicável; esta diretriz destina-se a recursos de computação de IaaS.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.ServiceFabric" para criar políticas personalizadas para auditar ou impor a configuração do cluster do Service Fabric.

* [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: use a Central de Segurança do Azure para executar verificações de linha de base para configurações do SO e do Docker para contêineres.

* [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: use Identidade de Serviço Gerenciada em conjunto com o Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

* [Uso de identidades gerenciadas para o Azure com o Service Fabric](./concepts-managed-identity.md)

* [Configurar o suporte de identidade gerenciada para um novo cluster do Service Fabric](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [Usar identidade gerenciada com um aplicativo do Service Fabric](./how-to-managed-identity-service-fabric-app-code.md)

* [Suporte do KeyVaultReference para aplicativos do Service Fabric](./service-fabric-keyvault-references.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: identidades gerenciadas podem ser usadas em clusters do Service Fabric implantados pelo Azure e para aplicativos implantados como recursos do Azure. As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

* [Uso de identidades gerenciadas para o Azure com o Service Fabric](./concepts-managed-identity.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretrizes**: se estiver usando qualquer código relacionado à implantação do Azure Service Fabric, você poderá implementar o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Use o Azure Key Vault para girar os certificados de cluster do Service Fabric automaticamente.

* [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Gerenciamento de certificados nos clusters do Service Fabric](./cluster-security-certificate-management.md#certificate-rotation)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para saber mais, confira [Controle de segurança: Defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Usar software antimalware gerenciado centralmente

**Diretrizes**: por padrão, o Windows Defender Antivírus está instalado no Windows Server 2016. A interface do usuário é instalada por padrão em alguns SKUs, mas não é necessária.

Confira a documentação sobre antimalware para obter as regras de configuração, caso não esteja usando o Windows Defender. O Windows Defender não tem suporte no Linux.

* [Entender o Windows Defender Antivírus no Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Service Fabric), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados. O antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Service Fabric), mas não é executado no conteúdo do cliente.

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Não aplicável

## <a name="data-recovery"></a>Recuperação de dados

*Para saber mais, confira [Controle de segurança: Recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automatizados regulares

**Diretrizes**: o serviço de Backup e Restauração no Service Fabric possibilita backup fácil e automático das informações armazenadas em serviço com estado. Fazer backup de dados de aplicativo em uma base periódica é fundamental para proteger contra a indisponibilidade de serviço e perda de dados. O Service Fabric fornece um serviço opcional de backup e restauração, que permite que você configure o backup periódico dos Reliable Services com estado (inclusive Serviços de Ator) sem a necessidade de escrever código adicional. Ele também facilita a restauração dos backup obtidos anteriormente.

* [Backup e restauração periódicos do cluster do Azure Service Fabric](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: habilite o serviço de restauração de backup no cluster do Service Fabric e crie políticas de backup para fazer backup dos serviços com estado periodicamente e sob demanda. Chaves de backup gerenciadas pelo cliente no Azure Key Vault.

* [Backup e restauração periódicos do cluster do Azure Service Fabric](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [Noções básicas sobre a configuração de backup periódico no Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: garanta a capacidade de execução da restauração do serviço de restauração de backup revisando periodicamente as informações de configuração de backup e os backups disponíveis. Teste a restauração com os backups das chaves gerenciadas pelo cliente.

* [Noções básicas sobre a configuração de backup periódico no Azure Service Fabric](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [Backup de restauração no Azure Service Fabric](./service-fabric-backup-restore-service-trigger-restore.md)

* [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: os backups do serviço de restauração de backup do Service Fabric usam uma conta de Armazenamento do Azure em sua assinatura. O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter controle adicional sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados de armazenamento.

Se você estiver usando chaves gerenciadas pelo cliente, verifique se a exclusão reversível no Key Vault está habilitada para proteger as chaves contra exclusão acidental ou mal-intencionada.

* [Criptografia do Armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

* [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para saber mais, confira [Controle de segurança: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Diretrizes**: elabore um guia de resposta a incidentes para sua organização. Crie planos de resposta a incidentes por escrito, que definam todas as funções de pessoal e as fases de tratamento e gerenciamento de incidentes, desde a detecção até a revisão posterior.

* [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Use o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da Central de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a Central de Segurança do Azure atribui uma gravidade a cada alerta para ajudar a priorizar os alertas que deverão ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, aplique marcas nas assinaturas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

* [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

* [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

* [Guia NIST para programas de teste, treinamento e exercícios para planos de TI e funcionalidades](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de Participação do Teste de Penetração do Microsoft Cloud para verificar se os testes de penetração não estão violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

* [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da Central de Segurança do Azure**: Não disponível no momento

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)