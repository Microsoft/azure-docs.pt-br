---
title: Linha de base de segurança do Azure para Azure Functions
description: A linha de base de segurança Azure Functions fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8987c4ac61e81c35f664e19dfc72936bb6754e18
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566942"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Linha de base de segurança do Azure para Azure Functions

Essa linha de base de segurança aplica as diretrizes da [1,0 versão de avaliação de segurança do Azure](../security/benchmarks/overview-v1.md) para Azure functions. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao Azure functions. **Controles** não aplicáveis a Azure Functions foram excluídos.

 
Para ver como Azure Functions é completamente mapeado para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base de segurança Azure Functions](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretrizes**: Integre seus aplicativos de Azure Functions com uma rede virtual do Azure. Os aplicativos de funções em execução no plano Premium têm os mesmos recursos de hospedagem que os aplicativos Web no serviço Azure App, que inclui o recurso "integração VNet".  As redes virtuais do Azure permitem que você coloque muitos dos seus recursos do Azure, como Azure Functions, em uma rede roteável que não seja da Internet.

- [Como integrar funções com uma rede virtual do Azure](functions-create-vnet.md)

- [Entender a integração vnet para o Azure Functions e o serviço de Azure App](../app-service/web-sites-integrate-with-vnet.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: Use a central de segurança do Azure e siga as recomendações de proteção de rede para ajudar a proteger os recursos de rede e as configurações de rede relacionadas aos seus aplicativos Azure functions.

Se estiver usando grupos de segurança de rede com sua implementação de Azure Functions, habilite logs de fluxo de grupos de segurança de rede e envie logs para uma conta de armazenamento do Azure para auditorias de tráfego. Você também pode enviar logs de fluxo de grupos de segurança de rede para um espaço de trabalho Log Analytics e usar Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: para proteger totalmente seus pontos de extremidade de Azure Functions em produção, você deve considerar a implementação de uma das seguintes opções de segurança de nível de aplicativo de função:

- Ativar a autenticação ou autorização do serviço de aplicativo para seu aplicativo de funções

- Use o APIM (Gerenciamento de API do Azure) para autenticar solicitações

- Implante seu aplicativo de funções em um Ambiente do Serviço de Aplicativo do Azure.

Além disso, verifique se a depuração remota foi desabilitada para seu Azure Functions de produção. Além disso, o CORS (compartilhamento de recursos entre origens) não deve permitir que todos os domínios acessem seu aplicativo de funções no Azure. Permitir que somente os domínios necessários interajam com seu aplicativo de funções.

Considere implantar o WAF (firewall do aplicativo Web) do Azure como parte da configuração de rede para inspeção adicional do tráfego de entrada. Habilite a configuração de diagnóstico para WAF e ingerir logs em uma conta de armazenamento, Hub de eventos ou espaço de trabalho de Log Analytics. 

- [Como proteger pontos de extremidade Azure Functions em produção](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como implantar o Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Orientação**: habilite a proteção contra DDoS Standard nas redes virtuais associadas aos seus aplicativos de funções para proteger contra ataques de DDoS. Use os recursos de inteligência contra ameaças na central de segurança do Azure integrada para negar comunicações com endereços IP públicos mal-intencionados ou não usados conhecidos.

Além disso, configure um gateway de front-end, como o Firewall do aplicativo Web do Azure, para autenticar todas as solicitações de entrada e filtrar o tráfego mal-intencionado. O Firewall do aplicativo Web do Azure pode ajudar a proteger seu aplicativo de funções inspecionando o tráfego da Web de entrada para bloquear injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS. A introdução de um firewall do aplicativo Web requer um Ambiente do Serviço de Aplicativo ou o uso de pontos de extremidade privados. Você pode usar pontos de extremidade privados para suas funções hospedadas nos planos de serviço de aplicativo e Premium.

- [Opções de rede do Azure Functions](functions-networking-options.md)

- [Como configurar a proteção contra DDoS](../ddos-protection/manage-ddos-protection.md)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: se estiver usando o grupo de segurança de rede com sua implementação de Azure functions, habilite logs de fluxo do grupo de segurança de rede e envie logs para uma conta de armazenamento para auditoria de tráfego. Você também pode enviar logs de fluxo para um workspace do Log Analytics e usar a Análise de Tráfego para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças de segurança, compreender os padrões de fluxo de tráfego e identificar configurações de rede incorretas.

- [Como habilitar logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Como habilitar o Observador de Rede](../network-watcher/network-watcher-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Orientação**: configurar um gateway de front-end, como o Firewall do aplicativo Web do Azure, para autenticar todas as solicitações de entrada e filtrar o tráfego mal-intencionado. O Firewall do aplicativo Web do Azure pode ajudar a proteger seus aplicativos de funções inspecionando o tráfego da Web de entrada para bloquear injeções de SQL, scripts entre sites, carregamentos de malware e ataques de DDoS. A introdução de um firewall do aplicativo Web requer um Ambiente do Serviço de Aplicativo ou o uso de pontos de extremidade privados. Você pode usar pontos de extremidade privados para suas funções hospedadas nos planos de serviço de aplicativo e Premium.

Como alternativa, há várias opções do Marketplace, como o Firewall do aplicativo Web Barracuda para Azure que estão disponíveis no Azure Marketplace, que incluem recursos de detecção de intrusão ou prevenção.

- [Opções de rede do Azure Functions](functions-networking-options.md)

- [Azure Functions plano Premium](functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicativo](../app-service/environment/intro.md)

- [Considerações de rede para um Ambiente do Serviço de Aplicativo](../app-service/environment/network-info.md) 

- [Entender o Firewall do aplicativo Web do Azure](../web-application-firewall/overview.md)

- [Usando pontos de extremidade privados para Azure Functions](../app-service/networking/private-endpoint.md)

- [Entender o serviço de nuvem Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Diretrizes**: Configure um gateway de front-end para sua rede, como o Firewall do aplicativo Web do Azure com criptografia TLS de ponta a ponta. A introdução de um firewall do aplicativo Web requer um Ambiente do Serviço de Aplicativo ou o uso de pontos de extremidade privados. Você pode usar pontos de extremidade privados para suas funções hospedadas nos planos de serviço de aplicativo e Premium.

- [Opções de rede do Azure Functions](functions-networking-options.md)

- [Azure Functions plano Premium](functions-premium-plan.md)

- [Introdução aos Ambientes de Serviço de Aplicativo](../app-service/environment/intro.md)

- [Considerações de rede para um Ambiente do Serviço de Aplicativo](../app-service/environment/network-info.md) 

- [Entender o Firewall do aplicativo Web do Azure](../web-application-firewall/overview.md)

- [Como configurar o TLS de ponta a ponta usando o gateway de aplicativo com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Usando pontos de extremidade privados para Azure Functions](../app-service/networking/private-endpoint.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretrizes**: use marcas de serviço de rede virtual para definir os controles de acesso à rede no grupo de segurança de rede ou no firewall do Azure. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, AzureAppService) no campo de origem ou destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Para obter mais informações sobre como usar marcas de serviço](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Diretrizes**: defina e implemente configurações de segurança padrão para as configurações de rede relacionadas à sua Azure functions. Use aliases de Azure Policy nos namespaces "Microsoft. Web" e "Microsoft. Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Azure Functions. Você também pode fazer uso de definições de política internas para Azure Functions, como:

- O CORS não deve permitir que todos os recursos acessem seus aplicativos de funções

- O aplicativo de funções só deve ser acessível via HTTPS

- A versão mais recente do TLS deve ser usada em seu aplicativo de funções

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas em uma única definição de Blueprint. É fácil aplicar o blueprint a novas assinaturas e novos ambientes e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Orientação**: se estiver usando o grupo de segurança de rede com sua implementação de Azure functions, use marcas para o NSGs e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de grupo de segurança de rede individuais, use o campo "Descrição" para especificar a necessidade de negócios e/ou a duração, e assim por diante, para qualquer regra que permita o tráfego de/para uma rede.

Use qualquer uma das definições de política internas do Azure relacionadas à marcação, como "exigir marca e seu valor" para garantir que todos os recursos sejam criados com marcas e notificá-lo de recursos não marcados existentes.

Você pode usar o Azure PowerShell ou a CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações de configurações de rede e recursos relacionados às implantações do Azure functions. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em configurações de rede ou recursos críticos. 

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

O Azure Functions também oferece integração interna com o Aplicativo Azure insights para monitorar funções. Application Insights coleta dados de log, desempenho e erro. Ele detecta automaticamente anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender como suas funções são usadas.

Se você tiver registro em log de segurança/auditoria personalizado interno em seu aplicativo de funções, habilite a configuração de diagnóstico "FunctionAppLogs" e envie os logs para um espaço de trabalho Log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. 

Opcionalmente, você pode habilitar o e os dados integrados ao Azure Sentinel ou a uma solução de gerenciamento de eventos e informações do sistema de terceiros. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como configurar Azure Functions com o Aplicativo Azure insights](functions-monitoring.md)

- [Como habilitar as configurações de diagnóstico (logs gerados pelo usuário) para Azure Functions](functions-monitor-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Orientação**: para log de auditoria do plano de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie os logs para um espaço de trabalho log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento Usando os dados do Log de Atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do plano de controle para seus recursos do Azure.

Se você tiver registro em log de segurança/auditoria personalizado interno em seu aplicativo de funções, habilite a configuração de diagnóstico "FunctionAppLogs" e envie os logs para um espaço de trabalho Log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como habilitar as configurações de diagnóstico (logs gerados pelo usuário) para Azure Functions](functions-monitor-log-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: coletar logs de segurança de sistemas operacionais

**Orientação**: não aplicável; esta diretriz destina-se a recursos de computação IaaS.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Diretrizes**: em Azure monitor, defina o período de retenção de log para log Analytics espaços de trabalho associados aos seus aplicativos de funções de acordo com os regulamentos de conformidade da sua organização.

- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para seu aplicativo de funções e envie os logs para um espaço de trabalho log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados.

Habilite Application Insights para seus aplicativos de funções para coletar dados de log, desempenho e erro. Você pode exibir os dados de telemetria coletados por Application Insights no portal do Azure.

Se você tiver registro em log de segurança/auditoria personalizado interno em seu aplicativo de funções, habilite a configuração de diagnóstico "FunctionAppLogs" e envie os logs para um espaço de trabalho Log Analytics, Hub de eventos do Azure ou conta de armazenamento do Azure para arquivamento. 

Opcionalmente, você pode habilitar o e os dados integrados ao Azure Sentinel ou a uma solução de gerenciamento de eventos e informações do sistema de terceiros.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como habilitar as configurações de diagnóstico para Azure Functions](functions-monitor-log-analytics.md)

- [Como configurar Azure Functions com Aplicativo Azure insights e exibir os dados de telemetria](functions-monitoring.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para seu aplicativo de funções e envie os logs para um espaço de trabalho log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados. Você pode criar alertas com base em suas consultas do workspace do Log Analytics.

Habilite Application Insights para seus aplicativos de funções para coletar dados de log, desempenho e erro. Você pode exibir os dados de telemetria coletados por Application Insights e criar alertas dentro do portal do Azure.

Opcionalmente, você pode habilitar o e os dados integrados ao Azure Sentinel ou a uma solução de gerenciamento de eventos e informações do sistema de terceiros.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como habilitar as configurações de diagnóstico para Azure Functions](functions-monitor-log-analytics.md)

- [Como habilitar Application Insights para Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Orientação**: controle o acesso do plano a Azure Functions é controlado por meio do Azure Active Directory (AD do Azure). O Azure AD não tem o conceito de senhas padrão.

O acesso ao plano de dados pode ser controlado por vários meios, incluindo chaves de autorização, restrições de rede e validação de uma identidade do Azure AD. As chaves de autorização são usadas pelos clientes que se conectam aos seus Azure Functions pontos de extremidade HTTP e podem ser regeneradas a qualquer momento. Por padrão, essas chaves são geradas para novos pontos de extremidade HTTP.

Vários métodos de implantação estão disponíveis para aplicativos de funções, alguns dos quais podem aproveitar um conjunto de credenciais geradas. Examine os métodos de implantação que serão usados para seu aplicativo.

- [Proteger um ponto de extremidade HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Como obter e regenerar chaves de autorização](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Tecnologias de implantação no Azure Functions](functions-deployment-technologies.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura

- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura

- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Informações adicionais estão disponíveis nos links referenciados.

- [Como usar a central de segurança do Azure para monitorar a identidade e o acesso ](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: sempre que possível, use o SSO do Azure Active Directory (Azure AD) em vez de configurar credenciais autônomas individuais para acesso a dados ao seu aplicativo de funções. Use as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure. Implemente o logon único para seus aplicativos do Functions usando o recurso de autenticação/autorização do serviço de aplicativo.

- [Entender a autenticação e a autorização no Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Entender o SSO com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite a autenticação multifator do Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de acesso e identidade da central de segurança do Azure.

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usar computadores dedicados (estações de trabalho com acesso privilegiado) para todas as tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Diretrizes**: Use o PIM (Azure Active Directory do Azure ad Privileged Identity Management) para a geração de logs e alertas quando uma atividade suspeita ou não segura ocorrer no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: Use localizações nomeadas de acesso condicional para permitir o acesso ao portal do Azure somente a agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos de funções. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

- [Como configurar seu aplicativo de funções para usar o logon do Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudá-lo a descobrir contas obsoletas. Além disso, use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo, o acesso aos aplicativos empresariais e as atribuições de função. O acesso de usuários pode ser examinado regularmente para garantir que somente os usuários corretos tenham acesso contínuo.

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos de funções. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Você tem acesso à atividade de entrada do Azure AD, às fontes de log de eventos de auditoria e de risco, que permitem a integração com o Azure Sentinel ou um SIEM de terceiros.

Você pode simplificar esse processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando logs de auditoria e logs de entrada para um espaço de trabalho Log Analytics. Você pode configurar os alertas de log desejados no Log Analytics.

- [Como configurar seu aplicativo de funções para usar o logon do Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos de funções. Para o desvio do comportamento de logon da conta no plano de controle (o portal do Azure), use Azure AD Identity Protection e recursos de detecção de risco para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. Os aplicativos de funções devem ser separados por mapeamentos de rede virtual (VNet) e marcados adequadamente.

Você também pode usar pontos de extremidade privados para executar o isolamento de rede. Um ponto de extremidade privado do Azure é uma interface de rede que conecta você de forma privada e segura a um serviço (por exemplo: ponto de extremidade HTTPs do aplicativo de funções) da plataforma Azure link privado. O Ponto de Extremidade Privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Você pode usar pontos de extremidade privados para suas funções hospedadas nos planos de serviço de aplicativo e Premium.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Opções de rede do Azure Functions](functions-networking-options.md)

- [Azure Functions plano Premium](functions-premium-plan.md)

- [Entender o ponto de extremidade privado](../private-link/private-endpoint-overview.md)

- [Usando pontos de extremidade privados para Azure Functions](../app-service/networking/private-endpoint.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Diretrizes**: no portal do Azure para seus aplicativos de funções, em "recursos da plataforma: rede: SSL", habilite a configuração "somente https" e defina a versão mínima do TLS como 1,2.

- [Exigir HTTPS em aplicativos de funções](./security-concepts.md#require-https)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: não disponível no momento; os recursos de identificação de dados, classificação e prevenção de perda não estão disponíveis no momento para Azure Functions. Marque os aplicativos de função que podem estar processando informações confidenciais como tal e implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usar o controle de acesso baseado em função para controlar o acesso aos recursos

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso ao plano de controle do aplicativo de função (o portal do Azure).

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Orientação**: não aplicável; Essa recomendação destina-se a recursos de computação IaaS.

A Microsoft gerencia a infraestrutura subjacente para Azure Functions e implementou controles estritos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Orientação**: ao criar um aplicativo de funções, você deve criar ou vincular a uma conta de armazenamento do Azure de uso geral que dá suporte ao armazenamento de BLOBs, filas e tabelas. Isso ocorre porque o Functions usa o Armazenamento do Azure para operações como gerenciamento de gatilhos e log de execuções de função. O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados de BLOB e arquivo. Essas chaves devem estar presentes no Azure Key Vault para que o aplicativo de funções possa acessar a conta de armazenamento.

- [Entender as considerações de armazenamento para Azure Functions](storage-considerations.md)

- [Entender a criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas para quando as alterações ocorrerem em aplicativos de função de produção, bem como outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretrizes**: Adote uma prática DevSecOps para garantir que seus aplicativos de funções sejam seguros e permaneçam o mais seguro possível durante o ciclo de vida. O DevSecOps incorpora a equipe de segurança de sua organização e seus recursos em suas práticas de DevOps, tornando a segurança uma responsabilidade de todos na equipe.

Além disso, siga as recomendações da central de segurança do Azure para ajudar a proteger seus aplicativos de funções.

- [Como adicionar validação de segurança contínua ao pipeline de CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte a Azure functions, no entanto, você pode usar a severidade das recomendações na central de segurança do Azure, bem como a pontuação segura para medir o risco em seu ambiente. Sua pontuação segura é baseada em quantas recomendações da central de segurança foram atenuadas. Para priorizar as recomendações a serem resolvidas primeiro, considere a gravidade de cada uma.

- [Guia de referência de recomendações de segurança](../security-center/recommendations-reference.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Diretriz**: Use o Azure Resource Graph para consultar/descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos etc.) em suas assinaturas. Configure permissões apropriadas (leitura) no seu locatário e enumere todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

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

Informações adicionais estão disponíveis nos links referenciados.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Defina os recursos aprovados do Azure e o software aprovado para recursos de computação.

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

Informações adicionais estão disponíveis nos links referenciados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Diretrizes**: defina e implemente configurações de segurança padrão para seu aplicativo de funções com Azure Policy. Use aliases de Azure Policy no namespace "Microsoft. Web" para criar políticas personalizadas para auditar ou impor a configuração de seus aplicativos de funções. Você também pode fazer uso de definições de política internas, como:

- A identidade gerenciada deve ser usada no aplicativo de funções

- A depuração remota deve ser desativada para aplicativos de funções

- o aplicativo de funções só deve ser acessível via HTTPS

Informações adicionais estão disponíveis nos links referenciados.

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Diretrizes**: armazene e gerencie modelos ARM e definições de política personalizadas do Azure com segurança no controle do código-fonte.

- [O que é infraestrutura como código](/azure/devops/learn/what-is-infrastructure-as-code)

- [Política de design como fluxos de trabalho de código](../governance/policy/concepts/policy-as-code.md)

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolva também um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Use a política do Azure [auditoria], [negar] e [implantar se não existir] para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidades gerenciadas em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem. Identidades gerenciadas permitem que seu aplicativo de funções se autentique em qualquer serviço que dê suporte à autenticação Azure Active Directory (AD do Azure), incluindo Key Vault, sem nenhuma credencial em seu código.

- [Como criar um Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../app-service/overview-managed-identity.md)

- [Como autenticar-se no Key Vault](../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso de Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Usar referências de Key Vault para o serviço de aplicativo e Azure Functions](../app-service/app-service-key-vault-references.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer ao seu aplicativo de funções uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). As identidades gerenciadas permitem que você se autentique em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.

- [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../app-service/overview-managed-identity.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminar a exposição involuntária de credenciais

**Diretriz**: implemente o verificador de credenciais para identificar credenciais no código. O verificador de credenciais também encorajará a migração de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o verificador de credenciais](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretrizes**: Use o recurso de backup e restauração para agendar backups regulares de seu aplicativo. Os aplicativos de funções em execução no plano Premium têm os mesmos recursos de hospedagem que os aplicativos Web no serviço Azure App, que inclui o recurso "backup e restauração".

Além disso, use uma solução de controle do código-fonte, como Azure Repos e Azure DevOps, para armazenar e gerenciar seu código com segurança. O Azure DevOps Services aproveita muitos dos recursos de armazenamento do Azure para garantir a disponibilidade de dados em caso de falha de hardware, interrupção de serviço ou desastre na região. Além disso, a equipe do Azure DevOps segue os procedimentos para proteger os dados contra exclusão acidental ou mal-intencionada.

- [Fazer backup de seu aplicativo no Azure](../app-service/manage-backup.md)

- [Entender a disponibilidade de dados no Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use o recurso de backup e restauração para agendar backups regulares de seu aplicativo. Os aplicativos de funções em execução no plano Premium têm os mesmos recursos de hospedagem que os aplicativos Web no serviço Azure App, que inclui o recurso "backup e restauração". Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

Além disso, use uma solução de controle do código-fonte, como Azure Repos e Azure DevOps, para armazenar e gerenciar seu código com segurança. O Azure DevOps Services aproveita muitos dos recursos de armazenamento do Azure para garantir a disponibilidade de dados em caso de falha de hardware, interrupção de serviço ou desastre na região. Além disso, a equipe do Azure DevOps segue os procedimentos para proteger os dados contra exclusão acidental ou mal-intencionada.

- [Fazer backup de seu aplicativo no Azure](../app-service/manage-backup.md)

- [Como fazer backup de chaves do cofre de chaves no Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Entender a disponibilidade de dados no Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentação do Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Orientação**: garanta a capacidade de executar a restauração periodicamente do recurso de backup e restauração. Se estiver usando outro local offline para fazer backup de seu código, garanta periodicamente a capacidade de executar restaurações completas. Teste a restauração de chaves de backup gerenciadas pelo cliente.

- [Restaurar um aplicativo no Azure por meio de um backup](../app-service/web-sites-restore.md)

- [Restaurar um aplicativo no Azure a partir de um instantâneo](../app-service/app-service-web-restore-snapshots.md)

- [Como restaurar chaves do cofre de chaves no Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Orientação**: backups do recurso de backup e restauração usam uma conta de armazenamento do Azure em sua assinatura. O Armazenamento do Azure criptografa todos os dados em uma conta de armazenamento em repouso. Por padrão, os dados são criptografados com chaves gerenciadas pela Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer chaves gerenciadas pelo cliente para criptografia de dados de armazenamento.

Se você estiver usando chaves gerenciadas pelo cliente, verifique se Soft-Delete no Key Vault está habilitada para proteger as chaves contra exclusão acidental ou mal-intencionada.

- [Criptografia do Armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Compartilhado

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

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A severidade se baseia na confiança que a Central de Segurança tem na constatação ou na análise usada para emitir o alerta, bem como no nível de confiança de que houve uma ação mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomenclatura para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Compartilhado

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

**Diretrizes**: Use o recurso de automação de fluxo de trabalho na central de segurança do Azure para disparar automaticamente respostas a alertas de segurança e recomendações com aplicativos lógicos.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não sejam violações das políticas da Microsoft. Use a estratégia da Microsoft e a execução de equipes vermelhas e testes de penetração de sites ativos em infraestrutura de nuvem, serviços e aplicativos gerenciados pela Microsoft.

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
