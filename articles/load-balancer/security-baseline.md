---
title: Linha de base de segurança do Azure para Azure Load Balancer
description: A linha de base de segurança Azure Load Balancer fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bffc9eb3e75dda2b04ad4118d1f599f85a0013c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590089"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>Linha de base de segurança do Azure para Azure Load Balancer

Essa linha de base de segurança aplica diretrizes da [versão 1,0 do benchmark de segurança do Azure](../security/benchmarks/overview-v1.md) para Microsoft Azure Load Balancer. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure Load Balancer. **Controles** não aplicáveis a Azure Load Balancer foram excluídos.

 
Para ver como Azure Load Balancer é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure Load Balancer](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Use balanceadores de carga internos do Azure para permitir somente o tráfego para recursos de back-end de determinadas redes virtuais ou redes virtuais emparelhadas sem exposição à Internet. Implementar um Load Balancer externo com a rede de origem

A conversão de endereços (SNAT) para mascarar os endereços IP de recursos de back-end para proteção contra exposição direta da Internet.

O Azure oferece dois tipos de ofertas de Load Balancer, Standard e Basic. Use o Standard Load Balancer para todas as cargas de trabalho de produção. Implemente grupos de segurança de rede e somente permita o acesso às portas confiáveis e aos intervalos de endereços IP do seu aplicativo. Nos casos em que não há nenhum grupo de segurança de rede atribuído à sub-rede de back-end ou NIC das máquinas virtuais de back-end, o tráfego não será permitido a esses recursos do balanceador de carga. Com os balanceadores de carga padrão, forneça regras de saída para definir o NAT de saída com um grupo de segurança de rede. Examine essas regras de saída para ajustar o comportamento de suas conexões de saída.

O uso de um Standard Load Balancer é recomendado para suas cargas de trabalho de produção e, normalmente, o Load Balancer básico é usado apenas para teste, já que o tipo básico é aberto para conexões da Internet por padrão e não requer grupos de segurança de rede para a operação.

- [Conexões de saída no Azure](load-balancer-outbound-connections.md)

- [Atualizar Load Balancer públicos do Azure](upgrade-basic-standard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Diretrizes**: a Load Balancer é um serviço de passagem, pois depende das regras de grupos de segurança de rede aplicadas aos recursos de back-end e às regras de saída configuradas para controlar o acesso à Internet.

Examine as regras de saída configuradas para seu Standard Load Balancer por meio da folha regras de saída do seu Load Balancer e da folha regras de balanceamento de carga em que você pode ter regras de saída implícitas habilitadas.

Monitore a contagem de suas conexões de saída para controlar com que frequência seus recursos estão chegando à Internet. 

Use a central de segurança e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede do Azure.

Siga as recomendações de segurança para seus recursos de back-end e habilite os logs de fluxo do grupo de segurança de rede e envie os logs para uma conta de armazenamento do Azure para auditoria.

Além disso, envie os logs de fluxo para um espaço de trabalho Log Analytics e, em seguida, use Análise de Tráfego para fornecer informações sobre os padrões de tráfego em sua nuvem do Azure. As vantagens do Análise de Tráfego incluem a capacidade de visualizar a atividade da rede, identificar pontos de acesso e ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar as configurações incorretas da rede.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como fazer verificar minhas estatísticas de conexão de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Orientação**: defina explicitamente a conectividade com a Internet e os IPs de origem válidos por meio de regras de saída e grupos de segurança de rede com seu Load Balancer para usar a inteligência contra ameaças da Microsoft para proteger seus aplicativos Web.

- [Integrar o Firewall do Azure](../firewall/integrate-lb.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: rejeitar comunicações com endereços IP maliciosos conhecidos

**Orientação**: habilitar a proteção padrão de DDoS (negação de serviço distribuído) do Azure em sua rede virtual do Azure para proteger contra ataques de DDoS. 

Implante o Firewall do Azure em cada um dos limites de rede da organização com a filtragem baseada em inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado.

 

Use a proteção contra ameaças da central de segurança para detectar comunicações com endereços IP mal-intencionados conhecidos. 

O Standard Load Balancer é projetado para ser seguro por padrão e parte de uma rede virtual privada e isolada. Ele é fechado para fluxos de entrada, a menos que sejam abertos por grupos de segurança de rede para permitir explicitamente o tráfego permitido e para não permitir endereços IP mal-intencionados conhecidos. A menos que um grupo de segurança de rede em uma sub-rede ou NIC de seu recurso de máquina virtual exista por trás do Load Balancer, o tráfego não tem permissão para acessar esse recurso. 

Implante o Firewall do Azure em cada um dos limites de rede da organização com a filtragem baseada em inteligência contra ameaças habilitada e configurada para "alertar e negar" para tráfego de rede mal-intencionado para evitar ataques de endereços IP mal-intencionados. 

Implemente diretrizes para integrar o Firewall do Azure com seu Load Balancer.

Use os recursos da proteção contra ameaças da central de segurança para detectar comunicações com endereços IP mal-intencionados conhecidos. 

A central de segurança (camada Standard) fornece acesso à máquina virtual just-in-time e configura os endereços IP de origem permitidos para permitir o acesso somente de intervalos/endereços IP aprovados.
 

Use o recurso de proteção de rede adaptável da central de segurança para recomendar configurações de grupo de segurança de rede que limitam portas e IPs de origem com base no tráfego real e na inteligência contra ameaças.
 

- [Gerenciar a Proteção contra DDoS do Azure Standard usando o portal do Azure](../ddos-protection/manage-ddos-protection.md)

- [Filtragem baseada em inteligência contra ameaças do Firewall do Azure](../firewall/threat-intel.md)

- [Proteção contra ameaças na Central de Segurança do Azure](../security-center/azure-defender.md)

- [Proteja suas portas de gerenciamento com acesso just-in-time](../security-center/security-center-just-in-time.md)

- [Proteção de rede adaptável na central de segurança do Azure](../security-center/security-center-adaptive-network-hardening.md)

- [Integre o Firewall do Azure com seu Load Balancer](../firewall/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: habilitar a captura de pacotes do observador de rede para investigar atividades anormais.

- [Como criar uma instância do observador de rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: implemente uma oferta do Azure Marketplace que dá suporte à funcionalidade de IDS/IPS com recursos de inspeção de carga para o ambiente do seu Load Balancer. 

Use a inteligência contra ameaças do firewall do Azure se a inspeção de conteúdo não for um requisito. A filtragem baseada em inteligência de ameaças do firewall do Azure é usada para alertar e/ou bloquear o tráfego de e para domínios e endereços IP mal-intencionados conhecidos. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

Implante a solução de firewall de sua escolha em cada um dos limites de rede da sua organização para detectar e/ou bloquear o tráfego mal-intencionado.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Como implantar o Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Como configurar alertas com o Firewall do Azure](../firewall/threat-intel.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: defina explicitamente a conectividade com a Internet e os IPs de origem válidos por meio de regras de saída e grupos de segurança de rede com seu Load Balancer para usar os recursos de inteligência contra ameaças da Microsoft para proteger seus aplicativos Web.

- [Integrar o Firewall do Azure](../firewall/integrate-lb.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: use marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Especifique o nome da marca de serviço no campo de origem ou de destino de uma regra para permitir ou negar o tráfego para o serviço correspondente. 

A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. 

Por padrão, cada grupo de segurança de rede inclui a marca de serviço AzureLoadBalancer para permitir o tráfego de investigação de integridade. 

Consulte a documentação do Azure para todas as marcas de serviço disponíveis para uso em regras de grupo de segurança de rede.

- [Marcas de serviço disponíveis](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para recursos de rede com Azure Policy.

Use os planos gráficos do Azure para simplificar implantações do Azure em larga escala empacotando artefatos de ambiente-chave, como modelos do Azure Resource Manager, controles RBAC do Azure e políticas, em uma única definição de Blueprint. 

Aplique o plano gráfico às novas assinaturas e ajuste o controle e o gerenciamento, por meio da versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exemplos de Azure Policy para rede](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas de recurso para grupos de segurança de rede e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. 

Use o campo "Descrição" para documentar as regras que permitem o tráfego de/para uma rede para regras de grupo de segurança de rede individuais.

Implemente qualquer uma das definições de Azure Policy internas relacionadas à marcação, como "exigir marca e seu valor", o que garante que todos os recursos sejam criados com marcas e notifiquem quaisquer recursos não marcados existentes.

Use Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma rede virtual do Azure](../virtual-network/quick-create-portal.md)

- [Como filtrar o tráfego de rede com regras de grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos e detectar alterações nos recursos do Azure. 

Crie alertas no Azure Monitor para notificá-lo quando os recursos críticos forem alterados.

- [Como exibir e recuperar eventos do log de atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: examine as alterações em suas regras de saída e grupos de segurança de rede para seus balanceadores de carga exibindo o log de atividades em suas assinaturas. 

Exiba os logs de host internos para garantir que seus recursos de back-end sejam seguros.

Exporte esses logs para Log Analytics ou outra plataforma de armazenamento. No Azure Monitor, use espaços de trabalho do Log Analytics para consultar e executar análises e usar contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

Habilite e integre esses dados ao Azure Sentinel ou a um SIEM de terceiros com base em seus requisitos de negócios organizacionais.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como coletar logs e métricas de plataforma com Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como coletar logs de host interno da máquina virtual do Azure com Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Logs de atividade da plataforma](../azure-monitor/essentials/activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: revise as informações de log e auditoria do plano de gerenciamento e do controle capturadas com os logs de atividade para o Load Balancer básico. Essas configurações de captura são habilitadas por padrão. 

Use logs de atividade para monitorar ações em recursos para exibir todas as atividades e seu status. 

Determine quais operações foram executadas nos recursos em sua assinatura com os logs de atividade: 

- quem iniciou a operação
- quando a operação ocorreu
- o status da operação

- os valores de outras propriedades que podem ajudar você a pesquisar a operação

Recupere informações do log de atividades por meio do Azure PowerShell, a CLI (interface de linha de comando) do Azure, a API REST do Azure ou o portal do Azure. 

Implemente o diagnóstico multidimensional para os recursos de configurações de Standard Load Balancer com Azure Monitor.  Isso inclui as métricas disponíveis para segurança que incluem informações sobre conexões SNAT (conversão de endereço de rede de origem), portas. Métricas adicionais em pacotes SYN (Synchronize) e contadores de pacotes também estão disponíveis. 

Recuperar métricas multidimensionais programaticamente por meio de APIs e gravá-las em uma conta de armazenamento por meio da opção ' todas as métricas '.

Use o pacote de conteúdo dos logs de auditoria do Azure com o Microsoft Power BI para analisar seus dados com painéis pré-configurados ou para personalizar as exibições com base em seus requisitos.

Transmita logs para um hub de eventos ou um espaço de trabalho Log Analytics. Eles também podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como Excel e Power BI. 

Habilite e integre dados ao Azure Sentinel ou a um SIEM de terceiros com base em seus requisitos de negócios.

- [Leia este artigo com instruções passo a passo para cada método detalhado nas operações de auditoria com o Gerenciador de recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Logs do Azure Monitor para o Basic Load Balancer](load-balancer-monitor-log.md)

- [Exibir logs de atividade para monitorar ações em recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Recuperar as métricas multidimensionais programaticamente por meio de APIs](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: o log de atividades é habilitado por padrão e é preservado por 90 dias no repositório de logs de eventos do Azure. Defina seu período de retenção de Log Analytics espaço de trabalho de acordo com os regulamentos de conformidade de sua organização em Azure Monitor. Use contas de armazenamento do Azure para armazenamento de longo prazo e arquivamento.

- [Exibir logs de atividades para monitorar ações no artigo de recursos](../azure-resource-manager/management/view-activity-logs.md)

- [Alterar o período de retenção de dados em Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Como configurar a política de retenção para logs de conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2.6: monitorar e revisar logs

**Orientação**: monitorar, gerenciar e solucionar problemas Standard Load Balancer recursos usando a página Load Balancer no portal do Azure e a página Resource Health em Azure monitor. As métricas disponíveis para segurança incluem informações sobre conexões SNAT (conversão de endereço de rede de origem), portas. Além disso, as métricas em pacotes SYN (sincronizar) e contadores de pacotes também estão disponíveis. 

Use Azure Monitor para examinar o status da investigação de integridade do ponto de extremidade com métricas multidimensionais para balanceadores de carga padrão, externos e internos. Reúna essas métricas programaticamente por meio de APIs e gravadas em uma conta de armazenamento por meio da opção ' todas as métricas '.

Os logs de Azure Monitor não estão disponíveis para balanceadores de carga básicos internos. 

Use Azure Monitor para ver o status de investigação de integridade resumido por pool de back-end para o Load Balancer externo básico, como, o número de instâncias em seu pool de back-end não está recebendo solicitações do Load Balancer devido a falhas de investigação de integridade. 

Implemente o log com o Azure Operational insights para fornecer estatísticas sobre o status de integridade do balanceador de carga. 

Use os logs de atividade para exibir alertas e monitorar ações em recursos e seu status em suas assinaturas do Azure. Os logs de atividade são habilitados por padrão e podem ser exibidos no portal do Azure. 

Use o Microsoft Power BI com o pacote de conteúdo dos logs de auditoria do Azure e analise seus dados com painéis pré-configurados. Personalize exibições para atender às suas necessidades com base na necessidade de negócios. 

Transmita logs para um hub de eventos ou um espaço de trabalho Log Analytics. Eles também podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como Excel e Power BI. Você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Investigações de integridade do Load Balancer](load-balancer-custom-probe-overview.md)

- [API REST do Azure Monitor](/rest/api/monitor)

- [Como recuperar métricas por meio da API REST](/rest/api/monitor/metrics/list)

- [Diagnóstico de Standard Load Balancer com métricas, alertas e integridade de recursos](load-balancer-standard-diagnostics.md)

- [Logs do Azure Monitor para o Basic Load Balancer](load-balancer-monitor-log.md)

- [Exibir suas métricas do balanceador de carga no portal do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Use a central de segurança com log Analytics espaço de trabalho para monitoramento e alertas sobre atividades anormais relacionadas a Load Balancer em eventos e logs de segurança.

Habilitar e integrar dados ao Azure Sentinel ou a uma ferramenta SIEM de terceiros.

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Como gerenciar alertas na central de segurança do Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Como alertar sobre dados de log do log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: o Azure RBAC (controle de acesso baseado em função) do Azure permite que você gerencie o acesso aos recursos do Azure, como o Load Balancer por meio de atribuições de função. Atribua essas funções a usuários, grupos de entidades de serviço e identidades gerenciadas.

Funções predefinidas e internas de inventário para determinados recursos com ferramentas como CLI do Azure, Azure PowerShell ou portal do Azure.

- [Como obter uma função de diretório no Azure Active Directory (Azure AD) com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usar a autenticação multifator para todos os acessos baseados no Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso da central de segurança.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com autenticação multifator configurada para gerenciar e acessar recursos de rede do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](/security/compass/privileged-access-devices)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gerenciar recursos do Azure somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso somente de agrupamentos lógicos específicos de intervalos de endereços IP ou países/regiões.

- [Como configurar locais nomeados no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use o Azure Active Directory (AD do Azure) como um sistema de autenticação e autorização central para seus serviços. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.  

- [Como criar e configurar uma instância do Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: Use Azure Active Directory (Azure AD) para fornecer logs para ajudar a descobrir contas obsoletas. 

As revisões de acesso de identidade do Azure podem ser executadas para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso do usuário deve ser revisado regularmente para garantir que apenas os usuários ativos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: integre a atividade de entrada do Azure Active Directory (Azure AD), as fontes de log de eventos de auditoria e de risco, com qualquer ferramenta Siem ou de monitoramento baseada em seu acesso.

Simplifique esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Todos os alertas desejados podem ser configurados no espaço de trabalho Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: alertar sobre o desvio de comportamento de logon na conta

**Diretrizes**: Use os recursos de proteção de risco e identidade do Azure Active Directory (AD do Azure) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Ingerir dados no Azure Sentinel para qualquer investigação adicional.

- [Como exibir entradas suspeitas do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: Verifique se todos os clientes que se conectam aos recursos do Azure são capazes de negociar o TLS 1,2 ou superior.

Siga as recomendações da central de segurança do Azure para criptografia em repouso e criptografia em trânsito, quando aplicável.

- [Entender a criptografia em trânsito com o Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usar o RBAC do Azure para gerenciar o acesso aos recursos

**Orientação**: Use o RBAC do Azure para controlar o acesso aos seus recursos de Load Balancer.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretrizes**: Load Balancer é um serviço de passagem que não armazena dados do cliente. Ele faz parte da plataforma subjacente que é gerenciada pela Microsoft. 

A Microsoft trata todo o conteúdo do cliente como confidencial e vai para uma grande quantidade de comprimentos para se proteger contra a perda e a exposição de dados do cliente. 

Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados. 

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas quando as alterações ocorrerem para recursos críticos do Azure, como balanceadores de carga usados para cargas de trabalho de produção importantes.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar e descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos e assim por diante) em suas assinaturas. Azure Resource Manager é recomendável para criar e usar os recursos atuais.

Verifique as permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas e recursos do Azure em suas assinaturas.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure com metadados para organizar logicamente de acordo com uma taxonomia.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas, quando apropriado, para organizar e acompanhar ativos. 

Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos de suas assinaturas em tempo hábil.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definir e manter um inventário de recursos aprovados do Azure

**Diretrizes**: Crie uma lista de recursos aprovados do Azure de acordo com suas necessidades organizacionais, que você pode aproveitar como um mecanismo de lista de permissões. Isso permitirá que sua organização integre quaisquer serviços do Azure recentemente disponíveis depois que eles forem formalmente revisados e aprovados pelos processos de avaliação de segurança típicos da sua organização.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Consulte e descubra recursos com o grafo de recursos do Azure nas assinaturas de propriedade. 

Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Azure Policy de exemplo interno para rede virtual](/azure/virtual-network/policy-samples)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Use o acesso condicional do Azure Active Directory (Azure AD) para limitar a capacidade dos usuários de interagir com Azure Resource Manager Configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Gerenciador de recursos do Azure](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Orientação**: software necessário para operações de negócios, mas pode incorrer em um risco maior para a organização, deve ser isolado em sua própria máquina virtual e/ou rede virtual e é suficientemente protegido com um firewall do Azure ou um grupo de segurança de rede.

- [Como criar uma rede virtual](../virtual-network/quick-create-portal.md)

- [Como criar um grupo de segurança de rede com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos recursos do Azure. Use definições de Azure Policy internas.

Azure Resource Manager tem a capacidade de exportar o modelo no JavaScript Object Notation (JSON), que deve ser revisado para garantir que as configurações atendam aos requisitos de segurança da sua organização.

Exporte modelos de Azure Resource Manager em formatos de JavaScript Object Notation (JSON) e examine-os periodicamente para garantir que as configurações atendam aos requisitos de segurança organizacional.

Implemente as recomendações da central de segurança como uma linha de base de configuração segura para os recursos do Azure.

- [Como exibir os aliases de Azure Policy disponíveis](/powershell/module/az.resources/get-azpolicyalias)

- [Tutorial: Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e de vários recursos para um modelo no portal do Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança – um guia de referência](../security-center/recommendations-reference.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Diretriz**: use o Azure Policy [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.  Além disso, você pode usar modelos de Azure Resource Manager para manter a configuração de segurança dos recursos do Azure exigidos por sua organização. 

- [Entender Azure Policy efeitos](../governance/policy/concepts/effects.md)

- [Criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Visão geral de modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Use o Azure DevOps para armazenar e gerenciar com segurança seu código, como definições de Azure Policy personalizadas, modelos de Azure Resource Manager e scripts de configuração de estado desejado.

Conceda ou negue permissões a usuários específicos, grupos de segurança internos ou grupos definidos no Azure Active Directory (AD do Azure) se ele estiver integrado com o Azure DevOps ou no Azure AD se integrado ao TFS.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Sobre permissões e grupos no Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para recursos do Azure usando Azure Policy.  Use aliases de Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos recursos do Azure. Implemente definições de políticas internas relacionadas aos seus recursos específicos do Azure Load Balancer.  Além disso, use a automação do Azure para implantar alterações de configuração. para seus recursos de Azure Load Balancer.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como usar aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretrizes**: Use a central de segurança para executar verificações de linha de base para seus recursos do Azure e Azure Policy para alertar e auditar configurações de recursos.

- [Como corrigir recomendações na central de segurança do Azure](../security-center/security-center-remediate-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente. 

- [Como configurar automações de fluxo de trabalho na Central de Segurança do Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [O cliente também pode aproveitar o guia de tratamento de incidentes de segurança do computador da NIST para ajudar na criação de seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. 

A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Marque as assinaturas usando marcas e crie um sistema de nomeação para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  

É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise seu plano de resposta conforme necessário. 

- [Publicação do NIST – guia para testar, treinar e exercitar programas para planos de ti e recursos](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos. 

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua para ajudar a identificar os riscos para os recursos do Azure. 

Use o recurso de exportação contínua na central de segurança que permite exportar alertas e recomendações de forma manual ou contínua, de modo contínuo. 

Utilize o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança para disparar automaticamente respostas a alertas de segurança e recomendações para proteger os recursos do Azure.

- [Como configurar a automação do fluxo de trabalho na segurança Enter](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
