---
title: Linha de base de segurança do Azure para o serviço kubernetes do Azure
description: A linha de base de segurança do serviço kubernetes do Azure fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: de32de5fc78919154bade3895cab6da82b175a93
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740575"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Linha de base de segurança do Azure para o serviço kubernetes do Azure

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) para o Azure kubernetes. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure kubernetes. Os **controles** não aplicáveis ao Azure kubernetes foram excluídos.

 
Para ver como o Azure kubernetes é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de kubernetes do Azure completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: por padrão, um grupo de segurança de rede e uma tabela de rotas são criados automaticamente com a criação de um cluster do AKS (serviço de Kubernetes) Microsoft Azure. O AKS modifica automaticamente os grupos de segurança de rede para o fluxo de tráfego apropriado, pois os serviços são criados com balanceadores de carga, mapeamentos de porta ou rotas de entrada. O grupo de segurança de rede é associado automaticamente às NICs virtuais em nós de cliente e à tabela de rotas com a sub-rede na rede virtual. 

Use as políticas de rede do AKS para limitar o tráfego de rede definindo regras para tráfego de entrada e saída entre os pods do Linux em um cluster com base na escolha de namespaces e seletores de rótulo. O uso da política de rede requer o plug-in do CNI do Azure com rede virtual e sub-redes definidas e só pode ser habilitado na criação do cluster. Eles não podem ser implantados em um cluster AKS existente.

Você pode implementar um cluster AKS privado para garantir que o tráfego de rede entre o servidor de API do AKS e os pools de nós permaneça apenas na rede privada. O plano de controle ou servidor de API, reside em uma assinatura do Azure AKS e usa endereços IP internos (RFC1918), enquanto o cluster ou o pool do nó do cliente está em sua própria assinatura. O servidor e o cluster ou pool de nós se comunicam entre si usando o serviço de vínculo privado do Azure na rede virtual do servidor de API e um ponto de extremidade privado que é exposto na sub-rede do cluster AKS do cliente.  Como alternativa, use um ponto de extremidade público para o servidor de API AKS, mas restrinja o acesso com o recurso de intervalos de IP autorizados do servidor de API AKS. 

- [Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)](concepts-security.md)

- [Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)](use-network-policies.md)

- [Criar um cluster privado do Serviço de Kubernetes do Azure](private-clusters.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: Use a central de segurança e siga suas recomendações de proteção de rede para proteger os recursos de rede usados por seus clusters do AKS (serviço kubernetes do Azure). 

Habilite logs de fluxo do grupo de segurança de rede e envie os logs para uma conta de armazenamento do Azure para auditoria. Você também pode enviar os logs de fluxo para um espaço de trabalho Log Analytics e, em seguida, usar Análise de Tráfego para fornecer informações sobre os padrões de tráfego em sua nuvem do Azure para visualizar a atividade de rede, identificar pontos de acesso e ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar as configurações incorretas da rede.

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar logs de fluxo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: use aplicativo Azure um WAF (firewall do aplicativo Web) habilitado para gateway na frente de um cluster AKs para fornecer uma camada adicional de segurança filtrando o tráfego de entrada para seus aplicativos Web. O Azure WAF usa um conjunto de regras, fornecido pelo OWASP (Open Web Application Security Project), para ataques, como scripts entre sites ou envenenamento de cookie contra esse tráfego. 

Use um gateway de API para autenticação, autorização, limitação, Caching, transformação e monitoramento para APIs usadas em seu ambiente AKS. Um gateway de API serve como uma porta de front-end para os microserviços, dissocia clientes de seus microserviços e diminui a complexidade de seus microserviços removendo a carga de lidar com questões de corte cruzada.

- [Entender as práticas recomendadas para conectividade de rede e segurança no AKS](operator-best-practices-network.md)

- [Controlador de entrada do gateway de aplicativo ](../application-gateway/ingress-controller-overview.md)

- [Usar o gerenciamento de API do Azure com microservices implantados no serviço kubernetes do Azure](../api-management/api-management-kubernetes.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilitar a proteção padrão de DDoS (negação de serviço distribuído) da Microsoft nas redes virtuais em que os componentes do AKS (serviço de kubernetes do Azure) são implantados para proteger contra ataques de DDoS.

Instale o mecanismo de política de rede e crie políticas de rede kubernetes para controlar o fluxo de tráfego entre pods no AKS como, por padrão, todo o tráfego é permitido entre esses pods. A política de rede só deve ser usada para nós e pods baseados em Linux no AKS. Defina regras que limitam a comunicação pod para melhorar a segurança. 

Escolha permitir ou negar tráfego com base em configurações, como rótulos, namespace ou porta de tráfego atribuídos. As políticas de rede necessárias podem ser aplicadas automaticamente, pois os pods são criados dinamicamente em um cluster AKS. 

- [Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)](use-network-policies.md)

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: Use a captura de pacote do observador de rede conforme necessário para investigar a atividade anômala. 

O observador de rede é habilitado automaticamente na região da sua rede virtual quando você cria ou atualiza uma rede virtual em sua assinatura. Você também pode criar novas instâncias do observador de rede usando o PowerShell, a CLI do Azure, a API REST ou o método de cliente Azure Resource Manager

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: Proteja seu cluster AKs (serviço kubernetes do Azure) com um gateway aplicativo Azure habilitado com um firewall do aplicativo Web (WAF). 

Se a detecção de intrusão e/ou prevenção baseada na inspeção de carga ou análise de comportamento não for um requisito, um gateway de Aplicativo Azure com WAF poderá ser usado e configurado no "modo de detecção" para registrar alertas e ameaças, ou "modo de prevenção", para bloquear ativamente invasões e ataques detectados.

- [Entenda as práticas recomendadas para proteger o cluster AKS com um WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Como implantar Aplicativo Azure gateway (WAF do Azure)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede associados às instâncias do AKS (serviço kubernetes do Azure). As marcas de serviço podem ser usadas no lugar de endereços IP específicos ao criar regras de segurança para permitir ou negar o tráfego para o serviço correspondente, especificando o nome da marca de serviço. 

A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Aplique uma marca do Azure a pools de nós em seu cluster AKS. Eles são diferentes das marcas de serviço de rede virtual e são aplicados a cada nó dentro do pool de nós e persistem nas atualizações. 

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Entender o NSGs para AKS](support-policies.md)

- [Controlar o tráfego de saída dos nós de cluster no Serviço de Kubernetes do Azure (AKS)](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão com Azure Policy para recursos de rede associados aos seus clusters do AKS (serviço kubernetes do Azure). 

Use aliases de Azure Policy nos namespaces "Microsoft. ContainerService" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus clusters AKS. 

Além disso, use definições de política internas relacionadas a AKS, como:

- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes

- Impor a entrada de HTTPS no cluster do Kubernetes

- Garantir que os serviços escutem somente em portas permitidas no cluster do Kubernetes

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para grupos de segurança de rede e outros recursos para o fluxo de tráfego de e para clusters do AKS (serviço kubernetes do Azure). Use o campo "Descrição" para regras de grupo de segurança de rede individuais para especificar a necessidade de negócios e/ou duração, e assim por diante, para qualquer regra que permita o tráfego de/para uma rede.

Use qualquer uma das definições relacionadas à marcação de Azure Policy internas, por exemplo, "exigir marca e seu valor" que garanta que todos os recursos sejam criados com marcas e recebam notificações para recursos não marcados existentes.

Escolha permitir ou negar caminhos de rede específicos no cluster com base em namespaces e seletores de rótulo com políticas de rede. Use esses namespaces e rótulos como descritores para regras de configuração de tráfego. Use Azure PowerShell ou CLI (interface de linha de comando) do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Azure Policy com CLI](/cli/azure/policy)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos clusters do AKS (serviço kubernetes do Azure). 

Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. Todas as entradas do usuário AzureContainerService nos logs de atividade são registradas como ações de plataforma. 

Use logs de Azure Monitor para habilitar e consultar os logs de AKS os componentes mestres, Kube-apiserver e Kube-Controller-Manager. Crie e gerencie os nós que executam o kubelet com tempo de execução de contêiner e implante seus aplicativos por meio do servidor de API kubernetes gerenciado. 

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Habilitar e examinar os logs do nó mestre do Kubernetes no AKS (Serviço de Kubernetes do Azure)](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: os nós do AKS (serviço de kubernetes do Azure) usam o NTP.Ubuntu.com para sincronização de horário, juntamente com a porta UDP 123 e a protocolo NTP (NTP). 

Verifique se os servidores NTP estão acessíveis pelos nós do cluster se estiver usando servidores DNS personalizados. 

- [Entender os requisitos de domínio e porta do NTP para nós de cluster AKS](limit-egress-traffic.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: Habilitar logs de auditoria dos componentes mestres do AKS (serviços Kubernetess do Azure), Kube-apiserver e Kube-Controller-Manager, que são fornecidos como um serviço gerenciado. 

- Kube-auditaksService: o nome de exibição no log de auditoria para a operação de plano de controle (do hcpService) 

- MasterClient: o nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido de AZ AKs Get-Credentials 

- nodeclient: o nome de exibição para ClientCertificate, que é usado por nós de agente

Habilite outros logs de auditoria, como Kube-Audit também. 

Exporte esses logs para Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Habilite e integre esses dados ao Azure Sentinel ou a um SIEM de terceiros com base em seus requisitos de negócios organizacionais.

- [Examine o esquema de log, incluindo funções de log aqui](/azure/aks/view-master-logs)

- [Entender Azure Monitor para contêineres](/azure/azure-monitor/insights/container-insights-overview)

- [Como habilitar o Azure Monitor para contêineres](/azure/azure-monitor/insights/container-insights-onboard)

- [Habilitar e examinar os logs do nó mestre do Kubernetes no AKS (Serviço de Kubernetes do Azure)](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: Use logs de atividade para monitorar ações em recursos do AKS (serviço kubernetes do Azure) para exibir todas as atividades e seu status. Determine quais operações foram executadas nos recursos em sua assinatura com os logs de atividade: 

- quem iniciou a operação
- quando a operação ocorreu
- o status da operação
- os valores de outras propriedades que podem ajudar você a pesquisar a operação

Recupere informações do log de atividades por meio do Azure PowerShell, a CLI (interface de linha de comando) do Azure, a API REST do Azure ou o portal do Azure. 

Habilite os logs de auditoria em componentes mestres do AKS, como: 

- Kube-auditaksService: o nome de exibição no log de auditoria para a operação de plano de controle (do hcpService) 

- MasterClient: o nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido de AZ AKs Get-Credentials 

- nodeclient: o nome de exibição para ClientCertificate, que é usado por nós de agente

Ative outros logs de auditoria, como Kube-Audit também. 

- [Como habilitar e examinar os logs do nó mestre do kubernetes no AKS](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: habilitar a instalação automática de agentes de log Analytics para coletar dados dos nós de cluster AKs. Além disso, ative o provisionamento automático do agente de monitoramento de Log Analytics do Azure na central de segurança do Azure, como por padrão, o provisionamento automático está desativado. O agente também pode ser instalado manualmente. Com o provisionamento automático no, a central de segurança implanta o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas criadas. A central de segurança coleta dados de VMs (máquinas virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais e contêineres de IaaS, como nós de cluster kubernetes, para monitorar vulnerabilidades e ameaças de segurança. Os dados são coletados usando o agente de Log Analytics do Azure, que lê várias configurações relacionadas à segurança e logs de eventos do computador e copia os dados para o espaço de trabalho para análise. 

A coleta de dados é necessária para fornecer visibilidade de atualizações ausentes, configurações de segurança do sistema operacional configuradas incorretamente, status do Endpoint Protection e detecções de integridade e ameaças.

- [Como habilitar o provisionamento automático do agente de Log Analytics](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: integrar suas instâncias do AKS (serviço kubernetes do Azure) para Azure monitor e definir o período de retenção do espaço de trabalho do Azure log Analytics correspondente de acordo com os requisitos de conformidade da sua organização. 

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Orientação**: integrar suas instâncias do AKS (serviço kubernetes do Azure) para Azure monitor e definir configurações de diagnóstico para o cluster. 

Use o espaço de trabalho Log Analytics do Azure Monitor para examinar os logs e executar consultas nos dados de log. Azure Monitor logs são habilitados e gerenciados no portal do Azure, ou por meio da CLI, e trabalham com o controle de acesso baseado em função do kubernetes (kubernetes RBAC), o RBAC do Azure e clusters AKS não habilitados para RBAC.

Exiba os logs gerados por AKS Master Components (Kube-apiserver e Kube-controllermanager) para solucionar problemas de seu aplicativo e serviços. Habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros para gerenciamento e monitoramento centralizados de log.

- [Como habilitar e examinar os logs do nó mestre do kubernetes no AKS](/azure/aks/view-master-logs)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Orientação**: Use o AKs (serviço kubernetes do Azure) junto com a central de segurança para obter uma visibilidade mais profunda dos nós do AKS. 

Examine os alertas da central de segurança sobre ameaças e atividades mal-intencionadas detectadas no host e no nível do cluster. A central de segurança implementa a análise contínua de eventos de segurança brutos que ocorrem em um cluster AKS, como dados de rede, criação de processo e o log de auditoria do kubernetes. Determinar se essa atividade é um comportamento esperado ou se o aplicativo está tendo um comportamento inadequado. Use as métricas e os logs em Azure Monitor para obter suas descobertas. 

- [Entender a integração dos serviços Kubernetess do Azure com a central de segurança](../security-center/defender-for-kubernetes-introduction.md)

- [Como habilitar a camada Standard da central de segurança do Azure](../security-center/security-center-get-started.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Orientação**: instalar e habilitar o antimalware da Microsoft para o Azure para máquinas virtuais do AKS (serviço kubernetes) do Azure e para os nós do conjunto de dimensionamento de máquinas virtuais. Examine os alertas na central de segurança para correção.

- [Microsoft Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres-clusters do serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: o serviço de kubernetes do Azure (AKs) usa o projeto CoreDNS para a resolução e o gerenciamento de DNS do cluster.

Habilite o log de consultas DNS aplicando a configuração documentada em seu ConfigMap coredns-personalizado. 

- [Personalizar o CoreDNS com o Serviço de Kubernetes do Azure](coredns-custom.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Orientação**: Use o kubectl, um cliente de linha de comando, no AKs (serviço kubernetes do Azure) para gerenciar um cluster kubernetes e obter seus logs do nó AKs para fins de solução de problemas. O Kubectl já estará instalado se você usar Azure Cloud Shell. Para instalar o kubectl localmente, use o cmdlet ' Install-AzAksKubectl '.

- [Início rápido-implantar um cluster do serviço kubernetes do Azure usando o PowerShell](kubernetes-walkthrough-powershell.md)

- [Obter logs de Kubelet dos nós de cluster do AKS (Serviço de Kubernetes do Azure)](kubelet-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o serviço de kubernetes do Azure (AKs) em si não fornece uma solução de gerenciamento de identidade que armazena contas de usuário e senhas regulares. Com a integração do Azure Active Directory (AD do Azure), você pode conceder aos usuários ou grupos o acesso aos recursos do kubernetes em um namespace ou através do cluster.

Executar consultas ad hoc para descobrir contas que são membros de grupos administrativos do AKS com o módulo do PowerShell do Azure AD

Use CLI do Azure para operações como ' obter credenciais de acesso para um cluster kubernetes gerenciado ' para ajudar a reconciliar o acesso regularmente. Implemente esse processo para manter um inventário atualizado das contas de serviço, que são outro tipo de usuário primário no AKS. Aplique as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Como integrar o AKS ao Azure AD](azure-ad-integration-cli.md)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o serviço de kubernetes do Azure (AKs) não tem o conceito de senhas padrão comuns e não fornece uma solução de gerenciamento de identidades onde contas de usuário e senhas regulares podem ser armazenadas. Com a integração do Azure Active Directory (Azure AD), você pode conceder acesso baseado em função a recursos do AKS em um namespace ou através do cluster. 

Executar consultas ad hoc para descobrir contas que são membros de grupos administrativos do AKS com o módulo do PowerShell do Azure AD

- [Entender as opções de acesso e de identidade para AKS](concepts-identity.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usar contas administrativas dedicadas

**Orientação**: integre a autenticação de usuário para seus clusters do AKS (serviço kubernetes do Azure) com o Azure Active Directory (Azure AD). Entre em um cluster AKS usando um token de autenticação do Azure AD. Configure o controle de acesso baseado em função do kubernetes (kubernetes RBAC) para acesso administrativo às informações e permissões de kubeconfig (configuração kubernetes), namespaces e recursos de cluster. 

Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas. Implemente as recomendações de gerenciamento de acesso e identidade da central de segurança.

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

- [Controlar o acesso aos recursos de cluster](azure-ad-rbac.md)

- [Usar controles de acesso com base em função do Azure](control-kubeconfig-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Orientação**: usar o logon único para o serviço kubernetes do Azure (AKs) com autenticação integrada do Azure Active Directory (AD do Azure) para um cluster AKs.

- [Como exibir logs de kubernetes, eventos e métricas de pod em tempo real](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Orientação**: integre a autenticação do AKS (serviço kubernetes do Azure) com o Azure Active Directory (Azure AD). 

Habilite a autenticação multifator do Azure AD e siga as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use uma Paw (estação de trabalho com acesso privilegiado), com a MFA (autenticação multifator), configurada para fazer logon em seus clusters do AKS (serviço kubernetes do Azure) e recursos relacionados.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA (autenticação multifator) no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) com a autenticação integrada do Azure ad para o serviço kubernetes do Azure (AKs). Os alertas podem ser gerados quando uma atividade suspeita ou não segura ocorre no ambiente. Use a central de segurança para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso aos clusters do AKS (serviço kubernetes do Azure) somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões. Isso requer autenticação integrada para AKS com o Azure Active Directory (Azure AD).

Limite o acesso ao servidor de API AKS de um conjunto limitado de intervalos de endereços IP, pois ele recebe solicitações para executar ações no cluster para criar recursos ou dimensionar o número de nós. 

- [Proteger o acesso ao servidor de API usando intervalos de endereços IP autorizados no serviço de kubernetes do Azure (AKS)](api-server-authorized-ip-ranges.md)

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para o serviço de kubernetes do Azure (AKs). O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e Salts, hashes e armazena com segurança as credenciais do usuário.

Use as funções internas do AKS com o controle de acesso baseado em função do Azure (RBAC do Azure) – colaborador e proprietário da política de recursos para operações de atribuição de política para o cluster kubernetes

- [Visão geral da política do Azure](../governance/policy/overview.md)

- [Como integrar o Azure AD ao AKS](azure-ad-integration-cli.md)

- [Integrar o Azure AD gerenciado pelo AKS](managed-aad.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) com a autenticação integrada do Azure ad para o serviço kubernetes do Azure (AKs). Pesquise logs do Azure AD para ajudar a descobrir contas obsoletas. 

Execute as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. Corrija as recomendações de identidade e acesso da central de segurança.

Lembre-se das funções usadas para fins de suporte ou solução de problemas. Por exemplo, todas as ações de cluster tomadas pelo suporte da Microsoft (com consentimento do usuário) são feitas em uma função interna kubernetes "Edit" do nome AKs-support-rolebinding. O suporte a AKS é habilitado com essa função para editar a configuração e os recursos de cluster para solucionar problemas e diagnosticar os problema de cluster. No entanto, essa função não pode modificar permissões nem criar funções ou associações de função. Esse acesso à função é habilitado apenas em Tíquetes de suporte ativos com acesso JIT (just-in-time).
 
- [Opções de acesso e identidade do Serviço de Kubernetes do Azure (AKS)](concepts-identity.md)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Como monitorar a atividade de acesso e identidade do usuário na central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: integrar a autenticação de usuário para o serviço kubernetes do Azure (AKs) com o Azure Active Directory (Azure AD). Crie configurações de diagnóstico para o Azure AD, enviando os logs de auditoria e de entrada para um espaço de trabalho do Azure Log Analytics. Configure os alertas desejados (por exemplo, quando uma conta desativada tenta fazer logon) em um espaço de trabalho do Azure Log Analytics.
- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como criar, exibir e gerenciar alertas de log usando Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Orientação**: integrar a autenticação de usuário para o serviço kubernetes do Azure (AKs) com o Azure Active Directory (Azure AD). Use as detecções de risco do Azure AD e o recurso de proteção de identidade para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Ingerir dados no Azure Sentinel para investigações adicionais com base nas necessidades de negócios.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Orientação**: use marcas em recursos relacionados a implantações do AKS (serviço kubernetes do Azure) para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Atualizar marcas para clusters gerenciados](/rest/api/aks/managedclusters/updatetags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: Isole logicamente equipes e cargas de trabalho no mesmo cluster com o AKs (serviço kubernetes do Azure) para fornecer o número mínimo de privilégios, com escopo para os recursos exigidos por cada equipe. 

Use o namespace no kubernetes para criar um limite de isolamento lógico. Considere a implementação de recursos de kubernetes adicionais para isolamento e multilocação, como agendamento, rede, autenticação/autorização e contêineres.

Implemente assinaturas e/ou grupos de gerenciamento separados para ambientes de desenvolvimento, teste e produção. Separe os clusters AKS com rede, implantando-os em redes virtuais distintas, que são marcadas adequadamente.

- [Saiba mais sobre as práticas recomendadas para o isolamento de cluster no AKS](operator-best-practices-cluster-isolation.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Entender as práticas recomendadas para conectividade de rede e segurança no AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Use uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências ao alertar os profissionais de segurança de informações.

A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e leva a um grande comprimento para se proteger contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Lista de portas, endereços e nomes de domínio necessários para a funcionalidade AKS](limit-egress-traffic.md)

- [Como definir configurações de diagnóstico para o Firewall do Azure](../firewall/firewall-diagnostics.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: Crie um controlador de entrada HTTPS e use seus próprios certificados TLS (ou, opcionalmente, vamos criptografar) para suas implantações do AKS (serviço kubernetes do Azure). 

O tráfego de saída kubernetes é criptografado via HTTPS/TLS por padrão. Revise qualquer tráfego de saída potencialmente não criptografado de suas instâncias do AKS para monitoramento adicional. Isso pode incluir tráfego de NTP, tráfego de DNS, tráfego HTTP para recuperar atualizações em alguns casos. 

- [Como criar um controlador de entrada HTTPS no AKS e usar seus próprios certificados TLS](ingress-own-tls.md)

- [Como criar um controlador de entrada HTTPS no AKS com vamos criptografar](ingress-tls.md)

- [Lista de portas e protocolos de saída em potencial usados pelo AKS](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.
A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e leva a um grande comprimento para se proteger contra a perda e a exposição de dados do cliente. 

Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Diretrizes**: Use o sistema de autorização do Azure RBAC (controle de acesso baseado em função) criado em Azure Resource Manager para fornecer gerenciamento de acesso refinado de recursos do Azure.

Configure o AKS (serviço kubernetes do Azure) para usar o Azure Active Directory (Azure AD) para autenticação de usuário. Entre em um cluster AKS usando o token de autenticação do Azure AD usando essa configuração. 

Use as funções internas do AKS com o colaborador e o proprietário da política de recursos do RBAC do Azure para operações de atribuição de política para o cluster AKS

- [Como controlar o acesso a recursos de cluster usando o Azure RBAC e identidades do Azure AD no AKS](azure-ad-rbac.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o armazenamento do Azure ou para os recursos de computação. Se necessário, implemente uma solução de terceiros para fins de conformidade.
A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial e leva a um grande comprimento para se proteger contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Orientação**: os dois tipos principais de armazenamento fornecidos para volumes no serviço de kubernetes do Azure (AKs) são apoiados por discos do Azure ou arquivos do Azure. Os dois tipos de armazenamento usam o Azure Criptografia do Serviço de Armazenamento (SSE), que criptografa os dados em repouso para melhorar a segurança. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft.

A criptografia em repouso usando chaves gerenciadas pelo cliente está disponível para criptografar o sistema operacional e os discos de dados em clusters AKS para controle adicional sobre as chaves de criptografia. Os clientes têm a responsabilidade de atividades de gerenciamento de chaves, como o backup e a rotação da chave. No momento, os discos não podem ser criptografados usando a Criptografia de Disco do Azure no nível do nó do AKS.

- [Práticas recomendadas para armazenamento e backups no Serviço de Kubernetes do Azure (AKS)](operator-best-practices-storage.md)

- [Traga suas próprias chaves (BYOK) com discos do Azure no serviço kubernetes do Azure (AKS)](azure-disk-customer-managed-keys.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor para contêineres para monitorar o desempenho de cargas de trabalho de contêiner implantadas em clusters kubernetes gerenciados hospedados no AKs (serviço kubernetes do Azure). 

Configure alertas para notificação proativa ou criação de log quando a utilização de CPU e memória em nós ou contêineres exceder os limites definidos ou quando uma alteração de estado de integridade ocorrer no cluster no ROLLUP de integridade de nós ou infraestrutura. 

Use o log de atividades do Azure para monitorar seus clusters AKS e recursos relacionados em um alto nível. Integre com o Prometheus para exibir as métricas de aplicativo e carga de trabalho coletadas de nós e kubernetes usando consultas para criar alertas personalizados, painéis e executar análise detalhada.

- [Entender Azure Monitor para contêineres](/azure/azure-monitor/insights/container-insights-overview)

- [Como habilitar o Azure Monitor para contêineres](/azure/azure-monitor/insights/container-insights-onboard)

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: Use a central de segurança para monitorar o registro de contêiner do Azure, incluindo instâncias do AKS (serviço kubernetes do Azure) para vulnerabilidades. Habilite o pacote de registros de contêiner na central de segurança para garantir que a central de segurança esteja pronta para verificar as imagens que são enviadas por push ao registro.

Seja notificado no painel da central de segurança quando forem encontrados problemas depois que a central de segurança verificar a imagem usando Qualys. O recurso de pacote de registros de contêiner fornece uma visibilidade mais profunda das vulnerabilidades das imagens usadas em registros baseados em Azure Resource Manager. 

Use a central de segurança para obter recomendações acionáveis para cada vulnerabilidade. Essas recomendações incluem uma classificação de gravidade e diretrizes para correção. 

- [Práticas recomendadas para gerenciamento de imagens de contêiner e a segurança no Serviço de Kubernetes do Azure (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Entender as práticas recomendadas para gerenciamento e segurança de imagens de contêiner no AKS](operator-best-practices-container-image-management.md)

- [Entender a integração do registro de contêiner com a central de segurança do Azure](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: as atualizações de segurança são aplicadas automaticamente a nós do Linux para proteger os clusters do AKS (serviço kubernetes do Azure) do cliente. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. 

Observe que o processo de manutenção dos nós do Windows Server é diferente dos nós que executam o Linux, pois os nós do Windows Server não recebem atualizações diárias. Em vez disso, os clientes precisam executar uma atualização nos pools de nós do Windows Server em seus clusters AKS, que implanta novos nós com a mais recente imagem do servidor da janela base e os patches usando o painel de controle do Azure ou o CLI do Azure. Essas atualizações contêm melhorias de segurança ou funcionalidade para o AKS.

- [Entenda como as atualizações são aplicadas a nós de cluster AKS que executam o Linux](node-updates-kured.md)

- [Como atualizar um pool de nós AKS para clusters AKS que usam nós do Windows Server](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Atualizações de imagem de nó do AKS (serviço kubernetes do Azure)](node-image-upgrade.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar uma solução de gerenciamento de patch automatizada para títulos de software de terceiros

**Diretrizes**: implemente um processo manual para garantir que os aplicativos de terceiros do nó de cluster do AKS (serviço kubernetes) do Azure permaneçam corrigidos durante o tempo de vida do cluster. Isso pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Orientação**: exportar resultados da verificação da central de segurança em intervalos consistentes e comparar os resultados para verificar se as vulnerabilidades foram corrigidas.

Use o cmdlet "Get-AzSecurityTask" do PowerShell para automatizar a recuperação de tarefas de segurança que a central de segurança recomenda que você execute para fortalecer a postura de segurança e as descobertas da verificação de vulnerabilidade.

- [Como usar o PowerShell para exibir as vulnerabilidades descobertas pela central de segurança do Azure](/powershell/module/az.security/get-azsecuritytask)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: Use a classificação de gravidade fornecida pela central de segurança para priorizar a correção de vulnerabilidades. 

Use o CVSS (sistema de Pontuação de vulnerabilidades comuns) (ou outros sistemas de Pontuação fornecidos pela ferramenta de verificação) se estiver usando uma ferramenta de avaliação de vulnerabilidades interna (como Qualys ou Rapid7, oferecida pelo Azure).

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar/descobrir todos os recursos (como computação, armazenamento, rede e assim por diante) em suas assinaturas. Verifique se você tem permissões apropriadas (leitura) em seu locatário e se é capaz de enumerar todas as assinaturas do Azure, bem como recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do grafo de recursos, é altamente recomendável criar e usar recursos baseados em Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure com metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. 

Aplique os seus totais, rótulos ou marcas ao criar um pool de nós do AKS (serviço kubernetes do Azure). Todos os nós dentro desse pool de nós também herdarão o seu não, o rótulo ou a marca.

Os, rótulos ou marcas podem ser usados para reconciliar o inventário regularmente e garantir que os recursos não autorizados sejam excluídos das assinaturas oportunamente.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e marcar marcas de usuário](../azure-resource-manager/management/tag-resources.md)

- [Clusters gerenciados – marcas de atualização](/rest/api/aks/managedclusters/updatetags)

- [Especificar um seu, rótulo ou uma marca para um pool de nós](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: defina uma lista de recursos aprovados do Azure e software aprovado para recursos de computação com base nas necessidades de negócios organizacionais.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
-   Tipos de recursos não permitidos 

-   Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados com base nos requisitos de negócios organizacionais.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: Use os recursos de inventário e controle de alterações de automação do Azure para descobrir o software instalado em seu ambiente. 

Coletar e exibir inventário de software, arquivos, daemons do Linux, serviços do Windows e chaves do registro do Windows em seus computadores e monitorar aplicativos de software não aprovados. 

Acompanhe as configurações de seus computadores para ajudar a identificar problemas operacionais em seu ambiente e compreender melhor o estado de seus computadores.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: Use os recursos de inventário e controle de alterações de automação do Azure para descobrir o software instalado em seu ambiente. 

Coletar e exibir inventário de software, arquivos, daemons do Linux, serviços do Windows e chaves do registro do Windows em seus computadores e monitorar aplicativos de software não aprovados. 

Acompanhe as configurações de seus computadores para ajudar a identificar problemas operacionais em seu ambiente e compreender melhor o estado de seus computadores.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

- [Como usar o monitoramento de integridade de arquivo](../security-center/security-center-file-integrity-monitoring.md)

- [Entender Controle de Alterações do Azure](../automation/change-tracking/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretrizes**: Use os recursos de inventário e controle de alterações de automação do Azure para descobrir o software instalado em seu ambiente. 

Coletar e exibir inventário de software, arquivos, daemons do Linux, serviços do Windows e chaves do registro do Windows em seus computadores e monitorar aplicativos de software não aprovados. 

Acompanhe as configurações de seus computadores para ajudar a identificar problemas operacionais em seu ambiente e compreender melhor o estado de seus computadores.

Habilite a análise de aplicativo adaptável na central de segurança para aplicativos que existem em seu ambiente.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

- [Como usar os controles de aplicativo adaptáveis da central de segurança do Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o grafo de recursos do Azure para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas usando definições de política internas.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".
- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitar a capacidade dos usuários de executar scripts em recursos de computação

**Diretrizes**: o serviço de kubernetes do Azure (AKs) em si não fornece uma solução de gerenciamento de identidades onde contas de usuário e senhas regulares são armazenadas. Em vez disso, use Azure Active Directory (Azure AD) como a solução de identidade integrada para seus clusters AKS.

Conceda aos usuários ou grupos acesso aos recursos do kubernetes em um namespace ou entre o cluster usando a integração do Azure AD.

Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de seus grupos administrativos do AKS e usá-lo para reconciliar o acesso regularmente. Use CLI do Azure para operações como "obter credenciais de acesso para um cluster kubernetes gerenciado. Implemente as recomendações de gerenciamento de acesso e identidade da central de segurança.

- [Gerenciar AKS com CLI do Azure](/cli/azure/aks)

- [Entender a integração do AKS e do Azure AD](concepts-identity.md)

- [Como integrar o AKS ao Azure AD](azure-ad-integration-cli.md)

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorar a identidade e o acesso à central de segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: Use os recursos do AKS (serviço kubernetes do Azure) para isolar logicamente as equipes e as cargas de trabalho no mesmo cluster para o número mínimo de privilégios, com escopo para os recursos exigidos por cada equipe. 

Implemente o namespace no kubernetes para criar um limite de isolamento lógico. Use Azure Policy aliases no namespace "Microsoft. ContainerService" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do AKS (serviço kubernetes do Azure). 

Examine e implemente recursos e considerações adicionais do kubernetes para isolamento e multilocação para incluir o seguinte: agendamento, rede, autenticação/autorização e contêineres. Use também assinaturas e grupos de gerenciamento separados para desenvolvimento, teste e produção. Separar clusters AKS com redes virtuais, sub-redes que são marcadas adequadamente e protegidas por um WAF (firewall do aplicativo Web).

- [Saiba mais sobre as práticas recomendadas para o isolamento de cluster no AKS](operator-best-practices-cluster-isolation.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Entender as práticas recomendadas para conectividade de rede e segurança no AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy no namespace "Microsoft. ContainerService" para criar políticas personalizadas para auditar ou impor a configuração de suas instâncias do AKS (serviço kubernetes do Azure). Use definições de Azure Policy internas.

Exemplos de definições de política internas para AKS incluem:

- Impor a entrada de HTTPS no cluster do Kubernetes

- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes

- O RBAC (controle de acesso baseado em função) deve ser usado nos serviços Kubernetess

- Garantir que haja somente imagens de contêiner permitidas no cluster do Kubernetes

Exporte um modelo de sua configuração do AKS no JavaScript Object Notation (JSON) com Azure Resource Manager. Revise-a periodicamente para garantir que essas configurações atendam aos requisitos de segurança da sua organização. Use as recomendações da central de segurança do Azure como uma linha de base de configuração segura para os recursos do Azure. 

- [Como configurar e gerenciar políticas de segurança de Pod AKS](use-pod-security-policies.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretrizes**: os clusters do AKS (clusters do Azure kubernetes) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas a ela para reduzir a área de ataque da superfície e permite a implantação de contêineres de maneira segura. 

O Azure aplica patches diários (incluindo patches de segurança) a AKS hosts de máquina virtual com alguns patches que exigem uma reinicialização. Os clientes são responsáveis por agendar reinicializações de host de máquina virtual AKS de acordo com a necessidade. 

- [Proteção de segurança para SO host do nó do AKS Agent](security-hardened-vm-host-image.md)

- [Entender a proteção de segurança em hosts de máquina virtual AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretrizes**: Proteja seu cluster AKs (serviço kubernetes do Azure) usando políticas de segurança Pod.  Limite o que o pods pode ser agendado para melhorar a segurança do cluster. 

Pods que solicitam recursos que não são permitidos não podem ser executados no cluster AKS. 

Use também os efeitos Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras para os recursos do Azure relacionados às implantações do AKS (como redes virtuais, sub-redes, firewalls do Azure, contas de armazenamento e assim por diante). 

Crie definições de Azure Policy personalizadas usando aliases dos seguintes namespaces: 

- Microsoft.ContainerService

- Microsoft.Network

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretrizes**: os clusters do AKS (serviço kubernetes do Azure) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas a ela para reduzir a área de ataque da superfície e permite a implantação de contêineres de maneira segura. 

Consulte a lista de controles de CIS (central de segurança da Internet) que são criados no sistema operacional do host.  

- [Proteção de segurança para SO host do nó do AKS Agent](security-hardened-vm-host-image.md)

- [Entender a configuração de estado dos clusters AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Entender a proteção de segurança em hosts de máquina virtual AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use Azure Repos para armazenar e gerenciar suas configurações com segurança se estiver usando definições de Azure Policy personalizadas. Exporte um modelo de sua configuração do AKS (serviço kubernetes do Azure) no JavaScript Object Notation (JSON) com Azure Resource Manager. Examine-a periodicamente para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Implemente soluções de terceiros, como Terraform, para criar um arquivo de configuração que declara os recursos para o cluster kubernetes. Você pode proteger sua implantação do AKS implementando práticas recomendadas de segurança e armazenar sua configuração como código em um local seguro.

- [Definir um cluster do Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Proteção de segurança para SO host do nó do AKS Agent](security-hardened-vm-host-image.md)

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Orientação**: não aplicável ao serviço de kubernetes do Azure (AKs). O AKS fornece um sistema operacional de host otimizado para segurança (SO) por padrão. Não há nenhuma opção atual para selecionar um sistema operacional alternativo ou personalizado.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas usando definições de política internas, bem como aliases de Azure Policy no namespace "Microsoft. ContainerService". 

Crie políticas personalizadas para auditar e impor configurações do sistema. Desenvolver um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: os clusters do AKS (serviço kubernetes do Azure) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas a ela para reduzir a área de ataque da superfície e permite a implantação de contêineres de maneira segura. 

Consulte a lista de controles de CIS (central de segurança da Internet) que são criados em hosts AKS.  

- [Proteção de segurança para SO host do nó do AKS Agent](security-hardened-vm-host-image.md)

- [Entender a proteção de segurança em hosts de máquina virtual AKS](security-hardened-vm-host-image.md)

- [Entender a configuração de estado dos clusters AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança para executar verificações de linha de base para recursos relacionados às implantações do AKS (serviço kubernetes do Azure). Os recursos de exemplos incluem, mas não estão limitados ao próprio cluster AKS, a rede virtual em que o cluster AKS foi implantado, a conta de armazenamento do Azure usada para rastrear o estado Terraform ou Azure Key Vault instâncias que estão sendo usadas para as chaves de criptografia para o sistema operacional e os discos de dados do seu cluster AKS.

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: Use as recomendações de contêiner da central de segurança na &amp; seção "aplicativos de computação" para executar verificações de linha de base para seus clusters do AKS (serviço kubernetes do Azure). 

Seja notificado no painel da central de segurança quando problemas de configuração ou vulnerabilidades forem encontrados. Isso requer a habilitação do grupo de registros de contêiner opcional que permite que a central de segurança Verifique a imagem.  

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: integre Azure Key Vault com um cluster AKs (serviço kubernetes do Azure) usando uma unidade FlexVolume. Use Azure Key Vault para armazenar e girar regularmente segredos, como credenciais, chaves de conta de armazenamento ou certificados. O driver FlexVolume permite que o cluster do AKS nativamente recupere credenciais do Cofre de Chaves e fornecê-las com segurança apenas para o pod solicitante. Use uma identidade gerenciada pod para solicitar acesso a Key Vault e recuperar as credenciais necessárias por meio do driver FlexVolume. Verifique se a exclusão temporária do cofre de chaves está habilitada. 

Limite a exposição da credencial não definindo credenciais no código do aplicativo. 

Evite o uso de credenciais fixas ou compartilhadas. 

- [Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)](concepts-security.md)

- [Como usar Key Vault com o cluster AKS](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: não defina credenciais no código do aplicativo como uma prática recomendada de segurança. Use identidades gerenciadas para recursos do Azure para permitir que um pod se autentique em qualquer serviço no Azure que dê suporte a ele, incluindo Azure Key Vault. O pod é atribuído a uma identidade do Azure para autenticar no Azure Active Directory (Azure AD) e receber um token digital que pode ser apresentado a outros serviços do Azure que verificam se o Pod está autorizado a acessar o serviço e executar as ações necessárias.

Observe que as identidades de Pod gerenciados são destinadas ao uso apenas com imagens de contêiner e pods do Linux. Provisione Azure Key Vault para armazenar e recuperar credenciais e chaves digitais. Chaves como aquelas usadas para criptografar discos do sistema operacional, os dados do cluster AKS podem ser armazenados em Azure Key Vault.

As entidades de serviço também podem ser usadas em clusters AKS. No entanto, os clusters que usam entidades de serviço podem, eventualmente, atingir um estado no qual a entidade de serviço deve ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade, motivo pelo qual é mais fácil usar identidades gerenciadas. Os mesmos requisitos de permissão se aplicam a entidades de serviço e identidades gerenciadas.

- [Entender as identidades gerenciadas e Key Vault com o AKS (serviço kubernetes do Azure)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Identidade do Pod do Azure AD](https://github.com/Azure/aad-pod-identity)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também incentiva a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault com recomendações.

Limite a exposição da credencial não definindo credenciais no código do aplicativo. e evite o uso de credenciais compartilhadas. Azure Key Vault deve ser usado para armazenar e recuperar as credenciais e chaves digitais. Use identidades gerenciadas para recursos do Azure para permitir sua solicitação de acesso de pod para outros recursos. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Práticas recomendadas para desenvolvedores para segurança de Pod](developer-best-practices-pod-security.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: o AKs gerencia o ciclo de vida e as operações de nós de agente em seu nome-a modificação dos recursos de IaaS associados aos nós de agente não é suportada. No entanto, para nós do Linux, você pode usar os conjuntos de daemon para instalar software personalizado, como uma solução antimalware.

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres-clusters do serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS e conjuntos de daemon de responsabilidade compartilhada](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: examine previamente todos os arquivos que estão sendo carregados para os recursos do AKS. Use a detecção de ameaças da central de segurança para serviços de dados para detectar malwares carregados em contas de armazenamento se usar uma conta de armazenamento do Azure como um armazenamento de dados ou para rastrear o estado Terraform para o cluster AKS. 

- [Entender a detecção de ameaças da central de segurança do Azure para serviços de dados](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: o AKs gerencia o ciclo de vida e as operações de nós de agente em seu nome-a modificação dos recursos de IaaS associados aos nós de agente não é suportada. No entanto, para nós do Linux, você pode usar os conjuntos de daemon para instalar software personalizado, como uma solução antimalware.

- [Guia de referência de alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres-clusters do serviço kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [AKS e conjuntos de daemon de responsabilidade compartilhada](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Orientação**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como Velero, que pode fazer backup de volumes persistentes junto com recursos e configurações de cluster adicionais. Periodicamente, verifique a integridade e a segurança desses backups. 

Remova o estado de seus aplicativos antes do backup. Nos casos em que isso não puder ser feito, faça backup dos dados de volumes persistentes e teste regularmente as operações de restauração para verificar a integridade dos dados e os processos necessários.

- [Práticas recomendadas para armazenamento e backups no AKS](operator-best-practices-storage.md)

- [Práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKS](operator-best-practices-multi-region.md)

- [Entender Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Como configurar o Velero no Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como Velero, que pode fazer backup de volumes persistentes junto com recursos e configurações de cluster adicionais. 

Execute backups automatizados regulares de Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com comandos do PowerShell. 

- [Como fazer backup de certificados Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Como fazer backup de chaves de Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Como fazer backup Key Vault contas de armazenamento gerenciadas](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Como fazer backup de Key Vault segredos](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Como habilitar o backup do Azure](/azure/backup/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: executar periodicamente a restauração de dados de conteúdo no backup do Velero. Se necessário, teste a restauração em uma rede virtual isolada.

Execute periodicamente a restauração de dados de Key Vault certificados, chaves, contas de armazenamento gerenciadas e segredos, com comandos do PowerShell.

- [Como restaurar Key Vault certificados](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [Como restaurar chaves de Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Como restaurar Key Vault contas de armazenamento gerenciadas](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar segredos de Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-4.8.0&amp;preserve-view=true)

- [Como recuperar arquivos do backup de máquina virtual do Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como Velero, que pode fazer backup de volumes persistentes junto com recursos e configurações de cluster adicionais. 

Habilite Soft-Delete no Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada se Azure Key Vault estiver sendo usado com para implantações do AKS (serviço kubernetes do Azure).

- [Entender Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md)

- [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guia de tratamento de incidentes de segurança do computador do NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Orientação**: priorize quais alertas devem ser investigados primeiro com a severidade de segurança atribuída à central para alertas. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.
Marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise os planos de resposta a incidentes conforme necessário.

- [Guia para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. 

Examine os incidentes, após o fato, para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exportar alertas e recomendações da central de segurança usando seu recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. 

Escolha o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel, de acordo com a necessidade e com base nos requisitos de negócios organizacionais.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas por meio de "aplicativos lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Informações adicionais sobre a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem gerenciada pela Microsoft, serviços e aplicativos, nos links mencionados.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
