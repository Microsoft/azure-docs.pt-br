---
title: Linha de base de segurança do Azure para o Serviço de Kubernetes do Azure
description: A linha de base de segurança do Serviço de Kubernetes do Azure fornece diretrizes de procedimento e recursos para implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: container-service
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0564f1f39ac9d492dfffdf0e7adacdde08db0874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769581"
---
# <a name="azure-security-baseline-for-azure-kubernetes-service"></a>Linha de base de segurança do Azure para o Serviço de Kubernetes do Azure

Essa linha de base de segurança aplica as diretrizes do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md) ao Kubernetes do Azure. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure. O conteúdo é agrupado pelos **controles de segurança** definidos pelo Azure Security Benchmark e pelas diretrizes relacionadas aplicáveis ao Kubernetes do Azure. Foram excluídos os **controles** que não se aplicam ao Kubernetes do Azure ou cuja responsabilidade é da Microsoft.

Para ver como o Kubernetes do Azure é mapeado por completo para o Azure Security Benchmark, confira o [arquivo de mapeamento completo da linha de base de segurança do Kubernetes do Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Diretrizes**: por padrão, um grupo de segurança de rede e uma tabela de rotas são criados automaticamente com a criação de um cluster do AKS (Serviço de Kubernetes do Microsoft Azure). O AKS modifica automaticamente os grupos de segurança de rede para o fluxo de tráfego apropriado, conforme os serviços são criados com balanceadores de carga, mapeamentos de porta ou rotas de entrada. O grupo de segurança de rede é associado automaticamente às NICs virtuais em nós de cliente e à tabela de rotas com a sub-rede na rede virtual. 

Use as políticas de rede do AKS para limitar o tráfego de rede definindo regras para tráfego de entrada e de saída entre os pods do Linux em um cluster com base na escolha de namespaces e seletores de rótulo. O uso da política de rede exige o plug-in da CNI do Azure com a rede virtual e as sub-redes definidas e só pode ser habilitado na criação do cluster. Elas não podem ser implantadas em um cluster do AKS existente.

Você pode implementar um cluster do AKS particular para garantir que o tráfego de rede entre o servidor de API do AKS e os pools de nós permaneça apenas na rede privada. O painel de controle ou o servidor de API reside em uma assinatura do Azure gerenciada pelo AKS e usa endereços IP internos (RFC1918), enquanto o cluster ou o pool de nós do cliente está na respectiva assinatura. O servidor e o cluster ou o pool de nós podem se comunicar entre si por meio do serviço de Link Privado do Azure na rede virtual do servidor de API e um ponto de extremidade privado que é exposto na sub-rede do cluster do AKS do cliente.  Como alternativa, use um ponto de extremidade público para o servidor de API do AKS, mas restrinja o acesso com o recurso Intervalos de IP Autorizados do servidor de API do AKS. 

- [Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)](concepts-security.md)

- [Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)](use-network-policies.md)

- [Criar um cluster privado do Serviço de Kubernetes do Azure](private-clusters.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem precisar de um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy: Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorar e registrar em log a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: use a Central de Segurança e siga as recomendações de proteção de rede para proteger os recursos de rede usados pelos clusters do AKS (Serviço de Kubernetes do Azure). 

Habilite os logs de fluxo do grupo de segurança de rede e envie-os para uma conta do armazenamento do Azure para auditoria. Envie também os logs de fluxo para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre os padrões de tráfego na sua nuvem do Azure a fim de visualizar a atividade de rede, identificar os pontos de acesso e as ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede.

- [Noções básicas sobre a segurança de rede fornecida pela Central de Segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar os logs de fluxo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: use um WAF (Firewall de Aplicativo Web) habilitado para o Gateway de Aplicativo do Azure na frente de um cluster do AKS para fornecer uma camada adicional de segurança filtrando o tráfego de entrada para seus aplicativos Web. O WAF do Azure usa um conjunto de regras, fornecido pelo OWASP (Open Web Application Security Project), para ataques, como cross-site scripting ou envenenamento de cookie, contra esse tráfego. 

Use um gateway de API para autenticação, autorização, limitação, cache, transformação e monitoramento das APIs usadas no seu ambiente do AKS. Um gateway de API serve como um front door para os microsserviços, separa os clientes deles e diminui a complexidade dos microsserviços removendo a carga de lidar com interesses paralelos.

- [Noções básicas sobre as melhores práticas de conectividade de rede e segurança no AKS](operator-best-practices-network.md)

- [Controlador de entrada do Gateway de Aplicativo ](../application-gateway/ingress-controller-overview.md)

- [Usar o Gerenciamento de API do Azure com microsserviços implantados no Serviço de Kubernetes do Azure](../api-management/api-management-kubernetes.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Diretrizes**: habilite a proteção Standard de DDoS (negação de serviço distribuído) da Microsoft nas redes virtuais em que os componentes do AKS (Serviço de Kubernetes do Azure) são implantados para proteção contra DDoS.

Instale o mecanismo de política de rede e crie políticas de rede do Kubernetes para controlar o fluxo de tráfego entre pods no AKS pois, por padrão, todo o tráfego é permitido entre esses pods. A política de rede só deve ser usada para nós e pods baseados em Linux no AKS. Defina regras que limitam a comunicação entre pods para aprimorar a segurança. 

Você pode optar por permitir ou negar o tráfego de acordo com as configurações, como rótulos atribuídos, namespace ou porta de tráfego. As políticas de rede necessárias podem ser aplicadas automaticamente, pois os pods são criados de modo dinâmico em um cluster do AKS. 

- [Proteger o tráfego entre os pods usando as políticas de rede no Serviço de Kubernetes do Azure (AKS)](use-network-policies.md)

- [Como configurar a Proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1.5: Registrar os pacotes de rede

**Diretrizes**: use a captura de pacotes do Observador de Rede conforme necessário para investigar atividades anormais. 

O Observador de Rede é habilitado automaticamente na região da sua rede virtual quando você cria ou atualiza uma rede virtual na sua assinatura. Você também pode criar instâncias do Observador de Rede usando o PowerShell, a CLI do Azure, a API REST ou o método do Cliente do Azure Resource Manager

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implantar IDS/IPS (sistemas de detecção de intrusões/prevenção de intrusões) baseados em rede

**Diretrizes**: proteja seu cluster do AKS (Serviço de Kubernetes do Azure) com um Gateway de Aplicativo do Azure habilitado com um WAF (Firewall de Aplicativo Web). 

Se a detecção de intrusões e/ou a prevenção contra intrusões baseada na inspeção de conteúdo ou na análise de comportamento não for um requisito, um Gateway de Aplicativo do Azure com o WAF poderá ser usado e configurado no "modo de detecção" para registrar alertas e ameaças ou no "modo de prevenção" para bloquear ativamente as intrusões e os ataques detectados.

- [Noções básicas sobre as melhores práticas de proteção do cluster do AKS com um WAF](https://docs.microsoft.com/azure/aks/operator-best-practices-network#secure-traffic-with-a-web-application-firewall-waf)

- [Como implantar o WAF do Azure (Gateway de Aplicativo do Azure)](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede associados às instâncias do AKS (Serviço de Kubernetes do Azure). As marcas de serviço podem ser usadas no lugar de endereços IP específicos ao criar regras de segurança para permitir ou negar o tráfego para o serviço correspondente, especificando o nome da marca de serviço. 

A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Aplique uma marca do Azure aos pools de nós no seu cluster do AKS. Elas são diferentes das marcas de serviço de rede virtual e são aplicadas a cada nó do pool de nós e persistem nas atualizações. 

- [Noções básicas e uso das marcas de serviço](../virtual-network/service-tags-overview.md)

- [Noções básicas sobre NSGs para o AKS](support-policies.md)

- [Controlar o tráfego de saída dos nós de cluster no Serviço de Kubernetes do Azure (AKS)](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão com o Azure Policy para recursos de rede associados aos seus clusters do AKS (Serviço de Kubernetes do Azure). 

Use aliases do Azure Policy nos namespaces "Microsoft.ContainerService" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede da clusters do AKS. 

Além disso, use definições de política internas relacionadas ao AKS, como:

- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes

- Impor a entrada de HTTPS no cluster do Kubernetes

- Garantir que os serviços escutem somente em portas permitidas no cluster do Kubernetes

Há informações adicionais disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de rede do Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretrizes**: use marcas para grupos de segurança de rede e outros recursos para o fluxo de tráfego bidirecional nos clusters do AKS (Serviço de Kubernetes do Azure). Use o campo "Descrição" para regras de grupo de segurança de rede individuais a fim de especificar a necessidade empresarial e/ou a duração etc. para qualquer regra que permita o tráfego em uma rede.

Use uma das definições internas do Azure Policy relacionadas à marcação, por exemplo, "Exigir a marca e o respectivo valor" para garantir que todos os recursos sejam criados com marcas e receber notificações de recursos não marcados existentes.

Opte por permitir ou negar caminhos de rede específicos no cluster, baseado em namespaces e seletores de rótulo com as políticas de rede. Use esses namespaces e rótulos como descritores para regras de configuração de tráfego. Use o Azure PowerShell ou a CLI (interface de linha de comando) do Azure para pesquisar ou executar ações em recursos com base nas respectivas marcas.

- [Azure Policy com a CLI](/cli/azure/policy)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretrizes**: use o Log de Atividades do Azure para monitorar configurações de recursos de rede e detectar alterações de recursos de rede relacionados aos clusters do AKS (Serviço de Kubernetes do Azure). 

Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos. Todas as entradas do usuário AzureContainerService nos logs de atividades são registradas como ações da plataforma. 

Use os logs do Azure Monitor para habilitar e consultar os logs em componentes mestre, kube-apiserver e kube-controller-manager do AKS. Crie e gerencie os nós que executam o kubelet e o runtime do contêiner e implemente os aplicativos dele por meio do servidor de API do Kubernetes gerenciado. 

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

- [Habilitar e examinar os logs do nó mestre do Kubernetes no AKS (Serviço de Kubernetes do Azure)](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, confira [Azure Security Benchmark: log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usar fontes de sincronização de tempo aprovadas

**Diretrizes**: os nós do AKS (Serviço de Kubernetes do Azure) usam ntp.ubuntu.com para sincronização de hora, com a porta UDP 123 e o protocolo NTP. 

Verifique se os servidores NTP podem ser acessados pelos nós de cluster, caso servidores DNS personalizados estejam sendo usados. 

- [Noções básicas sobre os requisitos de domínio e porta do NTP para nós de cluster do AKS](limit-egress-traffic.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretrizes**: habilite os logs de auditoria em componentes mestre kube-apiserver e kube-controller-manager do AKS (Serviços de Kubernetes do Azure), que são fornecidos como um serviço gerenciado. 

- kube-auditaksService: o nome de exibição no log de auditoria para a operação de painel de controle (por meio do hcpService) 

- masterclient: o nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido com az aks get-credentials 

- nodeclient: o nome de exibição para ClientCertificate, que é usado pelos nós de agente

Habilite outros logs de auditoria, como kube-audit também. 

Exporte esses logs para o Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, use workspaces do Log Analytics para consultar e executar análises e use contas do Armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Habilite e integre esses dados ao Azure Sentinel ou a um SIEM de terceiros de acordo com seus requisitos de negócios organizacionais.

- [Examine o esquema de log, incluindo as funções de log aqui](/azure/aks/view-master-logs)

- [Noções básicas sobre o Azure Monitor para Contêineres](/azure/azure-monitor/insights/container-insights-overview)

- [Como habilitar o Azure Monitor para Contêineres](/azure/azure-monitor/insights/container-insights-onboard)

- [Habilitar e examinar os logs do nó mestre do Kubernetes no AKS (Serviço de Kubernetes do Azure)](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: use os logs de atividades para monitorar ações em recursos do AKS (Serviço de Kubernetes do Azure) e ver todas as atividades e o respectivo status. Determine quais operações foram executadas nos recursos na sua assinatura com os logs de atividades: 

- quem iniciou a operação
- quando a operação ocorreu
- o status da operação
- os valores de outras propriedades que podem ajudar você a pesquisar a operação

Recupere informações do log de atividades por meio do Azure PowerShell, da CLI (interface de linha de comando) do Azure, da API REST do Azure ou do portal do Azure. 

Habilite os logs de auditoria em componentes mestre do AKS, como: 

- kube-auditaksService: o nome de exibição no log de auditoria para a operação de painel de controle (por meio do hcpService) 

- masterclient: o nome de exibição no log de auditoria para MasterClientCertificate, o certificado obtido com az aks get-credentials 

- nodeclient: o nome de exibição para ClientCertificate, que é usado pelos nós de agente

Ative outros logs de auditoria, como kube-audit também. 

- [Como habilitar e examinar os logs do nó mestre do Kubernetes no AKS](/azure/aks/view-master-logs)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Coletar logs de segurança de sistemas operacionais

**Diretrizes**: habilite a instalação automática de agentes do Log Analytics para coletar dados dos nós de cluster do AKS. Além disso, ative o provisionamento automático do Agente de Monitoramento do Log Analytics do Azure na Central de Segurança do Azure, pois, por padrão, o provisionamento automático está desativado. O agente também pode ser instalado manualmente. Com o provisionamento automático ativado, a Central de Segurança implantará o agente do Log Analytics em todas as VMs do Azure compatíveis, bem como naquelas que forem criadas. A Central de Segurança coleta dados de VMs (Máquinas Virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais e contêineres de IaaS, como nós de cluster do Kubernetes, para monitorar vulnerabilidades e ameaças à segurança. Os dados são coletados por meio do agente do Azure Log Analytics, que lê uma variedade de configurações e logs de eventos relacionados à segurança do computador e copia os dados para o seu workspace para análise. 

A coleta de dados é necessária para dar visibilidade a atualizações ausentes, configurações incorretas de segurança do sistema operacional, status de proteção do ponto de extremidade, bem como detecções de ameaças e integridade.

- [Como habilitar o provisionamento automático do agente do Log Analytics](../security-center/security-center-enable-data-collection.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretrizes**: integre suas instâncias do AKS (Serviço de Kubernetes do Azure) ao Azure Monitor e defina o período de retenção do workspace do Azure Log Analytics correspondente de acordo com os requisitos de conformidade da sua organização. 

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Diretrizes**: integre suas instâncias do AKS (Serviço de Kubernetes do Azure) ao Azure Monitor e defina configurações de diagnóstico para o cluster. 

Use o workspace do Log Analytics do Azure Monitor para examinar os logs e realizar consultas nos dados de log. Os logs do Azure Monitor são habilitados e gerenciados no portal do Azure ou por meio da CLI e funcionam com o RBAC (controle de acesso baseado em função) do Kubernetes, o RBAC do Azure e os clusters do AKS não habilitados para RBAC.

Veja os logs gerados pelos componentes mestre (kube-apiserver e kube-controllermanager) do AKS para solucionar problemas do aplicativo e dos serviços. Habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros para gerenciamento e monitoramento centralizados de log.

- [Como habilitar e examinar os logs do nó mestre do Kubernetes no AKS](/azure/aks/view-master-logs)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitar alertas sobre atividades anormais

**Diretrizes**: use o AKS (Serviço de Kubernetes do Azure) com a Central de Segurança para obter uma visibilidade mais profunda dos nós do AKS. 

Examine os alertas da Central de Segurança sobre ameaças e atividades mal-intencionadas detectadas no nível do host e do cluster. A Central de Segurança implementa a análise contínua de eventos de segurança brutos que ocorrem em um cluster do AKS, como os dados de rede, a criação de processo e o log de auditoria do Kubernetes. Determinar se essa atividade é um comportamento esperado ou se o aplicativo está tendo um comportamento inadequado. Use métricas e logs no Azure Monitor para substanciar suas conclusões. 

- [Noções básicas sobre a integração do Serviço de Kubernetes do Azure com a Central de Segurança](../security-center/defender-for-kubernetes-introduction.md)

- [Como habilitar a camada Standard da Central de Segurança do Azure](../security-center/security-center-get-started.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: instale e habilite o Microsoft Antimalware para Azure para máquinas virtuais do AKS (Serviço de Kubernetes do Azure) e os nós do conjunto de dimensionamento de máquinas virtuais. Examine os alertas na Central de Segurança para correção.

- [Microsoft Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](../security/fundamentals/antimalware.md)

- [Guia de referência dos alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres – clusters do Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="29-enable-dns-query-logging"></a>2.9: habilitar o registro em log de consulta DNS

**Diretrizes**: o AKS (Serviço de Kubernetes do Azure) usa o projeto CoreDNS para a resolução e o gerenciamento de DNS do cluster.

Habilite o log de consultas DNS aplicando a configuração documentada no ConfigMap personalizado pelo CoreDNS. 

- [Personalizar o CoreDNS com o Serviço de Kubernetes do Azure](coredns-custom.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="210-enable-command-line-audit-logging"></a>2.10: habilitar o registro em log de auditoria de linha de comando

**Diretrizes**: use o kubectl, um cliente de linha de comando, no AKS (Serviço de Kubernetes do Azure) para gerenciar um cluster do Kubernetes e obter os logs do nó do AKS para fins de solução de problemas. O kubectl já está instalado se você usa o Azure Cloud Shell. Para instalar o kubectl localmente, use o cmdlet 'Install-AzAksKubectl'.

- [Guia de Início Rápido – Implantar um cluster do Serviço de Kubernetes do Azure usando o PowerShell](kubernetes-walkthrough-powershell.md)

- [Obter logs de Kubelet dos nós de cluster do AKS (Serviço de Kubernetes do Azure)](kubelet-logs.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, confira [Azure Security Benchmark: controle de acesso e identidade](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretrizes**: o AKS (Serviço de Kubernetes do Azure) em si não fornece uma solução de gerenciamento de identidades que armazena contas de usuário e senhas normais. Com a integração ao Azure AD (Azure Active Directory), você pode permitir a usuários ou grupos o acesso aos recursos do Kubernetes em um namespace ou em todo o cluster.

Executar consultas ad hoc para descobrir contas que são membros de grupos administrativos do AKS com o módulo do PowerShell do Azure AD

Use a CLI do Azure para operações como 'Obter credenciais de acesso para um cluster do Kubernetes gerenciado' a fim de auxiliar a reconciliar o acesso regularmente. Implemente esse processo para manter um inventário atualizado das contas de serviço, que são outro tipo de usuário primário no AKS. Imponha as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança.

- [Como integrar o AKS ao Azure AD](azure-ad-integration-cli.md)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorar a identidade e o acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: o AKS (Serviço de Kubernetes do Azure) não tem o conceito de senhas padrão comuns e não fornece uma solução de gerenciamento de identidades em que contas de usuário e senhas normais possam ser armazenadas. Com a integração do Azure AD (Azure Active Directory), você pode permitir o acesso baseado em função aos recursos do AKS em um namespace ou em todo o cluster. 

Executar consultas ad hoc para descobrir contas que são membros de grupos administrativos do AKS com o módulo do PowerShell do Azure AD

- [Noções básicas sobre as opções de acesso e de identidade do AKS](concepts-identity.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretrizes**: integre a autenticação de usuário aos seus clusters do AKS (Serviço de Kubernetes do Azure) com o Azure AD (Azure Active Directory). Entre em um cluster do AKS usando um token de autenticação do Azure AD. Configure o RBAC (controle de acesso baseado em função) do Kubernetes para acesso administrativo a informações e permissões, namespaces e recursos de cluster do kubeconfig (configuração do Kubernetes). 

Crie políticas e procedimentos em relação ao uso de contas administrativas dedicadas. Implemente as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança.

- [Como monitorar a identidade e o acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

- [Controlar o acesso aos recursos de cluster](azure-ad-rbac.md)

- [Usar controles de acesso baseado em função do Azure](control-kubeconfig-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usar o SSO (logon único) com o Azure Active Directory

**Diretrizes**: use o logon único do AKS (Serviço de Kubernetes do Azure) com a autenticação integrada do Azure AD (Azure Active Directory) para um cluster do AKS.

- [Como ver logs do Kubernetes, eventos e métricas de pod em tempo real](/azure/azure-monitor/insights/container-insights-livedata-overview)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: integre a autenticação do AKS (Serviço de Kubernetes do Azure) ao Azure AD (Azure Active Directory). 

Habilite a Autenticação Multifator do Azure AD e siga as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: use uma PAW (estação de trabalho com acesso privilegiado) com a MFA (Autenticação Multifator) configurada para fazer logon nos seus clusters do AKS (Serviço de Kubernetes do Azure) e recursos relacionados.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a MFA (Autenticação Multifator) no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Registrar atividades suspeitas em contas administrativas e alertar sobre elas

**Diretrizes**: use os relatórios de segurança do Azure AD (Azure Active Directory) com a autenticação integrada do Azure AD para o AKS (Serviço de Kubernetes do Azure). Os alertas podem ser gerados quando uma atividade suspeita ou não segura ocorre no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Gerenciar os recursos do Azure somente em localizações aprovadas

**Diretrizes**: use as localizações nomeadas de acesso condicional para permitir o acesso aos clusters do AKS (Serviço de Kubernetes do Azure) somente em agrupamentos lógicos de intervalos de endereços IP ou regiões/países específicos. Isso exige a autenticação integrada do AKS no Azure AD (Azure Active Directory).

Limite o acesso ao servidor de API do AKS de um conjunto limitado de intervalos de endereços IP, conforme ele recebe solicitações para executar ações no cluster para criar recursos ou escalar o número de nós. 

- [Proteger o acesso ao servidor de API usando intervalos de endereços IP autorizados no AKS (Serviço de Kubernetes do Azure)](api-server-authorized-ip-ranges.md)

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: use o Azure AD (Azure Active Directory) como o sistema de autenticação e autorização central do AKS (Serviço de Kubernetes do Azure). O Azure AD protege os dados usando uma criptografia forte para sal, hashes e dados inativos e em trânsito e armazena com segurança as credenciais do usuário.

Use as funções internas do AKS, Colaborador e Proprietário da Política de Recursos, com o RBAC do Azure (controle de acesso baseado em função do Azure) para operações de atribuição de política no cluster do Kubernetes

- [Visão geral da política do Azure](../governance/policy/overview.md)

- [Como integrar o Azure AD ao AKS](azure-ad-integration-cli.md)

- [Integrar o Azure AD gerenciado pelo AKS](managed-aad.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: use os relatórios de segurança do Azure AD (Azure Active Directory) com a autenticação integrada do Azure AD para o AKS (Serviço de Kubernetes do Azure). Pesquise os logs do Azure AD para ajudar a descobrir contas obsoletas. 

Execute revisões de acesso de identidade do Azure para gerenciar com eficiência associações a um grupo, acesso aos aplicativos empresariais e atribuições de função. Corrija as recomendações de identidade e acesso da Central de Segurança.

Lembre-se das funções usadas para fins de suporte ou solução de problemas. Por exemplo, todas as ações de cluster executadas pelo suporte da Microsoft (com o consentimento do usuário) são feitas em uma função interna "Editar" do Kubernetes do nome aks-support-rolebinding. O suporte ao AKS é habilitado com essa função para editar a configuração e os recursos de cluster a fim de diagnosticar e solucionar problemas de cluster. No entanto, essa função não pode modificar permissões nem criar funções ou associações de função. O acesso à essa função é habilitado apenas em tíquetes de suporte ativos com acesso JIT (Just-In-Time).
 
- [Opções de acesso e identidade do Serviço de Kubernetes do Azure (AKS)](concepts-identity.md)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Monitorar as tentativas de acessar credenciais desativadas

**Diretrizes**: integre a autenticação de usuário do AKS (Serviço de Kubernetes do Azure) ao Azure AD (Azure Active Directory). Crie configurações de diagnóstico para o Azure AD, enviando os logs de auditoria e de entrada para um workspace do Azure Log Analytics. Configure os alertas desejados (por exemplo, quando uma conta desativada tenta fazer logon) em um workspace do Azure Log Analytics.
- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como criar, ver e gerenciar alertas de log usando o Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: integre a autenticação de usuário do AKS (Serviço de Kubernetes do Azure) ao Azure AD (Azure Active Directory). Use o recurso de proteção de identidade e detecção de risco do Azure AD para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades do usuário. Ingira dados no Azure Sentinel para investigações adicionais com base nas necessidades empresariais.

- [Como exibir entradas suspeitas do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretrizes**: use marcas em recursos relacionados a implantações do AKS (Serviço de Kubernetes do Azure) para auxiliar no acompanhamento de recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Atualizar marcas para clusters gerenciados](/rest/api/aks/managedclusters/updatetags)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: isole logicamente equipes e cargas de trabalho no mesmo cluster com o AKS (Serviço de Kubernetes do Azure) para fornecer o número mínimo de privilégios, com escopo para os recursos exigidos por equipe. 

Use o namespace no Kubernetes para criar um limite de isolamento lógico. Considere a implementação de recursos adicionais do Kubernetes para isolamento e multilocação, como agendamento, rede, autenticação/autorização e contêineres.

Implemente assinaturas e/ou grupos de gerenciamento separados para ambientes de desenvolvimento, teste e produção. Separe os clusters do AKS com rede, implantando-os em redes virtuais distintas, que sejam marcadas adequadamente.

- [Saiba mais sobre as melhores práticas de isolamento de cluster no AKS](operator-best-practices-cluster-isolation.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Noções básicas sobre as melhores práticas de conectividade de rede e segurança no AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Diretrizes**: use uma solução de terceiros do Azure Marketplace em perímetros de rede que monitora a transferência não autorizada de informações confidenciais e bloqueia essas transferências alertando os profissionais de segurança da informação.

A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial, além de fazer todo o esforço possível para fornecer proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Lista de portas, endereços e nomes de domínio necessários para a funcionalidade do AKS](limit-egress-traffic.md)

- [Como definir as configurações de diagnóstico do Firewall do Azure](../firewall/firewall-diagnostics.md)

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: crie um controlador de entrada HTTPS e use certificados TLS próprios (ou, opcionalmente, o Let's Encrypt) para suas implantações do AKS (Serviço de Kubernetes do Azure). 

O tráfego de saída do Kubernetes é criptografado via HTTPS/TLS por padrão. Examine qualquer tráfego de saída potencialmente não criptografado das suas instâncias do AKS para monitoramento adicional. Em alguns casos, isso pode incluir o tráfego de NTP, DNS e HTTP para recuperar atualizações. 

- [Como criar um controlador de entrada HTTPS no AKS e usar certificados TLS próprios](ingress-own-tls.md)

- [Como criar um controlador de entrada HTTPS no AKS com o Let's Encrypt](ingress-tls.md)

- [Lista de portas e protocolos de saída potenciais usados pelo AKS](limit-egress-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Diretrizes**: os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis para os recursos de computação nem para o Armazenamento do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.
A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial, além de fazer todo o esforço possível para fornecer proteção contra perda e exposição de dados do cliente. 

Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Usar o RBAC do Azure para gerenciar o acesso a recursos

**Diretrizes**: use o sistema de autorização do RBAC do Azure (controle de acesso baseado em função do Azure) baseado no Azure Resource Manager para fornecer gerenciamento de acesso refinado dos recursos no Azure.

Configure o AKS (Serviço de Kubernetes do Azure) para usar o Azure AD (Azure Active Directory) para autenticação do usuário. Entre em um cluster do AKS usando o token de autenticação do Azure AD com essa configuração. 

Use as funções internas do AKS, Colaborador e Proprietário da Política de Recursos, com o RBAC do Azure para operações de atribuição de política no cluster do AKS

- [Como controlar o acesso a recursos de cluster usando o RBAC do Azure e as identidades do Azure AD no AKS](azure-ad-rbac.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem precisar de um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy: Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 4.6](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-4-6.md)]

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: os recursos de identificação, classificação e prevenção contra perda de dados ainda não estão disponíveis para os recursos de computação nem para o Armazenamento do Azure. Se necessário, implemente uma solução de terceiros para fins de conformidade.
A Microsoft gerencia a plataforma subjacente e trata todo o conteúdo do cliente como confidencial, além de fazer todo o esforço possível para fornecer proteção contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografar informações confidenciais em repouso

**Diretrizes**: os dois principais tipos de armazenamento fornecidos para volumes no AKS (Serviço de Kubernetes do Azure) são apoiados por Discos do Azure ou Arquivos do Azure. Os dois tipos de armazenamento usam a SSE (Criptografia do Serviço de Armazenamento) do Azure, que criptografa os dados inativos para aprimorar a segurança. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft.

A criptografia em repouso com chaves gerenciadas pelo cliente está disponível para criptografar os discos de SO e de dados em clusters do AKS a fim de proporcionar controle adicional sobre as chaves de criptografia. Os clientes têm a responsabilidade pelas atividades de gerenciamento de chaves, como backup e rotação das chaves. No momento, os discos não podem ser criptografados usando a Criptografia de Disco do Azure no nível do nó do AKS.

- [Práticas recomendadas para armazenamento e backups no Serviço de Kubernetes do Azure (AKS)](operator-best-practices-storage.md)

- [BYOK (Bring Your Own Key) com discos do Azure no AKS (Serviço de Kubernetes do Azure)](azure-disk-customer-managed-keys.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: use o Azure Monitor para Contêineres a fim de monitorar o desempenho de cargas de trabalho de contêiner implantadas em clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure). 

Configure alertas para notificação proativa ou criação de log quando a memória e a utilização da CPU em nós ou contêineres exceder os limites definidos ou quando uma alteração de estado de integridade ocorrer no cluster no rollup de integridade de nós ou na infraestrutura. 

Use o log de atividades do Azure para monitorar seus clusters do AKS e os recursos relacionados em um alto nível. Faça a integração ao Prometheus para ver as métricas de aplicativo e carga de trabalho coletadas dos nós e do Kubernetes usando consultas para criar alertas personalizados, painéis e executar uma análise detalhada.

- [Noções básicas sobre o Azure Monitor para Contêineres](/azure/azure-monitor/insights/container-insights-overview)

- [Como habilitar o Azure Monitor para Contêineres](/azure/azure-monitor/insights/container-insights-onboard)

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: use a Central de Segurança para monitorar o Registro de Contêiner do Azure, incluindo as instâncias do AKS (Serviço de Kubernetes do Azure), em busca de vulnerabilidades. Habilite o pacote de Registros de Contêiner na Central de Segurança para garantir que a Central de Segurança esteja pronta para verificar as imagens enviadas por push ao Registro.

Receba notificações no painel da Central de Segurança quando forem encontrados problemas depois que ela verificar a imagem usando o Qualys. O recurso de pacote de Registros de Contêiner fornece uma visibilidade mais profunda das vulnerabilidades das imagens usadas em registros baseados no Azure Resource Manager. 

Use a Central de Segurança para obter recomendações acionáveis para cada vulnerabilidade. Essas recomendações incluem uma classificação de severidade e diretrizes para correção. 

- [Práticas recomendadas para gerenciamento de imagens de contêiner e a segurança no Serviço de Kubernetes do Azure (AKS)](../security-center/defender-for-container-registries-introduction.md)

- [Noções básicas sobre as melhores práticas de gerenciamento e segurança de imagens de contêiner no AKS](operator-best-practices-container-image-management.md)

- [Noções básicas sobre a integração do registro de contêiner à Central de Segurança do Azure](../security-center/defender-for-container-registries-introduction.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretrizes**: as atualizações de segurança são aplicadas automaticamente aos nós do Linux para proteger os clusters do AKS (Serviço de Kubernetes do Azure) do cliente. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. 

Observe que o processo de manter atualizados os nós do Windows Server é diferente dos nós que executam o Linux, pois os nós do Windows Server não recebem atualizações diárias. Em vez disso, os clientes precisam executar uma atualização nos pools de nós do Windows Server nos clusters do AKS, que implanta novos nós com a imagem base e os patches mais recentes do Windows Server usando o painel de controle do Azure ou a CLI do Azure. Essas atualizações contêm aprimoramentos de segurança ou funcionalidade para o AKS.

- [Noções básicas de aplicação das atualizações aos nós de cluster do AKS que executam o Linux](node-updates-kured.md)

- [Como atualizar um pool de nós do AKS para clusters do AKS que usam nós do Windows Server](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#upgrade-a-node-pool)

- [Atualizações de imagens de nós do AKS (Serviço de Kubernetes do Azure)](node-image-upgrade.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implantar uma solução automatizada de gerenciamento de patch para títulos de software de terceiros

**Diretrizes**: implemente um processo manual para garantir que os aplicativos de terceiros do nó de cluster do AKS (Serviço de Kubernetes do Azure) permaneçam corrigidos durante o tempo de vida do cluster. Isso pode exigir a habilitação de atualizações automáticas, o monitoramento dos nós ou a realização de reinicializações periódicas.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: o [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão e é a base para as [recomendações da Central de Segurança](/azure/security-center/security-center-recommendations). As definições do Azure Policy relacionadas a esse controle são habilitadas automaticamente pela Central de Segurança. Os alertas relacionados a esse controle podem precisar de um plano do [Azure Defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Definições internas do Azure Policy: Microsoft.ContainerService**:

[!INCLUDE [Resource Policy for Microsoft.ContainerService 5.3](../../includes/policy/standards/asb/rp-controls/microsoft.containerservice-5-3.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretrizes**: exporte os resultados da verificação da Central de Segurança em intervalos consistentes e compare-os para verificar se as vulnerabilidades foram corrigidas.

Use o cmdlet "Get-AzSecurityTask" do PowerShell para automatizar a recuperação de tarefas de segurança recomendadas pela Central de Segurança para fortalecer sua postura de segurança e as conclusões da verificação de vulnerabilidades.

- [Como usar o PowerShell para ver as vulnerabilidades descobertas pela Central de Segurança do Azure](/powershell/module/az.security/get-azsecuritytask)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: use a classificação de severidade fornecida pela Central de Segurança para priorizar a correção de vulnerabilidades. 

Use o CVSS (Common Vulnerability Scoring System) ou outros sistemas de pontuação fornecidos pela ferramenta de verificação se estiver usando uma ferramenta interna de avaliação de vulnerabilidades (como o Qualys ou o Rapid7, oferecidos pelo Azure).

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, confira [Azure Security Benchmark: gerenciamento de inventário e ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Usar uma solução de descoberta de ativos automatizada

**Diretrizes**: use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede etc.) nas suas assinaturas. Verifique se você tem permissões (de leitura) apropriadas no seu locatário e se pode enumerar todas as assinaturas do Azure, bem como os recursos nas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos baseados no Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure com metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretrizes**: use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e acompanhar ativos. 

Aplique taints, rótulos ou marcas ao criar um pool de nós do AKS (Serviço de Kubernetes do Azure). Todos os nós desse pool de nós também herdarão o taint, o rótulo ou a marca.

Os taints, os rótulos ou as marcas podem ser usados para reconciliar o inventário regularmente e garantir que os recursos não autorizados sejam excluídos das assinaturas em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Clusters gerenciados – Atualizar marcas](/rest/api/aks/managedclusters/updatetags)

- [Especificar um taint, um rótulo ou uma marca para um pool de nós](https://docs.microsoft.com/azure/aks/use-multiple-node-pools#specify-a-taint-label-or-tag-for-a-node-pool)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: defina uma lista de recursos aprovados do Azure e programas de software aprovados para recursos de computação de acordo com as necessidades empresariais da organização.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
-   Tipos de recursos não permitidos 

-   Tipos de recursos permitidos

Use o Azure Resource Graph para consultar/descobrir recursos nas suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados de acordo com os requisitos empresariais organizacionais.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: use os recursos de Controle de Alterações e Inventário da Automação do Azure para descobrir os programas de software instalados no seu ambiente. 

Colete e veja o inventário de programas de software, arquivos, daemons do Linux, serviços Windows e chaves do Registro do Windows nos seus computadores e monitore os aplicativos de software não aprovados. 

Acompanhe as configurações dos computadores para auxiliar na detecção de problemas operacionais no seu ambiente e entender melhor o estado dos computadores.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretrizes**: use os recursos de Controle de Alterações e Inventário da Automação do Azure para descobrir os programas de software instalados no seu ambiente. 

Colete e veja o inventário de programas de software, arquivos, daemons do Linux, serviços Windows e chaves do Registro do Windows nos seus computadores e monitore os aplicativos de software não aprovados. 

Acompanhe as configurações dos computadores para auxiliar na detecção de problemas operacionais no seu ambiente e entender melhor o estado dos computadores.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

- [Como usar o Monitoramento de Integridade do Arquivo](../security-center/security-center-file-integrity-monitoring.md)

- [Noções básicas sobre o Controle de Alterações do Azure](../automation/change-tracking/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: Usar somente aplicativos aprovados

**Diretrizes**: use os recursos de Controle de Alterações e Inventário da Automação do Azure para descobrir os programas de software instalados no seu ambiente. 

Colete e veja o inventário de programas de software, arquivos, daemons do Linux, serviços Windows e chaves do Registro do Windows nos seus computadores e monitore os aplicativos de software não aprovados. 

Acompanhe as configurações dos computadores para auxiliar na detecção de problemas operacionais no seu ambiente e entender melhor o estado dos computadores.

Habilite a análise de aplicativo adaptável na Central de Segurança para os aplicativos que existem no seu ambiente.

- [Como habilitar o inventário de máquina virtual do Azure](../automation/automation-tutorial-installed-software.md)

- [Como usar os controles de aplicativos adaptáveis da Central de Segurança do Azure](../security-center/security-center-adaptive-application.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use o Azure Resource Graph para consultar/descobrir recursos nas suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de nomes de software aprovados

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados nas suas assinaturas usando definições de política interna.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitar a capacidade de interação dos usuários com o Azure Resource Manager

**Diretrizes**: Use o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de "Gerenciamento do Microsoft Azure".
- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade de os usuários executar scripts nos recursos de computação

**Diretrizes**: o AKS (Serviço de Kubernetes do Azure) em si não oferece uma solução de gerenciamento de identidades em que contas de usuário e senhas normais são armazenadas. Em vez disso, use o Azure AD (Azure Active Directory) como a solução de identidade integrada para seus clusters do AKS.

Permita a usuários ou grupos o acesso aos recursos do Kubernetes em um namespace ou em todo o cluster usando a integração do Azure AD.

Use o módulo do PowerShell do Azure AD para executar consultas ad hoc e descobrir contas que sejam membros dos seus grupos administrativos do AKS e use-o para reconciliar o acesso regularmente. Use a CLI do Azure para operações como ‘Obter credenciais de acesso para um cluster do Kubernetes gerenciado’. Implemente as recomendações do Gerenciamento de Identidades e Acesso da Central de Segurança.

- [Gerenciar o AKS com a CLI do Azure](/cli/azure/aks)

- [Noções básicas sobre a integração do AKS e do Azure AD](concepts-identity.md)

- [Como integrar o AKS ao Azure AD](azure-ad-integration-cli.md)

- [Como obter uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função do diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Como monitorar a identidade e o acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: use os recursos do AKS (Serviço de Kubernetes do Azure) para isolar logicamente as equipes e as cargas de trabalho no mesmo cluster para fornecer o número mínimo de privilégios, com escopo para os recursos exigidos por equipe. 

Implemente o namespace no Kubernetes para criar um limite de isolamento lógico. Use aliases do Azure Policy no namespace "Microsoft.ContainerService" para criar políticas personalizadas a fim de auditar ou impor a configuração das instâncias do AKS (Serviço de Kubernetes do Azure). 

Examine e implemente recursos e considerações adicionais do Kubernetes para isolamento e multilocação a fim de incluir o seguinte: agendamento, rede, autenticação/autorização e contêineres. Use também assinaturas e grupos de gerenciamento separados para desenvolvimento, teste e produção. Separe clusters do AKS com redes virtuais, sub-redes que são marcadas adequadamente e protegidas por um WAF (Firewall de Aplicativo Web).

- [Saiba mais sobre as melhores práticas de isolamento de cluster no AKS](operator-best-practices-cluster-isolation.md)

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Noções básicas sobre as melhores práticas de conectividade de rede e segurança no AKS](operator-best-practices-network.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, confira [Azure Security Benchmark: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: use aliases do Azure Policy no namespace "Microsoft.ContainerService" para criar políticas personalizadas a fim de auditar ou impor a configuração das instâncias do AKS (Serviço de Kubernetes do Azure). Use definições internas do Azure Policy.

Entre os exemplos de definições de política internas do AKS estão:

- Impor a entrada de HTTPS no cluster do Kubernetes

- Os intervalos de IP autorizados devem ser definidos nos Serviços do Kubernetes

- O RBAC (controle de acesso baseado em função) deve ser usado nos Serviços de Kubernetes

- Garantir que haja somente imagens de contêiner permitidas no cluster do Kubernetes

Exporte um modelo da sua configuração do AKS em JSON (JavaScript Object Notation) com o Azure Resource Manager. Examine-o periodicamente para garantir que essas configurações atendam aos requisitos de segurança da sua organização. Use as recomendações da Central de Segurança do Azure como uma linha de base de configuração segura para seus recursos do Azure. 

- [Como configurar e gerenciar políticas de segurança de pod do AKS](use-pod-security-policies.md)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras de sistema operacional

**Diretrizes**: os clusters do AKS (Clusters do Kubernetes do Azure) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas para reduzir a área da superfície de ataque e permite a implantação de contêineres de maneira segura. 

O Azure aplica patches diários (incluindo patches de segurança) aos hosts da máquina virtual do AKS com alguns patches que exigem uma reinicialização. Os clientes são responsáveis por agendar reinicializações do host da máquina virtual do AKS de acordo com a necessidade. 

- [Proteção de segurança para o sistema operacional do host do nó do agente do AKS](security-hardened-vm-host-image.md)

- [Noções básicas sobre a proteção de segurança em hosts da máquina virtual do AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretrizes**: proteja o cluster do AKS (Serviço de Kubernetes do Azure) usando políticas de segurança de pod.  Limite quais pods podem ser agendados para aprimorar a segurança do cluster. 

Os pods que solicitam recursos que não são permitidos não podem ser executados no cluster do AKS. 

Use também os efeitos [negar] e [implantar se não existir] do Azure Policy para impor configurações seguras para os recursos do Azure relacionados às implantações do AKS (como redes virtuais, sub-redes, firewalls do Azure, contas de armazenamento etc.). 

Crie definições personalizadas do Azure Policy usando aliases dos seguintes namespaces: 

- Microsoft.ContainerService

- Microsoft.Network

Há informações adicionais disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras de sistema operacional

**Diretrizes**: os clusters do AKS (Serviço de Kubernetes do Azure) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas para reduzir a área da superfície de ataque e permite a implantação de contêineres de maneira segura. 

Veja a lista de controles do CIS (Center for Internet Security) incorporados no sistema operacional do host.  

- [Proteção de segurança para o sistema operacional do host do nó do agente do AKS](security-hardened-vm-host-image.md)

- [Noções básicas sobre a configuração de estado de clusters do AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

- [Noções básicas sobre a proteção de segurança em hosts da máquina virtual do AKS](security-hardened-vm-host-image.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: use o Azure Repos para armazenar e gerenciar suas configurações com segurança se estiver usando definições personalizadas do Azure Policy. Exporte um modelo da sua configuração do AKS (Serviço de Kubernetes do Azure) em JSON (JavaScript Object Notation) com o Azure Resource Manager. Examine-o periodicamente para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Implemente soluções de terceiros, como o Terraform, para criar um arquivo de configuração que declare os recursos para o cluster do Kubernetes. Você pode proteger sua implantação do AKS implementando melhores práticas de segurança e armazenar sua configuração como código em uma localização segura.

- [Definir um cluster do Kubernetes](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks#define-a-kubernetes-cluster)

- [Proteção de segurança para o sistema operacional do host do nó do agente do AKS](security-hardened-vm-host-image.md)

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazene imagens personalizadas do sistema operacional com segurança

**Diretrizes**: não aplicável ao AKS (Serviço de Kubernetes do Azure). Por padrão, o AKS fornece um sistema operacional do host otimizado para segurança. Não há nenhuma opção atual para selecionar um sistema operacional alternativo ou personalizado.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implantar ferramentas de gerenciamento de configuração para recursos do Azure

**Diretrizes**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas usando definições de política internas, bem como aliases do Azure Policy no namespace "Microsoft.ContainerService". 

Crie políticas personalizadas para auditar e impor configurações do sistema. Desenvolva um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implantar ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretrizes**: os clusters do AKS (Serviço de Kubernetes do Azure) são implantados em máquinas virtuais do host com um sistema operacional otimizado para segurança. O sistema operacional do host tem etapas de proteção de segurança adicionais incorporadas para reduzir a área da superfície de ataque e permite a implantação de contêineres de maneira segura. 

Veja a lista de controles do CIS (Center for Internet Security) incorporados nos hosts do AKS.  

- [Proteção de segurança para o sistema operacional do host do nó do agente do AKS](security-hardened-vm-host-image.md)

- [Noções básicas sobre a proteção de segurança em hosts da máquina virtual do AKS](security-hardened-vm-host-image.md)

- [Noções básicas sobre a configuração de estado de clusters do AKS](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#control-plane)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: use a Central de Segurança para executar verificações de linha de base para recursos relacionados às implantações do AKS (Serviço de Kubernetes do Azure). Entre os exemplos de recursos estão, entre outros, o próprio cluster do AKS, a rede virtual em que o cluster do AKS foi implantado, a conta do Armazenamento do Azure usada para acompanhar o estado do Terraform ou as instâncias do Azure Key Vault que estão sendo usadas para as chaves de criptografia dos discos de SO e de dados do cluster do AKS.

- [Como corrigir recomendações na Central de Segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretrizes**: use as recomendações de contêiner da Central de Segurança descritas na seção "Computação e aplicativos" para executar verificações de linha de base para seus clusters do AKS (Serviço de Kubernetes do Azure). 

Receba notificações no painel da Central de Segurança quando forem encontrados problemas de configuração ou vulnerabilidades. Isso exige a habilitação do pacote de Registros de Contêiner opcional que permite que a Central de Segurança verifique a imagem.  

- [Entenda as recomendações do contêiner da Central de Segurança do Azure](../security-center/container-security.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerenciar segredos do Azure com segurança

**Diretrizes**: integre o Azure Key Vault a um cluster do AKS (Serviço de Kubernetes do Azure) usando uma unidade FlexVolume. Use o Azure Key Vault para armazenar e girar regularmente segredos, como credenciais, chaves de conta de armazenamento ou certificados. O driver FlexVolume permite que o cluster do AKS nativamente recupere credenciais do Cofre de Chaves e fornecê-las com segurança apenas para o pod solicitante. Use uma identidade gerenciada de pod para solicitar acesso ao Key Vault e recuperar as credenciais necessárias por meio do driver FlexVolume. Verifique se a exclusão temporária do cofre de chaves está habilitada. 

Limite a exposição da credencial não definindo credenciais no código do aplicativo. 

Evite o uso de credenciais fixas ou compartilhadas. 

- [Conceitos de segurança para aplicativos e clusters no AKS (Serviço de Kubernetes do Azure)](concepts-security.md)

- [Como usar o Key Vault com o cluster do AKS](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerenciar identidades de maneira segura e automática

**Diretrizes**: como uma melhor prática de segurança, não defina credenciais no código do aplicativo. Use identidades gerenciadas para recursos do Azure a fim de permitir que um pod se autentique em qualquer serviço no Azure que dê suporte a ele, incluindo o Azure Key Vault. O pod recebe uma identidade do Azure para se autenticar no Azure AD (Azure Active Directory) e receber um token digital que pode ser apresentado a outros serviços do Azure que verificam se o pod está autorizado a acessar o serviço e executar as ações necessárias.

Observe que as identidades gerenciadas pelo pod devem ser usadas somente com os pods e as imagens de contêiner do Linux. Provisione o Azure Key Vault para armazenar e recuperar credenciais e chaves digitais. Chaves como aquelas usadas para criptografar discos de SO, os dados do cluster do AKS podem ser armazenados no Azure Key Vault.

As entidades de serviço também podem ser usadas em clusters do AKS. No entanto, os clusters que usam entidades de serviço podem acabar atingindo um estado no qual a entidade de serviço precisará ser renovada para manter o cluster funcionando. O gerenciamento de entidades de serviço adiciona complexidade, motivo pelo qual é mais fácil usar identidades gerenciadas. Os mesmos requisitos de permissão se aplicam a entidades de serviço e identidades gerenciadas.

- [Noções básicas sobre as identidades gerenciadas e o Key Vault com o AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/developer-best-practices-pod-security#limit-credential-exposure)

- [Identidade de pod do Azure AD](https://github.com/Azure/aad-pod-identity)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também incentiva a migração das credenciais descobertas para localizações mais seguras, como o Azure Key Vault, com recomendações.

Limite a exposição da credencial não definindo credenciais no código do aplicativo. Além disso, evite o uso de credenciais compartilhadas. O Azure Key Vault deve ser usado para armazenar e recuperar credenciais e chaves digitais. Use identidades gerenciadas para recursos do Azure para permitir sua solicitação de acesso de pod para outros recursos. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Melhores práticas para desenvolvedores de segurança do pod](developer-best-practices-pod-security.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, confira [Azure Security Benchmark: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Use o software antimalware gerenciado de modo centralizado

**Diretrizes**: o AKS gerencia o ciclo de vida e as operações de nós de agente em seu nome. Não há suporte para a modificação dos recursos de IaaS associados aos nós de agente. No entanto, para nós do Linux, você pode usar conjuntos de daemon para instalar programas de software personalizados, como uma solução antimalware.

- [Guia de referência dos alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres – clusters do Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Responsabilidade compartilhada do AKS e conjuntos de daemon](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretrizes**: verifique previamente todos os arquivos que estão sendo carregados nos seus recursos do AKS. Use a detecção de ameaças da Central de Segurança para serviços de dados a fim de detectar malwares carregados em contas de armazenamento se estiver usando uma conta do Armazenamento do Azure como um armazenamento de dados ou acompanhar o estado do Terraform do seu cluster do AKS. 

- [Noções básicas sobre a detecção de ameaças da Central de Segurança do Azure para serviços de dados](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretrizes**: o AKS gerencia o ciclo de vida e as operações de nós de agente em seu nome. Não há suporte para a modificação dos recursos de IaaS associados aos nós de agente. No entanto, para nós do Linux, você pode usar conjuntos de daemon para instalar programas de software personalizados, como uma solução antimalware.

- [Guia de referência dos alertas de segurança](../security-center/alerts-reference.md)

- [Alertas para contêineres – clusters do Serviço de Kubernetes do Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)

- [Responsabilidade compartilhada do AKS e conjuntos de daemon](https://docs.microsoft.com/azure/aks/support-policies#shared-responsibility)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, confira [Azure Security Benchmark: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantir backups automatizados regulares

**Diretrizes**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como o Velero, que pode fazer backup de volumes persistentes com recursos de cluster e configurações adicionais. Periodicamente, verifique a integridade e a segurança desses backups. 

Remova o estado dos seus aplicativos antes do backup. Em casos em que isso não possa ser feito, faça o backup dos dados de volumes persistentes e teste regularmente as operações de restauração para verificar a integridade dos dados e os processos necessários.

- [Melhores práticas de armazenamento e backup no AKS](operator-best-practices-storage.md)

- [Melhores práticas de continuidade dos negócios e recuperação de desastres no AKS](operator-best-practices-multi-region.md)

- [Noções básicas sobre o Azure Site Recovery](../site-recovery/site-recovery-overview.md)

- [Como configurar o Velero no Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Fazer backups completos do sistema e backups de todas as chaves gerenciadas pelo cliente

**Diretrizes**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como o Velero, que pode fazer backup de volumes persistentes com recursos de cluster e configurações adicionais. 

Execute backups automatizados regulares de certificados, chaves, contas de armazenamento gerenciadas e segredos do Key Vault com os comandos do PowerShell. 

- [Como fazer backup de certificados do Azure Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

- [Como fazer backup de chaves do Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Como fazer backup de contas de armazenamento gerenciadas do Key Vault](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Como fazer backup de segredos do Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

- [Como habilitar o Backup do Azure](/azure/backup/)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todos os backups, inclusive os de chaves gerenciadas pelo cliente

**Diretrizes**: execute periodicamente a restauração de dados de conteúdo no Backup do Velero. Se necessário, teste a restauração em uma rede virtual isolada.

Execute periodicamente a restauração de dados de certificados, chaves, contas de armazenamento gerenciadas e segredos do Key Vault com os comandos do PowerShell.

- [Como restaurar certificados do Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Como restaurar chaves do Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Como restaurar contas de armazenamento gerenciadas do Key Vault](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Como restaurar segredos do Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Como recuperar arquivos do backup da Máquina Virtual do Azure](/azure/backup/backup-azure-restore-files-from-vm)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção dos backups e das chaves gerenciadas pelo cliente

**Diretrizes**: faça backup dos dados usando uma ferramenta apropriada para seu tipo de armazenamento, como o Velero, que pode fazer backup de volumes persistentes com recursos de cluster e configurações adicionais. 

Habilite a exclusão reversível no Key Vault para proteger as chaves contra a exclusão acidental ou mal-intencionada se o Azure Key Vault estiver sendo usado para as implantações do AKS (Serviço de Kubernetes do Azure).

- [Noções básicas sobre a Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md)

- [Como habilitar a exclusão reversível no Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview?tabs=azure-portal)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Guia de Tratamento de Incidentes de Segurança do Computador do NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretrizes**: priorize os alertas que precisam ser investigados primeiro com a severidade atribuída pela Central de Segurança aos alertas. A severidade é baseada no grau de confiança da Central de Segurança nas conclusões ou na análise usada para emitir o alerta, bem como no nível de confiança em uma possível ação mal-intencionada por trás da atividade que gerou o alerta.
Marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretrizes**: conduza exercícios para testar as funcionalidades de resposta a incidentes dos seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise os planos de resposta a incidentes conforme necessário.

- [Guia para programas de teste, treinamento e exercícios para planos de TI e funcionalidades](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal. 

Examine os incidentes, após o fato, para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte os alertas e as recomendações da Central de Segurança usando o recurso de Exportação Contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. 

Escolha o conector de dados da Central de Segurança para transmitir os alertas para o Azure Sentinel, de acordo com a necessidade e os requisitos empresariais organizacionais.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: use o recurso de Automação de Fluxo de Trabalho da Central de Segurança para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas e recomendações de segurança.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, confira [Azure Security Benchmark: testes de penetração e exercícios de Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes de penetração regulares dos recursos do Azure e garantir a correção de todas as conclusões de segurança críticas

**Diretrizes**: siga as Regras de Participação da Microsoft para garantir que os testes de penetração não violem as políticas da Microsoft. Mais informações sobre a estratégia da Microsoft e a execução de Equipes Vermelhas e testes de penetração de sites ativos em serviços, infraestrutura de nuvem e aplicativos gerenciados pela Microsoft nos links referenciados.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da Central de Segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
