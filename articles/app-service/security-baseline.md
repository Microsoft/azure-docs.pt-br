---
title: Linha de base de segurança do Azure para o serviço de aplicativo
description: A linha de base de segurança do serviço de aplicativo fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7607b1726900423498a58502494c015f62da8829
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559427"
---
# <a name="azure-security-baseline-for-app-service"></a>Linha de base de segurança do Azure para o serviço de aplicativo

A linha de base de segurança do Azure para o serviço de aplicativo contém recomendações que ajudarão você a melhorar a postura de segurança de sua implantação. A linha de base para esse serviço é extraída do [Azure Security Benchmark versão 1.0](../security/benchmarks/overview-v1.md), que fornece recomendações sobre como proteger suas soluções de nuvem no Azure com nossas diretrizes de melhores práticas. O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao serviço de aplicativo. Os **controles** não aplicáveis ao serviço de aplicativo foram excluídos.

Para ver como o serviço de aplicativo é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo completo de mapeamento de linha de base do serviço de aplicativo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Orientação**: ao usar o serviço de aplicativo no tipo de preço isolado, também chamado de ambiente do serviço de aplicativo (ase), você pode implantar diretamente em uma sub-rede dentro de sua rede virtual do Azure. Use grupos de segurança de rede para proteger seus Ambiente do Serviço de Aplicativo do Azure bloqueando o tráfego de entrada e de saída para recursos em sua rede virtual ou para restringir o acesso a aplicativos em um Ambiente do Serviço de Aplicativo.

Por padrão, os grupos de segurança de rede incluem uma regra de negação implícita na prioridade mais baixa e exige que você adicione regras de permissão explícitas. Adicione regras de permissão para o grupo de segurança de rede com base em uma abordagem de rede com privilégios mínimos. As máquinas virtuais subjacentes usadas para hospedar os Ambiente do Serviço de Aplicativo não são acessíveis diretamente porque estão em uma assinatura gerenciada pela Microsoft.

Proteger um Ambiente do Serviço de Aplicativo roteando o tráfego por meio de um WAF (firewall do aplicativo Web) habilitado Aplicativo Azure gateway. Use pontos de extremidade de serviço em conjunto com o gateway de aplicativo para proteger o tráfego de publicação de entrada para seu aplicativo.  

No serviço de aplicativo multilocatário (um aplicativo que não está na camada isolada), use grupos de segurança de rede para bloquear o tráfego de saída de seu aplicativo. Habilite seus aplicativos para acessar recursos no ou por meio de uma rede virtual, com o recurso de integração de rede virtual. Esse recurso também pode ser usado para bloquear o tráfego de saída para endereços públicos do aplicativo. A integração de rede virtual não pode ser usada para fornecer acesso de entrada a um aplicativo.  

Proteja o tráfego de entrada para seu aplicativo com:
- Restrições de acesso – uma série de regras de permissão ou negação que controlam o acesso de entrada
- Pontos de extremidade de serviço – podem negar tráfego de entrada de fora de redes virtuais ou sub-redes especificadas
- Pontos de extremidade privados – exponha seu aplicativo à sua rede virtual com um endereço IP privado. Com os pontos de extremidade privados habilitados em seu aplicativo, ele não é mais acessível à Internet

Ao usar o recurso de integração de rede virtual com redes virtuais na mesma região, use grupos de segurança de rede e tabelas de rotas com rotas definidas pelo usuário. As rotas definidas pelo usuário podem ser colocadas na sub-rede de integração para enviar o tráfego de saída como pretendido.  

Considere implementar um firewall do Azure para criar, impor e registrar de forma centralizada políticas de conectividade de aplicativos e de rede em suas assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para recursos de rede virtual, o que permite que os firewalls externos identifiquem o tráfego originado de sua rede virtual. 

- [Bloqueando um Ambiente do Serviço de Aplicativo](environment/firewall-integration.md)

- [Abrir o OWASP (projeto de segurança de aplicativo Web) principais 10 vulnerabilidades proteção](https://owasp.org/www-project-top-ten/)

- [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md)

- [Integrar seu aplicativo a uma rede virtual do Azure](web-sites-integrate-with-vnet.md)

- [Considerações de rede para um Ambiente do Serviço de Aplicativo](environment/network-info.md)

- [Como criar um ASE externo](environment/create-external-ase.md)

- [Como criar um ASE interno](environment/create-ilb-ase.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Orientação**: implementar recomendações de proteção de rede da central de segurança do Azure para proteger recursos de rede e configurações relacionadas aos aplicativos e APIs do serviço de aplicativo.

Use o Firewall do Azure para enviar tráfego e criar, impor e registrar políticas de conectividade de aplicativo e de rede de forma centralizada em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, o que permite que os firewalls externos identifiquem o tráfego originado de sua rede virtual. O serviço de firewall do Azure também é totalmente integrado com Azure Monitor para registro em log e análise.

- [Visão geral do firewall do Azure](../firewall/overview.md)

- [Entender a segurança de rede fornecida pela central de segurança do Azure](../security-center/security-center-network-recommendations.md)

- [Como habilitar o monitoramento e a proteção do serviço de aplicativo](../security-center/defender-for-app-service-introduction.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: proteger aplicativos Web críticos

**Diretrizes**: proteger um aplicativo acessível pela Internet em um ambiente do serviço de aplicativo (ase) por:
- Implantando um WAF (firewall do aplicativo Web) com Aplicativo Azure gateway na frente de um aplicativo voltado para a Internet
- Usar restrições de acesso para proteger o tráfego de entrada para o gateway de aplicativo
- Proteger o aplicativo com o Azure Active Directory (AD do Azure) para garantir a autenticação
- Defina a versão mínima do TLS como 1,2
- Definir o aplicativo como somente HTTPS

Conduza toda a saída do tráfego de aplicativo por meio de um dispositivo de firewall do Azure e monitore os logs. 

Para proteger um aplicativo acessível pela Internet no serviço de aplicativo multilocatário (como, não na camada isolada)
- Implantar um dispositivo habilitado para firewall do aplicativo Web na frente de um aplicativo
- Usar restrições de acesso ou pontos de extremidade de serviço para proteger o tráfego de entrada para o dispositivo WAF (firewall do aplicativo Web)
- Proteger o aplicativo com o Azure AD para garantir a autenticação
- Defina a versão mínima do TLS como 1,2
- Definir o aplicativo como somente HTTPS
- Use a integração de rede virtual e a configuração do aplicativo WEBSITE_VIRTUAL NETWORK_ROUTE_ALL para fazer todo o tráfego de saída sujeito a grupos de segurança de rede e rotas definidas pelo usuário na sub-rede de integração.

Semelhante ao aplicativo de ambiente do serviço de aplicativo, conduza toda a saída do tráfego de aplicativo por meio de um dispositivo de firewall do Azure e monitore os logs no aplicativo.

Além disso, examine e siga as recomendações no documento bloquear um Ambiente do Serviço de Aplicativo.

- [Bloqueando um Ambiente do Serviço de Aplicativo](environment/firewall-integration.md)

- [Firewall do aplicativo Web do Azure no gateway de Aplicativo Azure](../web-application-firewall/ag/ag-overview.md)

- [Azure App restrições de acesso de serviço](app-service-ip-restrictions.md)

- [Acompanhe alertas do WAF e monitore facilmente as tendências com Azure Monitor ](../azure-monitor/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretrizes**: proteja o ambiente do serviço de aplicativo conforme descrito no bloqueando uma documentação de ambiente do serviço de aplicativo. Aplique a funcionalidade de inteligência de ameaças integrada na central de segurança do Azure para negar comunicações com endereços IP públicos mal-intencionados ou não utilizados. Use restrições de acesso para proteger o tráfego de entrada para o gateway de aplicativo. 

Proteger o serviço de aplicativo multilocatário (um aplicativo que não está em uma camada isolada), com um ponto de extremidade público para a Internet. Ele permite o tráfego somente de uma sub-rede específica dentro de sua rede virtual e bloqueia todo o resto. Use restrições de acesso para configurar listas de controle de acesso à rede (restrições de IP) para bloquear o tráfego de entrada permitido.

Defina a prioridade entre a lista ordenada de permitir ou negar para gerenciar o acesso à rede para seu aplicativo. Essa lista pode incluir endereços IP ou sub-redes de rede virtual. Uma regra "negar tudo" implícita existe no final da lista quando ela contém uma ou mais entradas. Esse recurso funciona com todas as cargas de trabalho hospedadas do serviço de aplicativo, incluindo aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções. 

Use pontos de extremidade de serviço para restringir o acesso ao seu aplicativo Web de uma rede virtual do Azure. Limite o acesso a um serviço de aplicativo multilocatário (um aplicativo que não está em uma camada isolada), de sub-redes selecionadas com pontos de extremidade de serviço. 

- [Restrições de IP estático do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md)

- [Firewall do aplicativo Web do Azure no gateway de Aplicativo Azure](../web-application-firewall/ag/ag-overview.md)

- [Como configurar um WAF (firewall do aplicativo Web) para Ambiente do Serviço de Aplicativo](environment/app-service-app-service-environment-web-application-firewall.md)

- [Proteja o ASE conforme descrito em bloqueando um Ambiente do Serviço de Aplicativo](environment/firewall-integration.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Orientação**: monitora solicitações e respostas enviadas de e para aplicativos do serviço de aplicativo com a central de segurança. Os ataques contra um aplicativo Web podem ser monitorados usando um gateway de aplicativo em tempo real com o Firewall do aplicativo Web, habilitado com o registro em log integrado de Azure Monitor para rastrear alertas de firewall do aplicativo Web e monitorar facilmente as tendências.

- [Firewall do aplicativo Web Azure no Gateway de Aplicativo do Azure](../web-application-firewall/ag/ag-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas-Microsoft. Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gerenciar o tráfego para aplicativos Web

**Orientação**: gerenciar o tráfego de um aplicativo em um ambiente do serviço de aplicativo:

- Proteja o Ambiente do Serviço de Aplicativo conforme descrito em bloquear um Ambiente do Serviço de Aplicativo
- Implantar um gateway de aplicativo que tenha um firewall do aplicativo Web do Azure na frente de seus aplicativos voltados para a Internet
- Defina o aplicativo como somente acessível via HTTPS

Gerenciar o tráfego para um aplicativo acessível pela Internet no serviço de aplicativo multilocatário (não na camada isolada): 

- Implantar um gateway de aplicativo que tenha o Firewall do aplicativo Web do Azure habilitado na frente de seus aplicativos para a Internet
- Use restrições de acesso ou pontos de extremidade de serviço para proteger o tráfego de entrada para o Firewall do aplicativo Web. O recurso de restrições de acesso funciona com todas as cargas de trabalho hospedadas do serviço de aplicativo, incluindo aplicativos Web, aplicativos de API, aplicativos do Linux, aplicativos de contêiner do Linux e funções.

- Defina o aplicativo como acessível somente via HTTPS
- Limite o acesso ao aplicativo do serviço de aplicativo com restrições de IP estático para que ele só receba o tráfego do VIP em um gateway de aplicativo como o único endereço com acesso.

Examine os links referenciados para obter informações adicionais.

- [Restrições de IP estático do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md)

- [Firewall do aplicativo Web do Azure no gateway de Aplicativo Azure](../web-application-firewall/ag/ag-overview.md)

- [Como configurar o TLS de ponta a ponta usando o gateway de aplicativo com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Proteger o ASE conforme descrito em bloqueando um serviço de aplicativo](/azure/app-service/environment/firewall-integration)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Orientação**: o serviço de aplicativo tem um número de pontos de extremidade que são usados para gerenciar o serviço. Esses endereços de ponto de extremidade também estão incluídos na marca de serviço de IP AppServiceManagement. A marca AppServiceManagement é usada apenas com um Ambiente do Serviço de Aplicativo para permitir esse tráfego. 

Você pode permitir ou negar o tráfego para o serviço correspondente especificando o nome da marca de serviço no campo de origem ou destino apropriado de uma regra. Os endereços de entrada do serviço de aplicativo são acompanhados na marca de serviço de IP AppService. Não há nenhuma marca de serviço IP que contenha os endereços de saída usados pelo serviço de aplicativo.

A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

- [Marcas de serviço de rede virtual](../virtual-network/service-tags-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: manter configurações de segurança padrão para dispositivos de rede

**Orientação**: definir e implementar configurações de segurança padrão para as configurações de rede relacionadas aos seus aplicativos do serviço de aplicativo. 

Mantenha as configurações de segurança usando aliases de Azure Policy nos namespaces "Microsoft. Web" e "Microsoft. Network". Crie políticas personalizadas para auditar ou impor a configuração de rede de seus aplicativos do serviço de aplicativo. 

Use definições de política internas para o serviço de aplicativo, como:
- O aplicativo deve usar um ponto de extremidade de serviço de rede virtual
- O aplicativo só deve ser acessível via HTTPS
- Definir a versão mínima do TLS para a versão atual

Examine os links referenciados para obter informações adicionais.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como configurar o TLS de ponta a ponta usando o gateway de aplicativo com o portal](../application-gateway/end-to-end-ssl-portal.md)

- [Proteger o ASE conforme descrito em bloqueando um serviço de aplicativo](/azure/app-service/environment/firewall-integration)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentar regras de configuração de tráfego

**Diretrizes**: use marcas para grupos de segurança de rede e outros recursos relacionados, incluindo o fluxo de tráfego no serviço de aplicativo.

Especifique a necessidade comercial, a duração e assim por diante, com o campo "Descrição" para qualquer regra, que permita o tráfego de ou para uma rede para regras de grupos de segurança de rede individuais.

Aplique qualquer uma das definições de Azure Policy internas relacionadas a efeitos de marcação, como "exigir marca e seu valor", para garantir que todos os recursos sejam criados com marcas e notificá-lo de quaisquer recursos não marcados existentes. Use Azure PowerShell ou CLI do Azure para pesquisar ou executar ações em recursos com base em suas marcas.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

- [Azure App restrições de acesso de serviço](/azure/app-service/app-service-ip-restrictions)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Orientação**: Use o log de atividades do Azure para monitorar as configurações de recursos de rede e detectar alterações nas configurações de rede e em todos os recursos relacionados ao serviço de aplicativo. 

Aplique uma das várias definições de Azure Policy internas para o serviço de aplicativo, como uma política que audita aplicativos para o uso do serviço de ponto de extremidade de rede virtual. Crie alertas dentro de Azure Monitor para disparar quando ocorrerem alterações em configurações de rede ou recursos críticos. 

Examine os alertas de segurança e recomendações detalhadas na central de segurança, no portal ou por meio de ferramentas programáticas. Exporte essas informações ou envie-as para outras ferramentas de monitoramento em seu ambiente. As ferramentas estão disponíveis para exportar alertas e recomendações de maneira manual ou contínua. Com essas ferramentas, você pode:
 
- Exportar continuamente para um espaço de trabalho Log Analytics
- Exportar continuamente para os hubs de eventos do Azure (para integrações com SIEMs de terceiros)
- Exportar para um arquivo CSV (uma vez)

É recomendável que você crie um processo com ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar rapidamente as alterações.

- [Como exibir e recuperar eventos do log de atividades do Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Como criar alertas no Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Exporte alertas e recomendações](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurar o gerenciamento central de log de segurança

**Diretrizes**: integre seu ambiente do serviço de aplicativo (ase) com Azure monitor para enviar logs para o armazenamento do Azure, hubs de eventos do azure ou log Analytics. Habilitar configurações de diagnóstico do log de atividades do Azure para log de auditoria do plano de controle. Os alertas de segurança da central de segurança são publicados no log de atividades do Azure. Auditar dados do log de atividades do Azure, que permite determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) executada no nível do plano de controle para Azure App serviço e outros recursos do Azure. Salve suas consultas para uso futuro, fixe os resultados da consulta nos painéis do Azure e crie alertas de log. Além disso, use a API REST de acesso a dados no Application Insights para acessar sua telemetria de forma programática.

Use Microsoft Azure Sentinel, um SIEM (gerenciamento de eventos de informações de segurança) escalonável, baseado em nuvem, disponível para se conectar a várias fontes de dados e conectores, com base em seus requisitos de negócios. Você também pode habilitar e integrar dados a um sistema SIEM (gerenciamento de eventos de informações de segurança) de terceiros, como o Barracuda no Azure Marketplace.

- [Registrando a atividade do ASE](./environment/using-an-ase.md#logging)

- [Como habilitar as configurações de diagnóstico para o serviço Azure App](troubleshoot-diagnostic-logs.md)

- [Como habilitar Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Exportar telemetria do Application Insights](../azure-monitor/app/export-telemetry.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: habilitar o registro em log de auditoria para recursos do Azure

**Diretrizes**: habilitar as configurações de diagnóstico do log de atividades do Azure para o log de auditoria do plano de controle do serviço de aplicativo. Envie os logs para um espaço de trabalho Log Analytics, Hub de eventos do Azure ou uma conta de armazenamento do Azure.

O "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executado no nível do plano de controle pode ser determinado usando os dados do log de atividades do Azure para o serviço de aplicativo e outros recursos do Azure.

Além disso, o Azure Key Vault fornece gerenciamento de segredos centralizado com políticas de acesso e histórico de auditoria. 

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como habilitar as configurações de diagnóstico para o serviço Azure App](troubleshoot-diagnostic-logs.md)

- [Operações do Resource Manager](../role-based-access-control/resource-provider-operations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurar a retenção de armazenamento do log de segurança

**Orientação**: em Azure monitor, defina o período de retenção de log para os espaços de trabalho de log Analytics associados aos recursos do serviço de aplicativo de acordo com os regulamentos de conformidade da sua organização.
- [Como definir parâmetros de retenção de log](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretrizes**: examine as configurações de diagnóstico do log de atividades do Azure em seus recursos do serviço de aplicativo com os logs que estão sendo enviados a um espaço de trabalho do log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados.

Use Application Insights para seus aplicativos do serviço de aplicativo e para coletar dados de log, desempenho e erro. Exiba os dados de telemetria coletados por Application Insights no portal do Azure.

Se você tiver implantado um WAF (firewall do aplicativo Web), poderá monitorar ataques em seus aplicativos do serviço de aplicativo usando um log de firewall do aplicativo Web em tempo real. O log é integrado com Azure Monitor para rastrear alertas de firewall do aplicativo Web e monitorar facilmente as tendências.

Use o Azure Sentinel, um SIEM (gerenciamento de eventos de informações de informação de segurança) escalonável e nativo de nuvem, para integrar com várias fontes de dados e conectores, de acordo com os requisitos. Opcionalmente, habilite e integre dados a uma solução de gerenciamento de eventos de informações de segurança de terceiros no Azure Marketplace.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](../azure-monitor/essentials/activity-log.md)

- [Como habilitar Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Como integrar seu Ambiente do Serviço de Aplicativo com o gateway de Aplicativo Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretrizes**: Configure a central de segurança em sua assinatura do Azure e examine os alertas gerados. Use Azure Monitor para obter os dados do log de atividades para um hub de eventos, no qual ele pode ser lido por uma solução SIEM (gerenciamento de eventos de informações de segurança), como o Azure Sentinel. 

Monitore ataques contra seus aplicativos do serviço de aplicativo usando um log de firewall do aplicativo Web em tempo real com um WAF (firewall do aplicativo Web) do Azure implantado. O log é integrado com Azure Monitor para rastrear alertas do WAF (firewall do aplicativo Web) e monitorar facilmente as tendências.

- [Como integrar seu Ambiente do Serviço de Aplicativo com o gateway de Aplicativo Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Exporte alertas e recomendações](../security-center/continuous-export.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: manter um inventário de contas administrativas

**Diretrizes**: Azure Active Directory (Azure AD) tem funções internas que devem ser explicitamente atribuídas e capazes de consultar. Use o módulo do PowerShell do Azure AD para executar consultas ad hoc para descobrir contas que são membros de grupos administrativos.

- [Como obter membros de uma função de diretório no Azure AD com o PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: alterar senhas padrão quando aplicável

**Diretrizes**: Azure Active Directory (Azure AD) não tem o conceito de senhas padrão. Ele fornece acesso ao plano de controle ao serviço de aplicativo.

Em geral, evite implementar senhas padrão para acesso de usuário ao compilar seus próprios aplicativos. Use um dos provedores de identidade disponíveis por padrão para o serviço de aplicativo, como o Azure AD, conta da Microsoft, Facebook, Google ou Twitter.

Desabilite o acesso anônimo, a menos que você precise dar suporte a ele. 

- [Provedores de identidade disponíveis por padrão no serviço Azure App](./overview-authentication-authorization.md#identity-providers)

- [Autenticação e autorização no serviço Azure App e Azure Functions](overview-authentication-authorization.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use os recursos de gerenciamento de acesso e identidade na central de segurança para monitorar e acompanhar o número de contas administrativas. 

Use as recomendações da central de segurança ou de políticas internas do Azure, como:

- Deve haver mais de um proprietário atribuído à sua assinatura. 
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

Crie um processo para monitorar as configurações de recursos de rede e detectar alterações em contas administrativas.

- [Como usar a central de segurança do Azure para monitorar a identidade e o acesso](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Saiba mais sobre como conceder acesso a aplicativos aos usuários](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: autentique o serviço de aplicativo por meio do Azure Active Directory (AD do Azure). Ele fornece um serviço OAuth 2,0 para seu provedor de identidade e permite o acesso autorizado a aplicativos móveis e Web. 

Os aplicativos do serviço de aplicativo usam a identidade federada, na qual um provedor de identidade de terceiros gerencia as identidades de usuário e o fluxo de autenticação para você. Esses provedores de identidade estão disponíveis por padrão:

- Azure AD
- Conta da Microsoft

- Facebook

- Google

- Twitter

Ao habilitar a autenticação e autorização com um desses provedores, seu ponto de extremidade de logon estará disponível para autenticação de usuário e validação de tokens de autenticação do provedor.

- [Entender a autenticação e a autorização no serviço Azure App](./overview-authentication-authorization.md#identity-providers)

- [Saiba mais sobre autenticação e autorização no serviço Azure App](overview-authentication-authorization.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usar a autenticação multifator para todo o acesso baseado em Azure Active Directory

**Diretrizes**: habilite o recurso de autenticação multifator no Azure Active Directory (Azure AD) e siga as recomendações de gerenciamento de identidade e acesso na central de segurança.

Implementar a autenticação multifator para o Azure AD. Os administradores precisam garantir que as contas de assinatura no portal sejam protegidas. A assinatura é vulnerável a ataques porque gerencia os recursos que você criou. 

- [Autenticação multifator de segurança do Azure](/previous-versions/azure/security/develop/secure-aad-app)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Como monitorar identidade e acesso na Central de Segurança do Azure](../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usar estações de trabalho seguras e gerenciadas pelo Azure para tarefas administrativas

**Diretrizes**: Use Paw (estações de trabalho com acesso privilegiado) com a autenticação multifator configurada para fazer logon e configurar os recursos do Azure. 

- [Saiba mais sobre Estações de Trabalho com Acesso Privilegiado](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Como habilitar a autenticação multifator no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrar em log e alertar sobre atividades suspeitas de contas administrativas

**Orientação**: Use PRIVILEGED Identity Management (PIM) no Azure Active Directory (Azure AD) para a geração de logs e alertas quando atividades suspeitas ou não seguras ocorrem no ambiente.

Além disso, use as detecções de risco do Azure Active Directory para ver alertas e relatórios sobre o comportamento do usuário suspeito.

A proteção contra ameaças na central de segurança fornece defesas abrangentes para seu ambiente, o que inclui proteção contra ameaças para recursos de computação do Azure, como máquinas com Windows, máquinas Linux, serviço de aplicativo e contêineres do Azure.

- [Como implantar o Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Entenda as detecções de risco do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças para recursos de computação do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gerenciar recursos do Azure provenientes somente de locais aprovados

**Orientação**: use locais nomeados de acesso condicional para permitir o acesso ao portal do Azure apenas de agrupamentos lógicos específicos de intervalos de endereços IP, países ou regiões.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos do serviço de aplicativo. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito e também Salts, hashes e armazena com segurança as credenciais do usuário.

- [Como configurar seus aplicativos de serviço de Azure App para usar o logon do Azure AD](configure-authentication-provider-aad.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Orientação**: descobrir contas obsoletas com os logs fornecidos pelo Azure Active Directory (Azure AD). Use as revisões de acesso de identidade do Azure para gerenciar com eficiência as associações de grupo e o acesso a aplicativos empresariais, bem como atribuições de função. Examine o acesso do usuário periodicamente para garantir que apenas os usuários pretendidos tenham acesso contínuo. 

- [Entender os relatórios do Azure AD](../active-directory/reports-monitoring/index.yml)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos do serviço de aplicativo. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito, Salts, hashes e armazena com segurança as credenciais do usuário.

O acesso à atividade de entrada do Azure AD, auditoria e fontes de log de eventos de risco permite que você se integre ao Azure Sentinel ou a uma solução de SIEM (gerenciamento de eventos de informações de segurança) de terceiros. Simplifique o processo criando configurações de diagnóstico para contas de usuário do Azure AD e enviando os logs de auditoria e de logon para um espaço de trabalho Log Analytics. Os alertas de log desejados podem ser configurados no Log Analytics.

- [Como configurar seus aplicativos de serviço de Azure App para usar o logon do Azure AD](configure-authentication-provider-aad.md)

- [Como integrar os logs de atividades do Azure ao Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: Use Azure Active Directory (AD do Azure) como o sistema de autenticação e autorização central para seus aplicativos do serviço de aplicativo. 

Use Azure AD Identity Protection para configurar respostas automatizadas para ações suspeitas detectadas relacionadas a identidades de usuário, como o desvio do comportamento de logon da conta no plano de controle com o portal do Azure. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada. 

- [Como configurar seu aplicativo de serviço de Azure App para usar o logon do Azure AD](configure-authentication-provider-aad.md)

- [Como exibir entradas suspeitas do Azure Active Directory](../active-directory/identity-protection/overview-identity-protection.md)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Orientação**: não disponível; Não há suporte para Sistema de Proteção de Dados do Cliente para Azure App Service.

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: manter um inventário de informações confidenciais

**Diretrizes**: use marcas para ajudar a controlar os recursos do serviço de aplicativo que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretrizes**: para obter uma ambiente do serviço de aplicativo, implemente assinaturas separadas, grupos de gerenciamento ou ambos, para ambientes de desenvolvimento, teste e produção. Você pode isolar aplicativos que processam informações confidenciais de outros aplicativos da mesma maneira. Implante seu aplicativo do serviço de aplicativo em uma rede virtual. Use grupos de segurança de rede e sub-redes para isolamento adicional de aplicativo. 

Há dois tipos de implantação para um ambiente do serviço de aplicativo (ASE). Ambos permitem isolar o tráfego com base em seus requisitos de negócios.

- Ambiente de serviço de aplicativo externo-expõe o Ambiente do Serviço de Aplicativo aplicativos hospedados em um endereço IP acessível pela Internet.

-  Ambiente do serviço de aplicativo ILB (balanceador de carga interno)-expõe o Ambiente do Serviço de Aplicativo aplicativos hospedados em um endereço IP dentro de sua rede virtual. O ponto de extremidade interno é um ILB (balanceador de carga interno), motivo pelo qual ele é chamado de ASE ILB. 

Para o serviço de aplicativo multilocatário (um aplicativo que não está na camada isolada), use a integração de rede virtual para o acesso do seu aplicativo aos recursos em sua rede virtual.  Use o acesso ao site privado para tornar um aplicativo acessível somente de uma rede privada, como uma de dentro de uma rede virtual do Azure. A integração de rede virtual é usada apenas para fazer chamadas de saída de seu aplicativo para sua rede virtual. O recurso de integração de rede virtual se comporta de maneira diferente quando é usado com uma rede virtual na mesma região e com redes virtuais em outras regiões. 
 
- [Considerações de rede para um Ambiente do Serviço de Aplicativo](environment/network-info.md)

- [Como criar um ASE externo](environment/create-external-ase.md)

- [Como criar um ASE interno](environment/create-ilb-ase.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorar e bloquear a transferência não autorizada de informações confidenciais

**Orientação**: embora os recursos de identificação de dados, classificação e prevenção de perda ainda não estejam disponíveis para o serviço de aplicativo, você pode reduzir os dados vazamentondo o risco da rede virtual removendo todas as regras em que o destino usa uma "marca" para serviços da Internet ou do Azure. 

A Microsoft gerencia a infraestrutura subjacente para o serviço de aplicativo e implementou controles estritos para evitar a perda ou a exposição de seus dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: criptografar todas as informações confidenciais em trânsito

**Orientação**: Use a versão mínima padrão do TLS 1,2, configurada nas configurações de TLS/SSL, para criptografar todas as informações em trânsito. Além disso, certifique-se de que todas as solicitações de conexão HTTP sejam redirecionadas para HTTPS.

- [Entender a criptografia em trânsito para aplicativos Web do serviço Azure App](security-recommendations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: o [benchmark de segurança do Azure](/azure/governance/policy/samples/azure-security-benchmark) é a iniciativa de política padrão para a central de segurança e é a base para as [recomendações da central de segurança](/azure/security-center/security-center-recommendations). As definições de Azure Policy relacionadas a esse controle são habilitadas automaticamente pela central de segurança. Os alertas relacionados a esse controle podem exigir um plano do [Azure defender](/azure/security-center/azure-defender) para os serviços relacionados.

**Azure Policy definições internas – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: não disponível no momento. Os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o serviço de aplicativo. 

Marque aplicativos de serviço de aplicativo que podem estar processando informações confidenciais. Implemente uma solução de terceiros, se necessário, para fins de conformidade.

A Microsoft gerencia a plataforma subjacente e trata todos os dados do cliente como confidenciais e leva a um grande comprimento para se proteger contra a perda e a exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) no Azure Active Directory (Azure AD) para controlar o acesso ao plano de controle do serviço de aplicativo na portal do Azure.

- [Como configurar o RBAC do Azure](../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: criptografar informações confidenciais em repouso

**Diretrizes**: o conteúdo do site em um aplicativo do serviço de aplicativo, como arquivos, é armazenado no armazenamento do Azure, que criptografa automaticamente o conteúdo em repouso. Escolha armazenar segredos do aplicativo no Key Vault e recuperá-los em tempo de execução.

Os segredos fornecidos pelo cliente são criptografados em repouso enquanto estão armazenados nos bancos de dados de configuração do serviço de aplicativo.

Observe que, embora os discos anexados localmente possam ser usados opcionalmente por sites como armazenamento temporário (por exemplo, D:\Local e% TMP%), eles não são criptografados em repouso.

- [Entender os controles de proteção de dados para Azure App serviço](./security-recommendations.md#data-protection)

- [Entender a criptografia de armazenamento do Azure em repouso](../storage/common/storage-service-encryption.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrar e alertar sobre alterações em recursos críticos do Azure

**Diretrizes**: Use Azure monitor com o log de atividades do Azure para criar alertas sobre quaisquer alterações nos aplicativos do serviço de aplicativo de produção e outros recursos importantes ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: executar ferramentas automatizadas de verificação de vulnerabilidade

**Orientação**: Adote uma prática DevSecOps para garantir que seus aplicativos do serviço de aplicativo sejam seguros e permaneçam protegidos durante todo o ciclo de vida. O DevSecOps incorpora a equipe de segurança de sua organização e seus recursos em suas práticas de DevOps, tornando a segurança a responsabilidade de todos na equipe.

Examine e siga as recomendações da central de segurança para proteger seus aplicativos do serviço de aplicativo.

- [Como adicionar validação de segurança contínua ao pipeline de CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Como implementar recomendações de avaliação de vulnerabilidade da central de segurança do Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretrizes**: a Microsoft executa o gerenciamento de vulnerabilidades nos sistemas subjacentes que dão suporte ao serviço de aplicativo. No entanto, você pode usar a severidade das recomendações na central de segurança, bem como a pontuação segura para medir o risco em seu ambiente. Sua pontuação segura é baseada em quantas recomendações da central de segurança foram atenuadas. Para priorizar as recomendações a serem resolvidas primeiro, considere a gravidade de cada uma.

- [Guia de referência de recomendações de segurança](../security-center/recommendations-reference.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="inventory-and-asset-management"></a>Inventário e gerenciamento de ativos

*Para obter mais informações, consulte o [benchmark de segurança do Azure: inventário e gerenciamento de ativos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usar solução de descoberta de ativos automatizada

**Orientação**: Use o grafo de recursos do Azure para consultar ou descobrir todos os recursos (como computação, armazenamento, rede, portas, protocolos e assim por diante) em suas assinaturas. Verifique se as permissões apropriadas são aplicadas ao seu locatário e se você pode enumerar todas as assinaturas do Azure, bem como os recursos em suas assinaturas.

Embora os recursos clássicos do Azure possam ser descobertos por meio do Resource Graph, é altamente recomendável criar e usar recursos do Azure Resource Manager no futuro.

- [Como criar consultas com o Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Como exibir suas assinaturas do Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Entender o RBAC do Azure](../role-based-access-control/overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="62-maintain-asset-metadata"></a>6.2: manter metadados de ativo

**Diretrizes**: aplique marcas aos recursos do Azure usando metadados para organizá-los logicamente em uma taxonomia.

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: excluir recursos do Azure não autorizados

**Orientação**: use marcação, grupos de gerenciamento e assinaturas separadas conforme apropriado, para organizar e controlar os recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam removidos das assinaturas como parte desse processo.

Escolha Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente, usando as seguintes definições de política interna:

- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Examine os links referenciados para obter informações adicionais.

- [Como criar assinaturas adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](../azure-resource-manager/management/tag-resources.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretrizes**: Crie um inventário dos recursos aprovados do Azure e do software aprovado para recursos de computação com base nas suas necessidades organizacionais.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorar recursos do Azure não aprovados

**Diretrizes**: Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas.

Use o grafo de recursos do Azure para consultar ou descobrir recursos em suas assinaturas.  Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. 

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretrizes**: Use o grafo de recursos do Azure para consultar ou descobrir recursos em suas assinaturas e garantir que os recursos do Azure descobertos sejam aprovados com base em suas políticas organizacionais.

Use trabalhos Web no serviço de aplicativo para monitorar aplicativos de software não aprovados que são implantados nos recursos de computação. Use trabalhos Web para executar um programa ou script na mesma instância que um aplicativo, aplicativo de API ou aplicativo móvel. Defina configurações e monitoramento do WebJob com logs. Na página Detalhes da Execução do WebJob, selecione Alternar Saída para ver o texto do conteúdo do log. Observe que os trabalhos Web ainda não têm suporte para o serviço de aplicativo no Linux.

- [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](webjobs-create.md)

- [Tutorial – criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Início rápido – executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: remover recursos e aplicativos de software não aprovados do Azure

**Orientação**: Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. Remova todos os aplicativos de software implantados que não foram aprovados de acordo com suas políticas organizacionais.

- [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](webjobs-create.md)

- [Tutorial – criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Início rápido – executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Orientação**: Verifique se todos os recursos do Azure presentes no ambiente foram aprovados. Use Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas. Remova todos os aplicativos de software implantados que não foram aprovados de acordo com suas políticas organizacionais. 

- [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](webjobs-create.md)

- [Tutorial – criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md)

- [Início rápido – executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretrizes**: Crie um processo para examinar os serviços do Azure não autorizados periodicamente para garantir que somente os serviços do Azure autorizados sejam usados em suas assinaturas.

Use o grafo de recursos do Azure, dentro desse processo, para consultar ou descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

Configure Azure Policy para colocar restrições no tipo de recursos que podem ser criados em suas assinaturas usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Use trabalhos Web no serviço de aplicativo para monitorar aplicativos de software não aprovados implantados em recursos do computador. Use trabalhos Web para executar um programa ou script na mesma instância que um aplicativo, aplicativo de API ou aplicativo móvel. Defina configurações e monitoramento do WebJob com logs. Na página Detalhes da Execução do WebJob, selecione Alternar Saída para ver o texto do conteúdo do log. Observe que os trabalhos Web ainda não têm suporte para o serviço de aplicativo no Linux.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](webjobs-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretrizes**: implemente um processo para inventariar e examinar títulos de software em suas assinaturas periodicamente para garantir que somente os serviços do Azure autorizados sejam usados em suas assinaturas.

Use o grafo de recursos do Azure, dentro desse processo, para consultar ou descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure descobertos no ambiente foram aprovados.

Configure Azure Policy para colocar restrições no tipo de recursos que podem ser criados em assinaturas de cliente usando as seguintes definições de política interna:

- Tipos de recursos não permitidos

- Tipos de recursos permitidos

Da mesma forma, use trabalhos Web no serviço de aplicativo para inventariar aplicativos de software não aprovados implantados em recursos do computador. Defina a configuração e o monitoramento com logs. Na página Detalhes da Execução do WebJob, selecione Alternar Saída para ver o texto do conteúdo do log. Observe que os trabalhos Web ainda não têm suporte para o serviço de aplicativo no Linux.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Orientação**: Configure o acesso condicional do Azure para limitar a capacidade de os usuários interagirem com Azure Resource Manager, configurando "bloquear acesso" para o aplicativo de "gerenciamento de Microsoft Azure".

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretrizes**: trabalhos Web no serviço de aplicativo permitem que os clientes executem um programa ou script na mesma instância que um aplicativo Web, aplicativo de API ou aplicativo móvel. Você é responsável por definir sua configuração para restringir ou limitar scripts, que não são permitidos pela organização. O serviço de aplicativo não fornece um mecanismo para limitar a execução de script nativamente. Observe que os trabalhos Web ainda não têm suporte para o serviço de aplicativo no Linux.

- [Executar tarefas em segundo plano com trabalhos Web no serviço Azure App](webjobs-create.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretrizes**: implemente assinaturas ou grupos de gerenciamento separados para fornecer isolamento para aplicativos de serviço de aplicativo de alto risco. Implante um aplicativo de risco mais alto em sua própria rede virtual, uma vez que a segurança do perímetro no serviço de aplicativo é obtida por meio do uso de redes virtuais. O Ambiente do Serviço de Aplicativo é uma implantação do serviço de aplicativo em uma sub-rede em sua rede virtual do Azure. 

Há dois tipos de ambiente de serviço de aplicativo, ambiente de serviço de aplicativo externo e ambiente de serviço de aplicativo ILB (interno Load Balancer). Escolha a melhor arquitetura com base em seus requisitos.

- [Considerações de rede para um Ambiente do Serviço de Aplicativo](environment/network-info.md)

- [Como criar um ambiente externo do Serviço de Aplicativo](environment/create-external-ase.md)

- [Criar e usar um Ambiente do Serviço de Aplicativo de Balanceador de Carga Interno](environment/create-ilb-ase.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: estabelecer configurações seguras para todos os recursos do Azure

**Orientação**: definir e implementar configurações de segurança padrão para seus aplicativos implantados no serviço de aplicativo com o Azure Policy.

Use Azure Policy aliases no namespace "Microsoft. Web" para criar políticas personalizadas para auditar ou impor a configuração de seus aplicativos Web do serviço de aplicativo.

Aplique definições de política internas, como:

- O Serviço de Aplicativo deve usar um ponto de extremidade de serviço de rede virtual

- Os aplicativos Web só devem ser acessíveis via HTTPS

- Usar a versão mais recente do TLS em seus aplicativos

É recomendável que você documente o processo para aplicar as definições de política internas para uso padronizado.   

- [Como exibir os aliases disponíveis do Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: manter configurações seguras de recursos do Azure

**Orientação**: Use os efeitos de Azure Policy [Deny] e [implantar se não existir] para impor configurações seguras em seus aplicativos de serviço de Azure app.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: armazenar configuração de recursos do Azure com segurança

**Orientação**: escolha Azure DevOps ou Azure Repos para armazenar e gerenciar com segurança seu código ao usar definições de Azure Policy personalizadas.

Use seu pipeline de integração contínua (CI) e entrega contínua (CD) existente para implantar uma configuração segura.

- [Como armazenar código no Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Documentação do Azure Repos](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. Desenvolver um processo e um pipeline para gerenciar exceções de política.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Orientação**: Use definições de Azure Policy internas, bem como aliases de Azure Policy no namespace "Microsoft. Web" para criar políticas personalizadas para alertar, auditar e impor configurações do sistema. 

Aplique Azure Policy [auditoria], [negar] e [implantar se não existir], efeitos para impor automaticamente as configurações para os recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="711-manage-azure-secrets-securely"></a>7.11: gerenciar segredos do Azure com segurança

**Diretrizes**: Use identidades gerenciadas para fornecer aos aplicativos do serviço de aplicativo uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que seus aplicativos se autentiquem em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código. Verifique se a exclusão reversível está habilitada no Azure Key Vault.

- [Como habilitar a exclusão reversível no Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Como usar identidades gerenciadas para o serviço de aplicativo](overview-managed-identity.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Diretrizes**: Use identidades gerenciadas para fornecer aplicativos implantados pelo serviço de aplicativo com uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Identidades gerenciadas permitem que seus aplicativos se autentiquem em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

- [Como usar identidades gerenciadas para o serviço de aplicativo](overview-managed-identity.md)

- [Como fornecer Key Vault autenticação com uma identidade gerenciada](../key-vault/general/assign-access-policy-portal.md)

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

**Diretrizes**: o recurso de backup e restauração no serviço de aplicativo permite que você crie facilmente backups de aplicativos manualmente ou em um agendamento. Você pode configurar os backups para que sejam mantidos até um período indefinido. Você pode restaurar o aplicativo em um instantâneo de um estado anterior, substituindo o aplicativo existente ou restaurando em outro aplicativo.

O serviço de aplicativo pode fazer o backup das seguintes informações em uma conta de armazenamento do Azure e um contêiner, que você configurou para usar o aplicativo:
- Configuração do aplicativo
- Conteúdo do arquivo
- Banco de dados conectado ao seu aplicativo

Verifique se os back-ups regulares e automatizados estão ocorrendo a uma frequência, conforme definido pelas políticas organizacionais.

- [Entender Azure App recurso de backup do serviço](manage-backup.md)

- [Chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure](../storage/common/customer-managed-keys-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretrizes**: Use o recurso de backup e restauração do serviço de aplicativo para fazer backup de seus aplicativos. Os recursos de backup exigem uma conta de armazenamento do Azure para armazenar as informações de backup do aplicativo.

- O armazenamento do Azure fornece criptografia em repouso-use chaves fornecidas pelo sistema ou suas próprias chaves gerenciadas pelo cliente. É aí que os dados do aplicativo são armazenados quando não estão em execução em um aplicativo Web no Azure.
- A execução de um pacote de implantação é um recurso de implantação do serviço de aplicativo. Ele permite que você implante o conteúdo do site de uma conta de armazenamento do Azure usando uma URL de SAS (assinatura de acesso compartilhado).

- Referências de Key Vault são um recurso de segurança do serviço de aplicativo. Ele permite que você importe segredos em tempo de execução como configurações de aplicativo. Use isso para criptografar a URL SAS da sua conta de armazenamento do Azure.

Mais informações estão disponíveis nos links referenciados.

- [Fazer backup de seu aplicativo no Azure](manage-backup.md)

- [Restaurar um aplicativo em execução no Azure App Service](web-sites-restore.md)

- [Noções básicas sobre a criptografia em repouso do Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modelo de criptografia e tabela de gerenciamento de chaves](../security/fundamentals/encryption-atrest.md)

- [Criptografia em repouso usando chaves gerenciadas pelo cliente](configure-encrypt-at-rest-using-cmk.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretrizes**: teste periodicamente o processo de restauração para todos os backups de seus aplicativos do serviço de aplicativo.

- [Fazer backup de seu aplicativo no Azure](manage-backup.md)

- [Como restaurar um aplicativo Web do serviço de Azure App](web-sites-restore.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretrizes**: os backups do serviço de aplicativo são armazenados em uma conta de armazenamento do Azure. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento, incluindo as contas do Resource Manager e do armazenamento clássico. A criptografia de armazenamento do Azure não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure.

Por padrão, os dados em uma conta de armazenamento são criptografados com chaves gerenciadas pela Microsoft. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus dados ou pode gerenciar a criptografia com suas próprias chaves. Verifique se a exclusão reversível está habilitada no Azure Key Vault.

- [Entender a criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)

- [Como habilitar a exclusão reversível no Azure Key Vault](../key-vault/general/key-vault-recovery.md)

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

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos do Azure.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: testar procedimentos de resposta de segurança

**Orientação**: conduza exercícios para testar os recursos de resposta a incidentes do seu sistema em uma cadência regular. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Consulte o guia de publicação do NIST para testar, treinar e exercitar programas para planos de ti e recursos](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: as informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente foram acessados por uma pessoa não autorizada ou ilegal.  Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretrizes**: exporte seus alertas e recomendações da central de segurança usando o recurso de exportação contínua. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Use o conector de dados da central de segurança para transmitir os alertas para o Azure Sentinel de acordo com as necessidades comerciais.

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

**Diretrizes**: siga as regras de teste de penetração Microsoft Cloud do Engagement para garantir que seus testes de penetração não estejam violando as políticas da Microsoft. Use a estratégia da Microsoft, a execução de Equipes Vermelhas e os testes de penetração de sites online na infraestrutura, nos serviços e nos aplicativos de nuvem gerenciados pela Microsoft. 

- [Regras de participação para testes de penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipes Vermelhas do Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Saiba mais sobre a [Linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
