---
title: Linha de base de segurança do Azure para gateway de aplicativo
description: A linha de base de segurança do gateway de aplicativo fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 75d219a207fef4f1f122799af5f4f4ef2765bca4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723853"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Linha de base de segurança do Azure para gateway de aplicativo

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) ao gateway de aplicativo. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao gateway de aplicativo. Os **controles** não aplicáveis ao gateway de aplicativo foram excluídos.

 
Para ver como o gateway de aplicativo é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base do gateway de aplicativo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: Verifique se todas as implantações de sub-rede de gateway aplicativo Azure rede virtual têm um NSG (grupo de segurança de rede) aplicado com controles de acesso à rede específicos para as portas e fontes confiáveis do seu aplicativo. Embora os grupos de segurança de rede tenham suporte no gateway Aplicativo Azure, há algumas restrições e requisitos que devem ser cumpridos para que o NSG e o gateway de Aplicativo Azure funcionem conforme o esperado.

- [Entenda as restrições e os requisitos em relação ao uso do NSGs com o gateway Aplicativo Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: para os NSGs (grupos de segurança de rede) associados às sub-redes de Gateway de aplicativo Azure, habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Observação: há alguns casos em que os logs de fluxo do NSG associados às suas sub-redes de gateway de Aplicativo Azure não mostrarão o tráfego permitido. Se sua configuração corresponder ao cenário a seguir, você não verá o tráfego permitido em seus logs de fluxo do NSG:

- Você implantou o Gateway de Aplicativo v2
- Você tem um NSG na sub-rede do gateway de aplicativo
- Você habilitou os logs de fluxo do NSG no NSG

Para obter informações adicionais, consulte as referências abaixo.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Perguntas frequentes para diagnóstico e log para Aplicativo Azure gateway](/azure/application-gateway/application-gateway-faq#what-types-of-logs-does-application-gateway-provide)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. O WAF (firewall do aplicativo Web) é um serviço (recurso do gateway de Aplicativo Azure) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger seus aplicativos Web do serviço de Azure App inspecionando o tráfego da Web de entrada para bloquear ataques como injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS. O WAF é baseado em regras dos conjuntos de regras de núcleo do OWASP (Open Web Application Security Project) 3.1 (somente WAF_v2), 3.0 e 2.2.9. 

- [Entender Aplicativo Azure recursos de gateway](features.md)

- [Entender o Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilite a proteção padrão de DDoS em suas redes virtuais do Azure associadas às suas instâncias de produção do Gateway de aplicativo Azure para proteção contra ataques de DDoS. Use a inteligência de ameaças integrada da central de segurança do Azure para negar comunicações com endereços IP mal-intencionados conhecidos.

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: para os NSGs (grupos de segurança de rede) associados às sub-redes de Gateway de aplicativo Azure, habilite logs de fluxo de NSG e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo NSG para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Observação: há alguns casos em que os logs de fluxo do NSG associados às suas sub-redes de gateway de Aplicativo Azure não mostrarão o tráfego permitido. Se sua configuração corresponder ao cenário a seguir, você não verá o tráfego permitido em seus logs de fluxo do NSG:

- Você implantou o Gateway de Aplicativo v2
- Você tem um NSG na sub-rede do gateway de aplicativo
- Você habilitou os logs de fluxo do NSG no NSG

Para obter informações adicionais, consulte as referências abaixo.

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Perguntas frequentes para diagnóstico e log para Aplicativo Azure gateway](/azure/application-gateway/application-gateway-faq#what-types-of-logs-does-application-gateway-provide)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretrizes**: implante o WAF (firewall do aplicativo Web) do Azure na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. O WAF (firewall do aplicativo Web) é um serviço (recurso do gateway de Aplicativo Azure) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger seus aplicativos Web do serviço de Azure App inspecionando o tráfego da Web de entrada para bloquear ataques como injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS. O WAF é baseado em regras dos conjuntos de regras de núcleo do OWASP (Open Web Application Security Project) 3.1 (somente WAF_v2), 3.0 e 2.2.9. 

Como alternativa, há várias opções do Marketplace, como o Barracuda WAF para Azure, que estão disponíveis no Azure Marketplace, que inclui recursos de IDS/IPS.

- [Entender Aplicativo Azure recursos de gateway](features.md)

- [Entender o Azure WAF](../web-application-firewall/ag/ag-overview.md)

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Entender o serviço de nuvem Barracuda WAF](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: implante o Gateway de aplicativo Azure para aplicativos Web com HTTPS/SSL habilitado para certificados confiáveis.

- [Como implantar o gateway de aplicativo](quick-create-portal.md)

- [Como configurar o gateway de aplicativo para usar HTTPS](create-ssl-portal.md)

- [Entender o balanceamento de carga de camada 7 com gateways de aplicativo Web do Azure](overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede em grupos de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, Gatewaymanager) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Para os NSGs (grupos de segurança de rede) associados às suas sub-redes de gateway de Aplicativo Azure, você deve permitir o tráfego de entrada na Internet nas portas TCP 65503-65534 para o SKU do gateway de aplicativo v1 e as portas TCP 65200-65535 para a SKU V2 com a sub-rede de destino como qualquer e origem como a marca de serviço Gatewaymanager. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) pelos certificados do Azure. Entidades externas, incluindo os clientes desses gateways, não podem se comunicar nesses pontos de extremidade.

- [Entender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Visão geral da configuração do gateway de Aplicativo Azure](configuration-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para as configurações de rede relacionadas às implantações do aplicativo Azure gateway. Use Azure Policy aliases no namespace "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus gateways de Aplicativo Azure, redes virtuais do Azure e grupos de segurança de rede. Você também pode fazer uso da definição de política interna.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas em uma única definição de Blueprint. É fácil aplicar o blueprint a novas assinaturas e novos ambientes e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: use marcas para NSGs (grupos de segurança de rede) associados à sua sub-rede de Gateway de aplicativo Azure, bem como quaisquer outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

Use qualquer uma das definições de política internas do Azure relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de configurações de rede e recursos relacionados às implantações do aplicativo Azure gateway. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em configurações de rede ou recursos críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para seu gateway de Aplicativo Azure e recursos relacionados, como NSGs (grupos de segurança de rede), que estão sendo usados para proteger a sub-rede de gateway de Aplicativo Azure.

Além dos logs de atividade, você pode definir configurações de diagnóstico para suas implantações do Aplicativo Azure gateway. as configurações de diagnóstico são usadas para configurar a exportação de streaming de logs e métricas de plataforma para um recurso para o destino de sua escolha (contas de armazenamento, hubs de eventos e Log Analytics).

Aplicativo Azure gateway também oferece integração interna com o Aplicativo Azure insights. Application Insights coleta dados de log, desempenho e erro. Application Insights detecta automaticamente anomalias de desempenho e inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender como seus aplicativos Web estão sendo usados. Você pode habilitar a exportação contínua para exportar telemetria de Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Como habilitar as configurações de diagnóstico para Aplicativo Azure gateway](application-gateway-diagnostics.md)

- [Como habilitar Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para seu gateway de Aplicativo Azure e recursos relacionados, como NSGs (grupos de segurança de rede), que estão sendo usados para proteger a sub-rede de gateway de Aplicativo Azure.

Além dos logs de atividade, você pode definir configurações de diagnóstico para suas implantações do Aplicativo Azure gateway. as configurações de diagnóstico são usadas para configurar a exportação de streaming de logs e métricas de plataforma para um recurso para o destino de sua escolha (contas de armazenamento, hubs de eventos e Log Analytics).

Aplicativo Azure gateway também oferece integração interna com o Aplicativo Azure insights. Application Insights coleta dados de log, desempenho e erro. Application Insights detecta automaticamente anomalias de desempenho e inclui ferramentas de análise poderosas para ajudá-lo a diagnosticar problemas e a entender como seus aplicativos Web estão sendo usados. Você pode habilitar a exportação contínua para exportar telemetria de Application Insights para um local centralizado para manter os dados por mais tempo do que o período de retenção padrão.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Como habilitar as configurações de diagnóstico para Aplicativo Azure gateway](application-gateway-diagnostics.md)

- [Como habilitar Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Como configurar a exportação contínua](../azure-monitor/app/export-telemetry.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas de Armazenamento do Microsoft Azure para armazenamentos de longo prazo/arquivamento.

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para seu Gateway de aplicativo Azure e envie os logs para um espaço de trabalho log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados.

Use Azure Monitor para redes para uma visão abrangente da integridade e das métricas de todos os recursos de rede implantados, incluindo seus gateways de Aplicativo Azure. 

Opcionalmente, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros. 

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Como habilitar as configurações de diagnóstico para Aplicativo Azure gateway](application-gateway-diagnostics.md)

- [Como usar Azure Monitor para redes](../azure-monitor/insights/network-insights-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: implante o SKU do WAF (firewall do aplicativo Web) V2 na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. O WAF (firewall do aplicativo Web) é um serviço (recurso do gateway de Aplicativo Azure) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger seus aplicativos Web do serviço de Azure App inspecionando o tráfego da Web de entrada para bloquear ataques como injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS. O WAF é baseado em regras dos conjuntos de regras de núcleo do OWASP (Open Web Application Security Project) 3.1 (somente WAF_v2), 3.0 e 2.2.9. 

Habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para o Azure WAF e envie os logs para um espaço de trabalho Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados.  Você pode criar alertas com base em suas consultas do workspace do Log Analytics.

Use Azure Monitor para redes para uma visão abrangente da integridade e das métricas de todos os recursos de rede implantados, incluindo seus gateways de Aplicativo Azure. No console do Azure Monitor para redes, você pode exibir e criar alertas para Aplicativo Azure gateway.

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Como habilitar as configurações de diagnóstico para Aplicativo Azure gateway](application-gateway-diagnostics.md)

- [Como usar Azure Monitor para redes](../azure-monitor/insights/network-insights-overview.md)

- [Como criar alertas no Azure](/azure/azure-monitor/learn/tutorial-response)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizar o registro em log de antimalware

**Diretrizes**: implante o WAF (firewall do aplicativo Web) V2 na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. O WAF (firewall do aplicativo Web) é um serviço (recurso do gateway de Aplicativo Azure) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger seus aplicativos Web do serviço de Azure App inspecionando o tráfego da Web de entrada para bloquear ataques como injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS.

Defina as configurações de diagnóstico para suas implantações do Aplicativo Azure gateway. as configurações de diagnóstico são usadas para configurar a exportação de streaming de logs e métricas de plataforma para um recurso para o destino de sua escolha (contas de armazenamento, hubs de eventos e Log Analytics).

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como definir configurações de diagnóstico para o Azure WAF](application-gateway-diagnostics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: controle o acesso do plano ao aplicativo Azure gateway é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Para obter informações adicionais, consulte as referências abaixo.

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: Use um registro de aplicativo do Azure (entidade de serviço) para recuperar um token que pode ser usado para interagir com seus gateways de aplicativo Azure por meio de chamadas de API.

- [Como chamar as APIs REST do Azure](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Como registrar seu aplicativo cliente (entidade de serviço) com o Azure Active Directory (Azure AD)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informações da API dos serviços de recuperação do Azure](/rest/api/recoveryservices/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use PAWs (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure.

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use os relatórios de segurança do Azure Active Directory (AD do Azure) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente. Use a Central de Segurança do Azure para monitorar a atividade de identidade e acesso.

- [Como identificar usuários do Azure AD sinalizados em relação a atividades arriscadas](../active-directory/identity-protection/overview-identity-protection.md)

- [Como monitorar a atividade de identidade e acesso dos usuários na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretrizes**: Use localizações nomeadas de acesso condicional para permitir o acesso somente de agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](/azure/active-directory/reports-monitoring/)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Orientação**: você tem acesso a atividades de entrada do Azure Active Directory (Azure AD), auditoria e risco de log de eventos, que permitem a integração com qualquer ferramenta Siem/monitoramento.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas desejados no workspace do Log Analytics.

- [Como integrar os logs de atividades do Azure ao Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para auxiliar no rastreamento de recursos do Azure que armazenam ou processam informações confidenciais. 

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Certifique-se de que todas as implantações de sub-rede de gateway Aplicativo Azure rede virtual tenham um NSG (grupo de segurança de rede) aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo. Embora os grupos de segurança de rede tenham suporte no gateway Aplicativo Azure, há algumas restrições e requisitos que devem ser cumpridos para que o NSG e o gateway de Aplicativo Azure funcionem conforme o esperado.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Entenda as restrições e os requisitos em relação ao uso do NSGs com o gateway Aplicativo Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: Verifique se todas as implantações de sub-rede de gateway aplicativo Azure rede virtual têm um NSG (grupo de segurança de rede) aplicado com controles de acesso à rede específicos para as portas e fontes confiáveis do seu aplicativo. Restrinja o tráfego de saída para apenas locais confiáveis para ajudar a mitigar a ameaça de vazamento de dados. Embora os grupos de segurança de rede tenham suporte no gateway Aplicativo Azure, há algumas restrições e requisitos que devem ser cumpridos para que o NSG e o gateway de Aplicativo Azure funcionem conforme o esperado.

- [Entenda as restrições e os requisitos em relação ao uso do NSGs com o gateway Aplicativo Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: Configure a criptografia de ponta a ponta com TLS para seus gateways de aplicativo Azure.

- [Como configurar o TLS de ponta a ponta usando o gateway de Aplicativo Azure](end-to-end-ssl-portal.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao plano de controle de gateway aplicativo Azure (o portal do Azure).

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem nas instâncias de gateway aplicativo Azure de produção, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: atualmente não disponível; a avaliação de vulnerabilidade na central de segurança do Azure ainda não está disponível para Aplicativo Azure gateway.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para Aplicativo Azure gateway para reduzir as vulnerabilidades relacionadas à configuração.

- [Cobertura de recurso (incluindo avaliação de vulnerabilidade) para serviços de PaaS do Azure](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: comparar verificações de vulnerabilidade consecutivas

**Orientação**: ainda não disponível; a avaliação de vulnerabilidade na central de segurança do Azure ainda não está disponível para Aplicativo Azure gateway.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para Aplicativo Azure gateway para reduzir as vulnerabilidades relacionadas à configuração.

- [Cobertura de recurso (incluindo avaliação de vulnerabilidade) para serviços de PaaS do Azure](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Orientação**: ainda não disponível; a avaliação de vulnerabilidade na central de segurança do Azure ainda não está disponível para Aplicativo Azure gateway.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para Aplicativo Azure gateway para reduzir as vulnerabilidades relacionadas à configuração.

- [Cobertura de recurso (incluindo avaliação de vulnerabilidade) para serviços de PaaS do Azure](../security-center/features-paas.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: Manter metadados de ativo

**Diretriz**: Aplique marcas aos recursos do Azure, fornecendo metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Para obter informações adicionais, consulte as referências abaixo.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. 

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Para obter informações adicionais, consulte as referências abaixo.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Certifique-se de que todas as implantações de sub-rede de gateway Aplicativo Azure rede virtual tenham um NSG (grupo de segurança de rede) aplicado com controles de acesso à rede específicos às portas e fontes confiáveis do seu aplicativo. Embora os grupos de segurança de rede tenham suporte no gateway Aplicativo Azure, há algumas restrições e requisitos que devem ser cumpridos para que o NSG e o gateway de Aplicativo Azure funcionem conforme o esperado.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Entenda as restrições e os requisitos em relação ao uso do NSGs com o gateway Aplicativo Azure](configuration-overview.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para as configurações de rede relacionadas às implantações do aplicativo Azure gateway. Use Azure Policy aliases no namespace "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede de seus gateways de Aplicativo Azure, redes virtuais do Azure e grupos de segurança de rede. Você também pode fazer uso da definição de política interna.

- [Como exibir os aliases disponíveis do Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretrizes**: Se você estiver usando definições personalizadas do Azure Policy, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

- [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Network" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidades gerenciadas para fornecer seu Gateway de aplicativo Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD do Azure). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

Use Azure Key Vault para armazenar certificados com segurança. Azure Key Vault é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados SSL. O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Esse suporte é limitado ao SKU do Gateway de Aplicativo v2.

- [Como configurar a terminação SSL com Key Vault certificados usando Azure PowerShell](configure-keyvault-ps.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer seu Gateway de aplicativo Azure com uma identidade gerenciada automaticamente no Azure Active Directory (AD do Azure). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

Use Azure Key Vault para armazenar certificados com segurança. Azure Key Vault é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados SSL. O Gateway de Aplicativo v2 do Azure dá suporte à integração com o Key Vault para certificados de servidor que estejam anexados a ouvintes habilitados para HTTPS. Esse suporte é limitado ao SKU do Gateway de Aplicativo v2.

- [Como configurar a terminação SSL com Key Vault certificados usando Azure PowerShell](configure-keyvault-ps.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="malware-defense"></a>Defesa contra malwares

*Para obter mais informações, consulte o [benchmark de segurança do Azure: defesa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usar software antimalware gerenciado centralmente

**Diretrizes**: implante o WAF (firewall do aplicativo Web) V2 na frente de aplicativos Web críticos para inspeção adicional do tráfego de entrada. O WAF (firewall do aplicativo Web) é um serviço (recurso do gateway de Aplicativo Azure) que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O Azure WAF pode ajudar a proteger seus aplicativos Web do serviço de Azure App inspecionando o tráfego da Web de entrada para bloquear ataques como injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS.

Defina as configurações de diagnóstico para suas implantações do Aplicativo Azure gateway. as configurações de diagnóstico são usadas para configurar a exportação de streaming de logs e métricas de plataforma para um recurso para o destino de sua escolha (contas de armazenamento, hubs de eventos e Log Analytics).

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Como definir configurações de diagnóstico para o Azure WAF](application-gateway-diagnostics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: garantir que o software antimalware e as assinaturas sejam atualizados

**Orientação**: ao usar o WAF (firewall do aplicativo Web) do Azure, você pode configurar políticas do WAF. Uma política de WAF consiste em dois tipos de regras de segurança: regras personalizadas que são criadas pelo cliente e conjuntos de regras gerenciadas que são uma coleção de regras pré-configuradas gerenciadas pelo Azure. Os conjuntos de regras gerenciados pelo Azure oferecem uma maneira fácil de implantar a proteção contra um conjunto comum de ameaças de segurança. Como esses conjuntos de regras são gerenciados pelo Azure, as regras são atualizadas conforme necessário para proteção contra novas assinaturas de ataque.

- [Entender os conjuntos de regras de WAF gerenciados pelo Azure](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy-and-rules)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Orientação**: aplicativo Azure gateway não armazena dados do cliente. No entanto, se você estiver usando definições personalizadas de política do Azure, use o Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

O Azure DevOps Services aproveita muitos dos recursos de armazenamento do Azure para garantir a disponibilidade de dados em caso de falha de hardware, interrupção de serviço ou desastre na região. Além disso, a equipe do Azure DevOps segue os procedimentos para proteger os dados contra exclusão acidental ou mal-intencionada.

- [Entender a disponibilidade de dados no Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability&amp;preserve-view=true)

- [Como armazenar código no Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Documentação do Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Orientação**: fazer backup de certificados gerenciados pelo cliente no Azure Key Vault.

- [Como fazer backup de certificados do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: testar a restauração de backups de certificados gerenciados pelo cliente.

- [Como restaurar certificados do cofre de chaves](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: Verifique se a exclusão reversível está habilitada para Azure Key Vault. A exclusão reversível permite a recuperação de cofres de chaves e objetos de cofre excluídos, como chaves, segredos e certificados.

- [Como usar a exclusão reversível do Azure Key Vault](../key-vault/general/key-vault-recovery.md)

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

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Diretriz**: conduza exercícios para testar os recursos de resposta a incidentes de seus sistemas em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Veja a publicação do NIST: Guia para testar, treinar e exercitar programas para planos de TI e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: exporte os alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizar a resposta a alertas de segurança

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de "Aplicativos Lógicos" em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
