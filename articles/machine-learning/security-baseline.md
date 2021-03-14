---
title: Linha de base de segurança do Azure para Azure Machine Learning
description: A linha de base de segurança Azure Machine Learning fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 08aa69db90901447297e06cd111b6b0174e6637a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575100"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Linha de base de segurança do Azure para Azure Machine Learning

A linha de base de segurança do Azure para Microsoft Azure Machine Learning contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação. A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas. Para obter mais informações, consulte [Visão geral sobre linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, consulte o [benchmark de segurança do Azure: segurança de rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação. Os recursos de computação (destinos de computação) são usados para treinar e implantar modelos. Você pode criar esses destinos de computação em uma rede virtual. Por exemplo, você pode usar a instância de computação de Machine Learning virtual do Azure para treinar um modelo e, em seguida, implantar o modelo no AKS (serviço kubernetes do Azure). Você pode proteger seus ciclos de vida do Machine Learning isolando Azure Machine Learning trabalhos de treinamento e inferência em uma rede virtual do Azure.

O Firewall do Azure pode ser usado para controlar o acesso ao seu espaço de trabalho do Azure Machine Learning e à Internet pública.

- [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

- [Usar o espaço de trabalho por trás do firewall do Azure para Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: Azure Machine Learning se baseia em outros serviços do Azure para recursos de computação. Atribua grupos de segurança de rede às redes que são criadas como sua Machine Learning implantação. 

Habilite logs de fluxo do grupo de segurança de rede e envie os logs para uma conta de armazenamento do Azure para auditoria. Você também pode enviar os logs de fluxo para um espaço de trabalho Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações sobre os padrões de tráfego em sua nuvem do Azure. Algumas vantagens do Análise de Tráfego são a capacidade de visualizar a atividade da rede, identificar pontos de acesso e ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar as configurações incorretas da rede.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: você pode habilitar o HTTPS para proteger a comunicação com os serviços Web implantados pelo Azure Machine Learning. Os serviços Web são implantados nos AKS (serviços de kubernetes do Azure) ou ACI (instâncias de contêiner do Azure) e protegem os dados enviados pelos clientes. Você também pode usar o IP privado com AKS para restringir a pontuação, de modo que somente os clientes por trás de uma rede virtual possam acessar o serviço Web.

- [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)

- [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilite a proteção contra DDoS Standard nas redes virtuais associadas à sua instância de Machine Learning para proteger contra ataques de DDoS (negação de serviço distribuído). Use a detecção de ameaças integradas da central de segurança do Azure para detectar comunicações com endereços IP de Internet mal-intencionados ou não utilizados conhecidos.

Implante o Firewall do Azure em cada um dos limites de rede da organização com a filtragem baseada em inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Usar o espaço de trabalho por trás do firewall do Azure para Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Para obter mais informações sobre a detecção de ameaças da central de segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: para qualquer VM com a extensão apropriada instalada em seus serviços de Azure Machine Learning, você pode habilitar a captura de pacotes do observador de rede para investigar atividades anormais. 

- [Como criar uma instância do observador de rede](../network-watcher/network-watcher-create.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou bloquear o tráfego mal-intencionado.

Selecione uma oferta do Azure Marketplace que dê suporte à funcionalidade de IDS/IPS com recursos de inspeção de conteúdo.  Quando a inspeção de carga não é um requisito, a inteligência de ameaças do firewall do Azure pode ser usada. A filtragem baseada em inteligência de ameaças do firewall do Azure é usada para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: para recursos que precisam de acesso à sua conta do Azure Machine Learning, use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureMachineLearning) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Azure Machine Learning serviço documenta uma lista de marcas de serviço para seus destinos de computação em uma rede virtual que ajuda a minimizar a complexidade, você pode usá-la como diretrizes em seu gerenciamento de rede.

- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede associados aos seus namespaces de Azure Machine Learning com Azure Policy. Use aliases de Azure Policy nos namespaces "Microsoft. MachineLearning" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus namespaces de Machine Learning. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para recursos de rede associados à sua implantação do Azure Machine Learning para organizá-los logicamente de acordo com uma taxonomia.

Para um recurso em seu Azure Machine Learning rede virtual que ofereça suporte ao campo Descrição, use-o para documentar as regras que permitem o tráfego de/para uma rede.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados a Azure Machine Learning. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: a Microsoft mantém a fonte de tempo usada para recursos do Azure, como Azure Machine Learning para carimbos de data/hora nos logs.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: ingerir logs por meio de Azure monitor para agregar dados de segurança gerados pelo Azure Machine Learning. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento. Como alternativa, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros.

- [Como configurar logs de diagnóstico para Azure Machine Learning](monitor-azure-machine-learning.md#configuration)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: habilite as configurações de diagnóstico nos recursos do Azure para acessar logs de auditoria, segurança e diagnóstico. Logs de atividade, que estão automaticamente disponíveis, incluem origem do evento, data, usuário, carimbo de data/hora, endereços de origem, endereços de destino e outros elementos úteis.

Você também pode correlacionar os logs de operação de serviço Machine Learning para fins de segurança e conformidade.

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Entender o registro em log e diferentes tipos de log no Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Habilitar logon Azure Machine Learning](./how-to-track-experiments.md)

- [Azure Machine Learning de monitoramento](monitor-azure-machine-learning.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável por coleta e monitoramento. 

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para todos os recursos de computação que pertencem à sua organização, use a central de segurança do Azure para monitorar o sistema operacional. 

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Entender a coleta de dados da central de segurança do Azure](../security-center/security-center-enable-data-collection.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados às suas instâncias de Azure Machine Learning de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Orientação**: analise e monitore os logs de comportamento anormal e Examine regularmente os resultados do seu Azure Machine Learning. Use Azure Monitor e um espaço de trabalho Log Analytics para examinar os logs e executar consultas nos dados de log.

Como alternativa, você pode habilitar o e os dados integrados para o Azure Sentinel ou um SIEM de terceiros. 

- [Como executar consultas para Azure Machine Learning em espaços de trabalho do Log Analytics](monitor-azure-machine-learning.md#analyzing-log-data)

- [Habilitar logon Azure Machine Learning](./how-to-track-experiments.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introdução às consultas de Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Como realizar consultas personalizadas no Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: em Azure monitor, configure logs relacionados a Azure Machine Learning no log de atividades e Machine Learning configurações de diagnóstico para enviar logs para um espaço de trabalho log Analytics para serem consultados ou em uma conta de armazenamento para armazenamento de arquivamento de longo prazo. Use Log Analytics espaço de trabalho para criar alertas para atividade anômala encontrada em eventos e logs de segurança.

Como alternativa, você pode habilitar e dados integrados para o Azure Sentinel.

- [Para obter mais informações sobre alertas de Azure Machine Learning](monitor-azure-machine-learning.md#alerts)

- [Como alertar sobre Log Analytics dados de log do espaço de trabalho](../azure-monitor/alerts/tutorial-response.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pela implantação antimalware do serviço de Azure Machine Learning. 

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, habilite a coleta de eventos antimalware para Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais.

- [Como configurar o Microsoft antimalware para uma máquina virtual](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Como configurar a extensão Microsoft antimalware para serviços de nuvem](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Entender o Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Orientação**: não aplicável; Azure Machine Learning não processa nem produz logs relacionados ao DNS.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação pertencentes à sua organização, use a central de segurança do Azure para habilitar o monitoramento do log de eventos de segurança para máquinas virtuais do Azure. A central de segurança do Azure provisiona o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas se o provisionamento automático estiver habilitado. Ou você pode instalar o agente manualmente. O agente habilita o evento 4688 de criação de processo e o campo CommandLine dentro do evento 4688. Os novos processos criados na VM são registrados pelo log de eventos e monitorados pelos serviços de detecção da central de segurança.

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Orientação**: você pode usar a guia gerenciamento de identidade e acesso de um recurso no portal do Azure para configurar o controle de acesso baseado em função do Azure (RBAC do Azure) e manter o inventário em Azure Machine Learning recursos. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos.

O Azure Machine Learning fornece funções internas para cenários comuns de gerenciamento no Azure Machine Learning. Um indivíduo que tem um perfil no Azure Active Directory (Azure AD) pode atribuir essas funções a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Machine Learning recursos.

Você também pode usar o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Entender o controle de acesso baseado em função do Azure no Azure Machine Learning](how-to-assign-roles.md)

- [Como obter uma função de diretório no Azure Active Directory com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o gerenciamento de acesso para Machine Learning recursos é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Diretrizes**: Azure Machine Learning vem com três funções padrão quando um novo espaço de trabalho é criado, criando procedimentos operacionais padrão em relação ao uso de contas de proprietário.

Você também pode habilitar um acesso just-in-time às contas administrativas usando Azure AD Privileged Identity Management e Azure Resource Manager. 

- [Para saber mais Machine Learning funções padrão](how-to-assign-roles.md#default-roles)

- [Saiba mais sobre o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: Machine Learning é integrado ao Azure Active Directory, use Azure Active Directory SSO em vez de configurar credenciais autônomas individuais por serviço. Use as recomendações de acesso e identidade da central de segurança do Azure.  

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite Azure Active Directory autenticação multifator e siga as recomendações de acesso e identidade da central de segurança do Azure.

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

**Orientação**: use locais nomeados do Azure ad para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.
 
 
 
- [Como configurar locais nomeados do Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.
 
O acesso à função pode ser definido para vários níveis no Azure. Por Machine Learning, as funções podem ser gerenciadas no nível do espaço de trabalho, por exemplo, você tem acesso de proprietário a um espaço de trabalho pode não ter acesso de proprietário ao grupo de recursos que contém o espaço de trabalho. Isso fornece controles de acesso mais granulares para separar funções dentro do mesmo grupo de recursos. 

- [Gerenciar acesso a um workspace do Azure Machine Learning](how-to-assign-roles.md) 
 
- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: O Azure AD fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de identidade e acesso do Azure AD para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos tenham acesso contínuo. 
 
Use o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de identidade e acesso do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Implantar o Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco, que permitem a integração com qualquer ferramenta Siem/Monitoring.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no espaço de trabalho Log Analytics.
 
 
- [Como integrar os logs de atividades do Azure com o Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use Azure ad Identity Protection recursos para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.
 
- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)
 
- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não aplicável; O serviço de Azure Machine Learning não dá suporte a Lockbox de cliente.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.
 
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: implemente o isolamento usando assinaturas e grupos de gerenciamento separados para domínios de segurança individuais, como tipo de ambiente e nível de sensibilidade de dados. Você pode restringir o nível de acesso aos recursos do Azure que seus aplicativos e ambientes empresariais exigem. Você pode controlar o acesso aos recursos do Azure por meio do RBAC do Azure.
 
- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)
 
- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Use uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorar a transferência não autorizada de informações confidenciais e bloquear essas transferências ao alertar os profissionais de segurança de informações. 

Quanto à plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e fornece proteção contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: os serviços Web implantados por meio do Azure Machine Learning só dão suporte à versão 1,2 do TLS que impõe a criptografia de dados em trânsito.

- [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para Azure Machine Learning. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente, que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Diretrizes**: o Azure Machine Learning dá suporte ao uso do Azure Active Directory (AD do Azure) para autorizar solicitações para Machine Learning recursos. Com o Azure AD, você pode usar o Azure RBAC (controle de acesso baseado em função) para conceder permissões a uma entidade de segurança, que pode ser um usuário ou uma entidade de serviço de aplicativo.

- [Gerenciar acesso a um workspace do Azure Machine Learning](how-to-assign-roles.md)
- [Usar o RBAC do Azure para autorização kubernetes](../aks/manage-azure-rbac.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para Machine Learning e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: Azure Machine Learning armazena instantâneos, saída e logs na conta de armazenamento de BLOBs do Azure que está vinculada ao espaço de trabalho Azure Machine Learning e à sua assinatura. Todos os dados armazenados no armazenamento de blobs do Azure são criptografados em repouso com chaves gerenciadas pela Microsoft. Você também pode criptografar os dados armazenados no armazenamento de BLOBs do Azure com suas próprias chaves no serviço Machine Learning. 

- [Azure Machine Learning a criptografia de dados em repouso](concept-enterprise-security.md#encryption-at-rest)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md)

- [Como configurar chaves de criptografia gerenciadas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em instâncias de produção do Azure Machine Learning e outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pelo gerenciamento de vulnerabilidades do serviço de Azure Machine Learning. 

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que são de propriedade de sua organização, siga as recomendações da central de segurança do Azure para executar avaliações de vulnerabilidade em suas máquinas virtuais do Azure, imagens de contêiner e SQL Servers.

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pelo gerenciamento de patches do serviço de Azure Machine Learning. 

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para todos os recursos de computação que pertencem à sua organização, use a Gerenciamento de Atualizações de automação do Azure para garantir que as atualizações de segurança mais recentes sejam instaladas em suas VMs Windows e Linux. Para VMs do Windows, verifique se Windows Update foi habilitado e definido para atualizar automaticamente.

- [Como configurar Gerenciamento de Atualizações para máquinas virtuais no Azure](../automation/update-management/overview.md)

- [Entender as políticas de segurança do Azure monitoradas pela central de segurança](../security-center/policy-reference.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar uma solução de gerenciamento de patch automatizada para títulos de software de terceiros

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use uma solução de gerenciamento de patches de terceiros. Os clientes que já usam Configuration Manager em seu ambiente também podem usar System Center Updates Publisher, permitindo que eles publiquem atualizações personalizadas no Windows Server Update Service. Isso permite que Gerenciamento de Atualizações corrija os computadores que usam Configuration Manager como seu repositório de atualizações com software de terceiros.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, siga as recomendações da central de segurança do Azure para executar avaliações de vulnerabilidade em suas máquinas virtuais do Azure, imagens de contêiner e SQL Servers. Exporte os resultados da verificação em intervalos consistentes e compare os resultados com verificações anteriores para verificar se as vulnerabilidades foram corrigidas. Ao usar as recomendações de gerenciamento de vulnerabilidade sugeridas pela central de segurança do Azure, você pode dinamizar o portal da solução selecionada para exibir dados de verificação históricas.

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretriz**: não aplicável; essa diretriz destina-se a recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir recursos (como computação, armazenamento, rede, portas e protocolos, etc.) em suas assinaturas.  Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Gerenciador de gráficos de recursos do Azure, é altamente recomendável criar e usar Azure Resource Manager recursos no futuro.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure, adicionando metadados para organizar logicamente de acordo com uma taxonomia.

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

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

* Tipos de recursos não permitidos
* Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar/descobrir recursos dentro das assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use a automação do Azure Controle de Alterações e o inventário para automatizar a coleta de informações de inventário de suas VMs Windows e Linux. O nome do software, a versão, o Publicador e o tempo de atualização estão disponíveis no portal do Azure. Para obter a data de instalação do software e outras informações, habilite o diagnóstico em nível de convidado e direcione os logs de eventos do Windows para Log Analytics espaço de trabalho.

- [Como habilitar a coleta de inventário de automação do Azure para uma VM](../automation/automation-tutorial-installed-software.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use o FIM (monitoramento de integridade de arquivo) da central de segurança do Azure para identificar todos os softwares instalados nas VMs. Outra opção que pode ser usada em vez de ou em conjunto com o FIM é a automação do Azure Controle de Alterações e o inventário para coletar o inventário de suas VMs Linux e Windows. 

Você pode implementar seu próprio processo para remover software não autorizado. Você também pode usar uma solução de terceiros para identificar softwares não aprovados.

Remova os recursos do Azure quando eles não forem mais necessários.

- [Como usar o monitoramento de integridade de arquivo](../security-center/security-center-file-integrity-monitoring.md)

- [Entender o inventário e Controle de Alterações de automação do Azure](../automation/change-tracking/overview.md)

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

- [Exclusão de recursos e grupo de recursos do Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use os controles de aplicativo adaptável da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Compartilhado

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

* Tipos de recursos não permitidos
* Tipos de recursos permitidos

Além disso, use o grafo de recursos do Azure para consultar e descobrir recursos nas assinaturas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para qualquer recurso de computação que pertença à sua organização, use os controles de aplicativo adaptável da central de segurança do Azure para especificar a quais tipos de arquivo uma regra pode ou não se aplicar.

Implemente uma solução de terceiros se os controles de aplicativo adaptáveis não atenderem ao requisito.

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure ad para limitar a capacidade dos usuários de interagir com o Gerenciador de recursos do Azure Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".
 
- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, dependendo do tipo de scripts, você pode usar configurações específicas do sistema operacional ou recursos de terceiros para limitar a capacidade dos usuários de executar scripts nos recursos de computação do Azure.  Você também pode usar os controles de aplicativo adaptáveis da central de segurança do Azure para garantir que apenas o software autorizado seja executado e todos os softwares não autorizados sejam impedidos de serem executados em máquinas virtuais do Azure.

- [Como controlar a execução de script do PowerShell em ambientes Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para seu serviço de Azure Machine Learning com Azure Policy. Use Azure Policy aliases no namespace "Microsoft. MachineLearning" para criar políticas personalizadas para auditar ou impor a configuração de seus serviços de Azure Machine Learning.

Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Você também pode usar as recomendações da central de segurança do Azure como uma linha de base de configuração segura para os recursos do Azure.

Azure Machine Learning dá suporte total a repositórios git para acompanhamento do trabalho; Você pode clonar repositórios diretamente em seu sistema de arquivos de espaço de trabalho compartilhado, usar o Git em sua estação de trabalho local e garantir que as configurações seguras se apliquem a recursos de código como parte de seu ambiente de Machine Learning.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pelas configurações seguras do sistema operacional do serviço de Azure Machine Learning.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use as recomendações da central de segurança do Azure para manter as configurações de segurança em todos os recursos de computação.  Além disso, você pode usar imagens personalizadas do sistema operacional ou a configuração de estado da automação do Azure para estabelecer a configuração de segurança do sistema operacional exigido por sua organização.

- [Como monitorar as recomendações da central de segurança do Azure](../security-center/security-center-recommendations.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md)

- [Carregar um VHD e usá-lo para criar novas VMs do Windows no Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Criar uma VM Linux usando um disco personalizado com a CLI do Azure](../virtual-machines/linux/upload-vhd.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure. Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 
 
 
 
- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)
 
- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)
 
- [ Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pelas configurações seguras do sistema operacional do serviço de Azure Machine Learning.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, siga as recomendações da central de segurança do Azure sobre como executar avaliações de vulnerabilidade em seus recursos de computação do Azure.  Além disso, você pode usar modelos de Azure Resource Manager, imagens do sistema operacional personalizado ou configuração de estado da automação do Azure para manter a configuração de segurança do sistema operacional exigido pela sua organização.   Os modelos de máquina virtual da Microsoft combinados com a configuração de estado de automação do Azure podem ajudar a atender e a manter os requisitos de segurança. 

Observe que as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são gerenciadas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

- [Como criar uma máquina virtual do Azure por meio de um modelo do ARM](../virtual-machines/windows/ps-template.md)

- [Visão geral da configuração do estado de automação do Azure](../automation/automation-dsc-overview.md)

- [Criar uma máquina virtual do Windows no portal do Azure ](../virtual-machines/windows/quick-create-portal.md)

- [Informações sobre como baixar o modelo de VM](/previous-versions/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Orientação**: se você estiver usando definições de Azure Policy personalizadas para seu Machine Learning ou recursos relacionados, use Azure Repos para armazenar e gerenciar seu código com segurança.

Azure Machine Learning dá suporte total a repositórios git para acompanhamento do trabalho; Você pode clonar repositórios diretamente em seu sistema de arquivos de espaço de trabalho compartilhado, usar o Git em sua estação de trabalho local e garantir que as configurações seguras se apliquem a recursos de código como parte de seu ambiente de Machine Learning.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretrizes**: Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que somente usuários autorizados possam acessar suas imagens personalizadas. Use uma galeria de imagens compartilhadas do Azure você pode compartilhar suas imagens para diferentes usuários, entidades de serviço ou grupos do Azure AD em sua organização. Armazene imagens de contêiner no registro de contêiner do Azure e use o RBAC do Azure para garantir que somente usuários autorizados tenham acesso.

- [Entender o RBAC do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Entender o RBAC do Azure para registro de contêiner](../container-registry/container-registry-roles.md)

- [Como configurar o RBAC do Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Visão geral da Galeria de imagens compartilhadas](../virtual-machines/shared-image-galleries.md)

- [Usar o RBAC do Azure para autorização kubernetes](../aks/manage-azure-rbac.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: não aplicável

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. MachineLearning" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pela implantação de configuração segura do serviço de Azure Machine Learning.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use a configuração de estado da automação do Azure para os nós de DSC (configuração de estado desejado) em qualquer datacenter local ou na nuvem. Você pode, facilmente, integrar máquinas, atribuir a elas configurações declarativas e exibir relatórios que mostram a conformidade de cada computador com o estado desejado especificado. 

- [Como habilitar a configuração de estado da automação do Azure](../automation/automation-dsc-onboarding.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure. Além disso, use Azure Policy para alertar e auditar configurações de recursos do Azure.
 
 
 
- [ Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Orientação**: se o recurso de computação pertence à Microsoft, a Microsoft é responsável pelo monitoramento automatizado da configuração segura do serviço de Azure Machine Learning.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use aplicativos de computação da central de segurança do Azure &amp; e siga as recomendações para VMs e servidores e contêineres.

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidades gerenciadas em conjunto com Azure Key Vault para simplificar o gerenciamento de segredos para seus aplicativos de nuvem.

Azure Machine Learning dá suporte à criptografia de armazenamento de dados com chaves gerenciadas pelo cliente, você precisa gerenciar a rotação de chaves e revogar os requisitos de segurança e conformidade da organização. 

Use Azure Key Vault para passar segredos para execuções remotas com segurança, em vez de texto não criptografado em seus scripts de treinamento.

- [Azure Machine Learning chaves gerenciadas pelo cliente](concept-enterprise-security.md#azure-blob-storage)

- [Usar segredos de credencial de autenticação em Azure Machine Learning execuções de treinamento](how-to-use-secrets-in-runs.md)

- [Como usar identidades gerenciadas para recursos do Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Key Vault](../key-vault/general/quick-create-portal.md)

- [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: o Azure Machine Learning dá suporte a funções internas e à capacidade de criar funções personalizadas. Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

 
- [Gerenciar acesso a um workspace do Azure Machine Learning](how-to-assign-roles.md)

- [Como configurar identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Machine Learning), no entanto, ele não é executado no conteúdo do cliente.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para recursos de computação que pertencem à sua organização, use o Microsoft antimalware para Azure para monitorar e defender continuamente seus recursos. Para o Linux, use a solução antimalware de terceiros. Além disso, use a detecção de ameaças da central de segurança do Azure para serviços de dados para detectar malwares carregados em contas de armazenamento.

- [Como configurar o Microsoft antimalware para o Azure](../security/fundamentals/antimalware.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: o antimalware da Microsoft está habilitado no host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Machine Learning), no entanto, ele não é executado no conteúdo do cliente.

É sua responsabilidade verificar previamente qualquer conteúdo que esteja sendo carregado em recursos não computados do Azure. A Microsoft não pode acessar dados do cliente e, portanto, não pode realizar verificações antimalware do conteúdo do cliente em seu nome.

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: o antimalware da Microsoft está habilitado e mantido para o host subjacente que dá suporte aos serviços do Azure (por exemplo, Azure Machine Learning), no entanto, ele não é executado no conteúdo do cliente.

Azure Machine Learning tem suporte variado em diferentes recursos de computação e até mesmo seus próprios recursos de computação. Para todos os recursos de computação que pertencem à sua organização, siga as recomendações na central de segurança do Azure, &amp; aplicativos de computação para garantir que todos os pontos de extremidade estejam atualizados com as assinaturas mais recentes. Para o Linux, use a solução antimalware de terceiros.

- [Como implantar o Microsoft antimalware para o Azure](../security/fundamentals/antimalware.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Compartilhado

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Diretrizes**: o gerenciamento de recuperação de dados no serviço Machine Learning é por meio de gerenciamento de dados em armazenamentos de dados conectados. Certifique-se de acompanhar as diretrizes de recuperação de dados para armazenamentos conectados para fazer backup de dados por políticas de organização do cliente.

- [Como recuperar arquivos do backup de máquina virtual do Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: o backup de dados no serviço Machine Learning é por meio de gerenciamento de dados em armazenamentos de dados conectados. Habilite o backup do Azure para VMs e configure a frequência e os períodos de retenção desejados. Fazer backup de chaves gerenciadas pelo cliente no Azure Key Vault.

- [Como recuperar arquivos do backup de máquina virtual do Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Como restaurar chaves de Key Vault no Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: a validação de backup de dados no serviço Machine Learning é por meio de gerenciamento de dados em armazenamentos de dados conectados. Executar periodicamente a restauração de dados de conteúdo no backup do Azure. Verifique se você pode restaurar as chaves gerenciadas pelo cliente de backup.

- [Como recuperar arquivos de um backup de máquina virtual do Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: para o backup local, a criptografia em repouso é fornecida usando a senha que você fornece ao fazer backup no Azure. Use o controle de acesso baseado em função do Azure para proteger backups e chaves gerenciadas pelo cliente. 

Habilite a exclusão reversível e a proteção de limpeza no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada. Se o armazenamento do Azure for usado para armazenar backups, habilite a exclusão reversível para salvar e recuperar seus dados quando BLOBs ou instantâneos de blob forem excluídos.
 
 
- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

- [Como habilitar a exclusão temporária e a proteção de limpeza no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Exclusão reversível para o armazenamento de BLOBs do Azure](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, consulte o [benchmark de segurança do Azure: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretrizes**: desenvolver um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de manipulação e gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Use o guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: a central de segurança do Azure atribui uma severidade a cada alerta para ajudá-lo a priorizar quais alertas devem ser investigados primeiro. A gravidade é baseada em quão confiante a central de segurança está na localização ou na forma analítica usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na importância dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoramento da Central de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário.

- [Publicação do NIST – guia para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua, continuamente. Você pode usar o conector de dados da central de segurança do Azure para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoramento da central de segurança do Azure**: não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Orientação**: usar o recurso de automação de fluxo de trabalho central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação do fluxo de trabalho na central de segurança](../security-center/workflow-automation.md)

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