---
title: Linha de base de segurança do Azure para o Hub IoT do Azure
description: A linha de base de segurança do Hub IoT do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2947faa8c7b56fdbe4cf549529b63d506b810750
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570852"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Linha de base de segurança do Azure para o Hub IoT do Azure

A linha de base de segurança do Azure para Microsoft Azure Hub IoT contém recomendações que o ajudarão a melhorar a postura de segurança de sua implantação. A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas. Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: por padrão, os nomes de host do Hub IOT são mapeados para um ponto de extremidade público com um endereço IP roteável publicamente pela Internet. Diferentes clientes compartilham esse ponto de extremidade público do Hub IoT e dispositivos IoT em redes de longa distância e redes locais podem acessá-lo.

Os recursos do Hub IoT, incluindo roteamento de mensagens, carregamento de arquivos e importação/exportação de dispositivos em massa também exigem conectividade do Hub IoT para um recurso do Azure de propriedade do cliente sobre seu ponto de extremidade público. Esses caminhos de conectividade compõem coletivamente o tráfego de saída do Hub IoT para os recursos do cliente.

Recomenda-se restringir a conectividade aos recursos do Azure (incluindo o Hub IoT do Azure) por meio de uma rede virtual que você possui e operar para reduzir a exposição de conectividade em uma rede isolada e habilitar a conectividade de rede local diretamente para a rede de backbone do Azure. Use o link privado do Azure e o ponto de extremidade privado do Azure, onde viável, para habilitar o acesso privado aos serviços de outras redes virtuais.

Mantenha as portas de hardware abertas em seus dispositivos para um mínimo para evitar o acesso indesejado. Além disso, os mecanismos de compilação impedem ou detectam a violação física do dispositivo.

- [Suporte a redes virtuais de IoT](virtual-network-support.md)
- [prática recomendada de rede grande](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Visão geral do link privado do Azure](../private-link/private-link-overview.md)
- [Grupo de segurança de rede do Azure](../virtual-network/network-security-groups-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede do Azure. Habilite logs de fluxo do grupo de segurança de rede e envie os logs para uma conta de armazenamento do Azure para auditoria. Você também pode enviar os logs de fluxo para um espaço de trabalho Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações sobre os padrões de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade da rede, identificar pontos de acesso e ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar as configurações incorretas da rede.
 
- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md)
 
- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: bloquear IPS mal-intencionados conhecidos com regras de filtro IP do Hub IOT. As tentativas mal-intencionadas também são registradas e alertadas por meio da central de segurança do Azure para IoT.

A proteção contra DDoS do Azure básica já está habilitada e disponível sem custo adicional como parte do Hub IoT. O monitoramento de tráfego sempre ativa e a mitigação em tempo real de ataques comuns em nível de rede, fornecem as mesmas defesas utilizadas pelo serviços online da Microsoft. Toda a escala da rede global do Azure pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões.

- [Filtro de IP do Hub IoT](iot-hub-ip-filtering.md)

- [Central de segurança do Azure para comunicação de endereço IP suspeito de IoT](../defender-for-iot/concept-security-alerts.md)

- [Gerenciar a proteção contra DDoS do Azure básica](../ddos-protection/ddos-protection-overview.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: não aplicável; Essa recomendação destina-se a ofertas que produzem pacotes de rede que podem ser registrados e exibidos pelos clientes. O Hub IoT não produz pacotes de rede voltados ao cliente e não é projetado para ser implantado diretamente em redes virtuais do Azure.

**Monitoramento da central de segurança do Azure**: não

**Responsabilidade**: não aplicável

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.  Quando a inspeção de carga não é um requisito, a inteligência de ameaças do firewall do Azure pode ser usada. A filtragem baseada em inteligência de ameaças do firewall do Azure é usada para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou bloquear o tráfego mal-intencionado. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: para recursos que precisam de acesso ao Hub IOT do Azure, use marcas de serviço de rede virtual para definir controles de acesso de rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureIoTHub) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Como usar marcas de serviço para o Azure IoT](iot-hub-understand-ip-address.md)
- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para recursos de rede associados aos namespaces do Hub IOT do Azure com o Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. Devices" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus namespaces de Machine Learning. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para recursos de rede associados à sua implantação do Hub IOT do Azure para organizá-las logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados ao Hub IOT do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como o Hub IOT do Azure para carimbos de data/hora nos logs.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Hub IOT do Azure. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento para armazenamento de longo prazo/arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e incidentes de segurança) de terceiros.

- [Configurar logs de IoT do Azure](monitor-iot-hub-reference.md#resource-logs)
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilitar as configurações de diagnóstico de IOT do Azure nos recursos do Azure para acessar logs de auditoria, segurança e recursos. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

- [Configurar logs do Hub IoT do Azure](monitor-iot-hub-reference.md#resource-logs)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados às instâncias do Hub IOT do Azure de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: analise e monitore os logs de comportamento anormal e Examine regularmente os resultados do Hub IOT do Azure. Use Azure Monitor e um espaço de trabalho Log Analytics para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros. 

- [Monitorar a integridade de IoT do Azure](monitor-iot-hub.md)
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Introdução às consultas de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)
   
- [ Como executar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança do Azure para IOT com um espaço de trabalho log Analytics para monitoramento e alertas em atividade anômala encontrada em eventos e logs de segurança. Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel. Você também pode definir alertas operacionais com Azure Monitor que podem ter implicações de segurança, como quando o tráfego cai inesperadamente.

- [Monitorar a integridade do Hub IoT do Azure](monitor-iot-hub.md)
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)
- [Central de segurança do Azure para alertas de IoT](../defender-for-iot/concept-security-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: não aplicável; O Hub IoT do Azure não processa nem produz logs relacionados a anti-malware.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Orientação**: não aplicável; O Hub IoT do Azure não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso ao Hub IOT do Azure por meio de atribuições de função. Você pode atribuir essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas. Há funções internas predefinidas para determinados recursos, e essas funções podem ser inventariadas ou consultadas por meio de ferramentas como CLI do Azure, ou Azure PowerShell ou a portal do Azure. 

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o gerenciamento de acesso aos recursos do Hub IOT do Azure é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas.

Você também pode habilitar o acesso just-in-time a contas administrativas usando Azure AD Privileged Identity Management e Azure Resource Manager.

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Orientação**: para usuários que acessam o Hub IOT, use Azure Active Directory SSO. Use as recomendações de acesso e identidade da central de segurança do Azure. 

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite o Azure ad MFA para proteger seu locatário geral do Azure, beneficiando todos os serviços. O serviço de Hub IoT não tem suporte para MFA.  

- [Como habilitar a MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma estação de trabalho segura e gerenciada pelo Azure (também conhecida como uma estação de trabalho com acesso privilegiado ou Paw) para tarefas administrativas que exigem privilégios elevados.

- [Entenda as estações de trabalho seguras e gerenciadas pelo Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar o Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use Azure Active Directory relatórios de segurança e monitoramento para detectar quando uma atividade suspeita ou insegura ocorre no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)
- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: para usuários que acessam o Hub IOT, não há suporte para acesso condicional. Para atenuar isso, use os locais nomeados do Azure AD para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões para seu locatário do Azure geral, beneficiando todos os serviços, incluindo o Hub IoT. 

- [Como configurar locais nomeados do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Orientação**: para acesso de usuário ao Hub IOT, use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Para acesso a dispositivo e serviço, o Hub IoT usa tokens de segurança e tokens de SAS (assinatura de acesso compartilhado) para autenticar dispositivos e serviços para evitar o envio de chaves na rede. 

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Tokens de segurança do Hub IoT](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de identidade e acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 

Use Azure AD Privileged Identity Management (PIM) para a geração de logs e alertas quando atividades suspeitas ou inseguras ocorrerem no ambiente.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)
- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)
- [Implantar o Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta Siem/Monitoring. 

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics. 

O usuário Azure Monitor logs de recursos para monitorar tentativas de conexão não autorizadas na categoria conexões.

- [Como integrar os logs de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Configurar logs de recursos para o Hub IoT](monitor-iot-hub.md#collection-and-routing)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use Azure ad Identity Protection recursos para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.
  
- [ Como exibir entradas arriscadas do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ Como carregar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: em cenários de suporte em que a Microsoft precisa acessar dados do cliente, ele será solicitado diretamente do cliente. 

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.
 
- [ Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do RBAC do Azure.
  
- [ Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)
- [ Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)
- [ Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Use uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar a transferência não autorizada de informações confidenciais e bloquear essas transferências ao alertar os profissionais de segurança de informações.

Para a plataforma subjacente gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e protege contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: o Hub IOT usa o protocolo TLS para proteger conexões de dispositivos e serviços IOT. Atualmente, três versões do protocolo TLS são compatíveis, principalmente as versões 1.0, 1.1 e 1.2. É altamente recomendável que você use o TLS 1.2 como a versão preferencial do TLS ao se conectar ao Hub IoT.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Suporte a TLS no Hub IoT](iot-hub-tls-support.md)
- [Entender a criptografia em trânsito com o Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o Hub IOT do Azure. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente do Azure gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Orientação**: para acesso de usuário do plano de controle ao Hub IOT, use o RBAC do Azure para controlar o acesso. Para acesso do plano de dados ao Hub IoT, use políticas de acesso compartilhado para o Hub IoT.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

- [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o Hub IoT do Azure e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: o Hub IOT dá suporte à criptografia de dados em repouso com chaves gerenciadas pelo cliente (CMK), também conhecidas como "Traga sua própria chave" (BYOK).

O Hub IoT do Azure fornece criptografia de dados em repouso e em trânsito conforme são gravados em nossos data centers e descriptografa-os para você conforme você o acessa. Por padrão, o Hub IoT usa chaves gerenciadas pela Microsoft para criptografar os dados em repouso.

- [Criptografia de dados em repouso com chaves gerenciadas pelo cliente para o Hub IoT](iot-hub-customer-managed-keys.md)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem para as instâncias de produção do Hub IOT do Azure e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao Hub IoT do Azure.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: não aplicável; A Microsoft executa o gerenciamento de patches nos sistemas subjacentes que dão suporte ao Hub IoT do Azure. 

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar uma solução de gerenciamento de patch automatizada para títulos de software de terceiros

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure (nem todos os recursos dão suporte a marcas, mas a maioria faz) para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.
  
- [ Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)
  
- [ Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)
  
- [ Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação de acordo com suas necessidades organizacionais.

Cada Hub IoT tem um registro de identidade que pode ser usado para criar recursos por dispositivo no serviço. As identidades de dispositivos individuais ou em grupo podem ser adicionadas a uma lista de permissões ou a uma lista de contatos bloqueados, permitindo o controle completo sobre o acesso ao dispositivo.

- [Registro de identidade do Hub IoT](iot-hub-devguide-identity-registry.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. 

Use o Azure Resource Graph para consultar e descobrir recursos dentro das assinaturas deles.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

* Tipos de recursos não permitidos
* Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure ad para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".
  
- [ Como configurar o acesso condicional para bloquear o acesso a Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para seu serviço de Hub IOT do Azure com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. Devices" para criar políticas personalizadas para auditar ou impor a configuração de seus serviços do Hub IoT do Azure.

Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Você também pode usar as recomendações da central de segurança do Azure como uma linha de base de configuração segura para os recursos do Azure.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure. Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização.  
 
- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)
- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)
- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: se você estiver usando definições de Azure Policy personalizadas para o Hub IOT do Azure ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. Devices" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure. Além disso, use Azure Policy para alertar e auditar configurações de recursos do Azure. 
 
- [ Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: o Hub IOT usa tokens de segurança e tokens de SAS (assinatura de acesso compartilhado) para autenticar dispositivos e serviços para evitar o envio de chaves na rede. 

Use identidades gerenciadas em conjunto com Azure Key Vault para simplificar o gerenciamento de segredos para seus aplicativos de nuvem.

- [Tokens de segurança do Hub IoT](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Como usar identidades gerenciadas para o Hub IoT](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Como criar um cofre de chaves](../key-vault/general/quick-create-portal.md)
- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: o Hub IOT usa tokens de segurança e tokens de SAS (assinatura de acesso compartilhado) para autenticar dispositivos e serviços para evitar o envio de chaves na rede. 

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

- [Tokens de segurança do Hub IoT](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Como configurar identidades gerenciadas para o Hub IoT](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 
 
- [  Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretriz**: Não aplicável; esta recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Serviço de Aplicativo do Azure), mas não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Hub IOT do Azure), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: não aplicável; Esse parâmetro de comparação destina-se a recursos de computação. O Microsoft Antimalware está habilitado no host subjacente que dá suporte aos serviços do Azure, no entanto, ele não é executado no conteúdo do cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Diretrizes**: o serviço de Hub IOT do Azure fornece métodos e estrutura para tornar os serviços do Hub IOT altamente disponíveis e recuperáveis de desastres com base em objetivos de negócios específicos. 

- [Alta disponibilidade e recuperação de desastres do Hub IoT](iot-hub-ha-dr.md)

- [Como clonar o Hub IoT](iot-hub-how-to-clone.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: o Hub IOT do Azure recomenda o Hub IOT secundário que deve conter todas as identidades de dispositivo que podem se conectar à solução. A solução deve manter backups replicados geograficamente das identidades do dispositivo e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é útil neste contexto.

- [Alta disponibilidade e recuperação de desastres do Hub IoT](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportação de identidade de dispositivo do Hub IoT](iot-hub-bulk-identity-mgmt.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: o Hub IOT do Azure recomenda o Hub IOT secundário que deve conter todas as identidades de dispositivo que podem se conectar à solução. A solução deve manter backups replicados geograficamente das identidades do dispositivo e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é útil neste contexto.

Executar periodicamente a restauração de dados de conteúdo no backup. Verifique se você pode restaurar as chaves gerenciadas pelo cliente de backup.

- [Alta disponibilidade e recuperação de desastres do Hub IoT](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportação de identidade de dispositivo do Hub IoT](iot-hub-bulk-identity-mgmt.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: habilite a exclusão reversível e a proteção de limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. Se o armazenamento do Azure for usado para armazenar backups, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos.
 
 
- [Entender o RBAC do Azure](../role-based-access-control/overview.md)
- [Exclusão reversível para o armazenamento de BLOBs do Azure](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 
  
- [ Orientação sobre como criar seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

  
 Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu.
  
- [ Alertas de segurança na central de segurança do Azure](../security-center/security-center-alerts-overview.md)
  
- [ Use marcas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário.
  
- [ Publicação do NIST – guia para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.
  
- [ Como definir o contato da segurança da central de segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel.
  
- [ Como configurar a exportação contínua](../security-center/continuous-export.md)
 
- [ Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Orientação**: usar o recurso de automação de fluxo de trabalho central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure.
  
- [ Como configurar a automação do fluxo de trabalho na central de segurança](../security-center/workflow-automation.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

## <a name="next-steps"></a>Próximas etapas

- Confira o [Azure Security Benchmark](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)