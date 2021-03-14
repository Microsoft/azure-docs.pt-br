---
title: Linha de base de segurança do Azure para Gerenciamento de API
description: A linha de base de segurança de gerenciamento de API fornece diretrizes de procedimento e recursos para implementar as recomendações de segurança especificadas no benchmark de segurança do Azure.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7c5fbbd8cdfd75819bffcfb93fc81900feb4a612
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652361"
---
# <a name="azure-security-baseline-for-api-management"></a>Linha de base de segurança do Azure para Gerenciamento de API

Essa linha de base de segurança aplica diretrizes do [benchmark de segurança do Azure versão 1,0](../security/benchmarks/overview-v1.md) ao gerenciamento de API. O Azure Security Benchmark fornece recomendações sobre como você pode proteger suas soluções de nuvem no Azure.
O conteúdo é agrupado pelos **controles de segurança** definidos pelo benchmark de segurança do Azure e pelas diretrizes relacionadas aplicáveis ao gerenciamento de API. Os **controles** não aplicáveis ao gerenciamento de API foram excluídos.

 
Para ver como o gerenciamento de API é mapeado completamente para o benchmark de segurança do Azure, consulte o [arquivo de mapeamento de linha de base de segurança de gerenciamento de API completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de rede

*Para obter mais informações, confira o [Azure Security Benchmark: Segurança de Rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteger os recursos do Azure em redes virtuais

**Diretriz**: O Gerenciamento de API do Azure pode ser implantado em uma Rede Virtual do Microsoft Azure (VNet) para que possa acessar serviços de back-end na rede. O portal do desenvolvedor e o gateway do Gerenciamento de API podem ser configurados para serem acessados pela Internet (externo) ou somente na rede virtual (interno).

- Externo: o portal de desenvolvedor e o gateway de Gerenciamento de API podem ser acessados pela Internet pública por meio de um balanceador de carga externo. O gateway pode acessar recursos na rede virtual.

- Interno: o portal de desenvolvedor e o gateway de Gerenciamento de API só podem ser acessados de dentro da rede virtual por meio de um balanceador de carga interno. O gateway pode acessar recursos na rede virtual.

O tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado pode ser controlado usando o grupo de segurança de rede.

- [Como usar o Gerenciamento de API do Azure com redes virtuais](api-management-using-with-vnet.md)

- [Usar o serviço de Gerenciamento de API do Azure com uma rede virtual interna](api-management-using-with-internal-vnet.md)

- [Integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](api-management-howto-integrate-internal-vnet-appgateway.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorar e registrar a configuração e o tráfego de redes virtuais, sub-redes e interfaces de rede

**Diretriz**: O tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado pode ser controlado usando o grupo de segurança de rede (NSGs). Implante um NSG em sua sub-rede de Gerenciamento de API e habilite logs de fluxo do NSG e envie logs para uma conta do Armazenamento do Microsoft Azure para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Cuidado: Ao configurar um NSG na sub-rede de Gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

- [Entender as configurações do NSG para o Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicativos Web críticos

**Diretriz**: Para proteger APIs Web/HTTP críticas, configure o Gerenciamento de API em uma Rede Virtual (Vnet) no modo interno e configure um Gateway de Aplicativo do Azure. O Gateway de Aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços.

Combinar o Gerenciamento de API provisionado em uma VNet interna com o front-end do Gateway de Aplicativo permite os seguintes cenários:
- Usar um único recurso de Gerenciamento de API para expor todas as APIs para clientes internos e externos.
- Use um único recurso de Gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Fornecer uma maneira rápida de ativar e desativar o acesso ao Gerenciamento de API da Internet pública.

Observação: Este recurso está disponível nas camadas Premium e Developer do Gerenciamento de API.

- [Como integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Compreender o Gateway de Aplicativo do Azure](/azure/application-gateway/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negar comunicações com endereços IP mal-intencionados conhecidos

**Diretriz**: Configure o Gerenciamento de API em uma Rede Virtual (Vnet) no modo interno e configure um Gateway de Aplicativo do Azure. O Gateway de Aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços.

Combinar o Gerenciamento de API provisionado em uma VNet interna com o front-end do Gateway de Aplicativo permite os seguintes cenários:
- Usar um único recurso de Gerenciamento de API para expor todas as APIs para clientes internos e externos.
- Use um único recurso de Gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Fornecer uma maneira rápida de ativar e desativar o acesso ao Gerenciamento de API da Internet pública.

Observação: Este recurso está disponível nas camadas Premium e Developer do Gerenciamento de API.

Use a inteligência contra ameaças integrada da Central de Segurança do Azure para negar comunicações com endereços IP da Internet maliciosos conhecidos ou não usados.

- [Como integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Compreender o Gateway de Aplicativo do Azure](/azure/application-gateway/)

- [Compreender a inteligência contra ameaças integrada da Central de Segurança do Azure](../security-center/azure-defender.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="15-record-network-packets"></a>1,5: gravar pacotes de rede

**Diretriz**: Você pode usar os grupos de segurança de rede (NSGs) para controlar o tráfego de entrada e saída para a sub-rede na qual o Gerenciamento de API foi implantado. Implante um NSG em sua sub-rede de Gerenciamento de API e habilite logs de fluxo do NSG e envie logs para uma conta do Armazenamento do Microsoft Azure para auditoria de tráfego. Também envie logs de fluxo de NSG para um workspace do Log Analytics e use a Análise de Tráfego para fornecer insights sobre o fluxo de tráfego em sua nuvem do Azure. Algumas vantagens da Análise de Tráfego são a capacidade de visualizar a atividade de rede e identificar pontos de acesso, identificar ameaças à segurança, compreender os padrões de fluxo de tráfego e identificar configurações incorretas de rede de pinpoint.

Cuidado: Ao configurar um NSG na sub-rede de Gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

- [Entender as configurações do NSG para o Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Como habilitar logs de fluxo de NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Como habilitar e usar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: implantar os sistemas de detecção de intrusão/prevenção de invasão baseado em rede (IDS/IPS)

**Diretriz**: Configure o Gerenciamento de API em uma Rede Virtual (Vnet) no modo interno e configure um Gateway de Aplicativo do Azure. O Gateway de Aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços. O WAF do Gateway de Aplicativo oferece proteção contra vulnerabilidades e explorações de segurança comuns e pode ser executado nos dois modos a seguir:
- Modo de detecção: Monitora e registra todos os alertas de ameaça. Você pode ativar o log de diagnóstico do Gateway de Aplicativo na seção Diagnóstico. Você precisa garantir que o log do WAF esteja selecionado e ativado. O firewall do aplicativo Web no modo de detecção não bloqueia solicitações de entrada quando está operando no modelo de Detecção.
- Modo de prevenção: Bloqueia invasões e ataques detectados pelas regras. O invasor recebe uma exceção "403 acesso não autorizado" e a conexão é encerrada. O modo de Prevenção registra tais ataques nos logs do WAF.

Combinar o Gerenciamento de API provisionado em uma VNet interna com o front-end do Gateway de Aplicativo permite os seguintes cenários:
- Usar um único recurso de Gerenciamento de API para expor todas as APIs para clientes internos e externos.
- Use um único recurso de Gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Fornecer uma maneira rápida de ativar e desativar o acesso ao Gerenciamento de API da Internet pública.

Observação: Este recurso está disponível nas camadas Premium e Developer do Gerenciamento de API.

- [Como integrar o Gerenciamento de API em uma VNET interna com o Gateway de Aplicativo](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Compreender o WAF do Gateway de Aplicativo do Azure](../web-application-firewall/ag/ag-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerenciar o tráfego para aplicativos Web

**Diretriz**: Para gerenciar o tráfego que passa pelas APIs Web/HTTP, implante o Gerenciamento de API em uma Rede Virtual (Vnet) associada ao Ambiente do Serviço de Aplicativo no modo externo ou interno.

No modo interno, configure um Gateway de Aplicativo do Azure na frente do Gerenciamento de API. O Gateway de Aplicativo é um serviço PaaS. Ele atua como um proxy reverso e fornece balanceamento de carga L7, roteamento, firewall do aplicativo Web (WAF) e outros serviços. O WAF do Gateway de Aplicativo oferece proteção contra vulnerabilidades e explorações de segurança comuns.

Combinar o Gerenciamento de API provisionado em uma VNet interna com o front-end do Gateway de Aplicativo permite os seguintes cenários:
- Usar um único recurso de Gerenciamento de API para expor todas as APIs para clientes internos e externos.
- Use um único recurso de Gerenciamento de API para expor um subconjunto de APIs para consumidores externos.
- Fornecer uma maneira rápida de ativar e desativar o acesso ao Gerenciamento de API da Internet pública.

Observação: Este recurso está disponível nas camadas Premium e Developer do Gerenciamento de API.

- [Como expor APIs privadas a consumidores externos](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Como usar o Gerenciamento de API em uma Vnet](api-management-using-with-vnet.md)

- [Firewall do aplicativo Web Azure no Gateway de Aplicativo do Azure](../web-application-firewall/ag/ag-overview.md)

- [Compreender o Gateway de Aplicativo do Azure](/azure/application-gateway/overview)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimizar a complexidade e a sobrecarga administrativa de regras de segurança de rede

**Diretriz**: Use as marcas de serviço de Rede Virtual (Vnet) para definir os controles de acesso à rede em NSGs (grupos de segurança de rede) usados em suas sub-redes de Gerenciamento de API. Você pode usar marcas de serviço em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, ApiManagement) no campo correto de origem ou destino de uma regra, você poderá permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

Cuidado: Ao configurar um NSG na sub-rede de Gerenciamento de API, há um conjunto de portas que precisam ser abertas. Se qualquer uma dessas portas estiver indisponível, o Gerenciamento de API poderá não funcionar corretamente e poderá se tornar inacessível.

- [Compreender e usar marcas de serviço](../virtual-network/service-tags-overview.md)

- [Portas necessárias para o Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Diretriz**: Defina e implemente configurações de segurança padrão para as configurações de rede relacionadas às suas implantações do Gerenciamento de API do Azure. Use aliases do Azure Policy nos namespaces "Microsoft.ApiManagement" e "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede das suas implantações do Gerenciamento de API do Azure e recursos relacionados.

Você também pode usar plantas do Azure para simplificar implantações do Azure de grande escala ao empacotar artefatos de ambiente-chave, como modelos de Azure Resource Manager, controle de acesso baseado em função do Azure (RBAC do Azure) e políticas em uma única definição de Blueprint. É fácil aplicar o blueprint a novas assinaturas e novos ambientes e ajustar o controle e o gerenciamento por meio do controle de versão.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar um blueprint do Azure](../governance/blueprints/create-blueprint-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documentar regras de configuração de tráfego

**Diretriz**: Use marcas para NSGs (grupos de segurança de rede) e outros recursos relacionados à segurança de rede e ao fluxo de tráfego. Para regras de NSG individuais, use o campo "Descrição" para especificar a necessidade empresarial e/ou a duração (etc.) para regras que permitam tráfego de/para uma rede.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

- [Como criar uma Rede Virtual](../virtual-network/quick-create-portal.md)

- [Como criar um NSG com uma configuração de segurança](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Usar ferramentas automatizadas para monitorar as configurações de recursos de rede e detectar alterações

**Diretriz**: Use o log de atividades do Azure para monitorar configurações de recursos de rede e detectar alterações para recursos de rede associados às suas implantações do Gerenciamento de API do Azure. Crie alertas no Azure Monitor que serão disparados quando ocorrerem alterações em recursos de rede críticos.

- [Como exibir e recuperar eventos do log de atividades do Azure](/azure/azure-monitor/platform/activity-log-view)

- [Como criar alertas no Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="logging-and-monitoring"></a>Registro em log e monitoramento

*Para obter mais informações, consulte o [benchmark de segurança do Azure: registro em log e monitoramento](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar o gerenciamento central de log de segurança

**Diretriz**: No Azure Monitor, use os workspaces do Log Analytics para consultar e executar análises, enviar logs para o Armazenamento do Microsoft Azure para análise offline ou armazenamento de longo prazo ou de arquivamento, ou exportar logs para outra solução de análise no Azure e em outro lugar usando os Hubs de Eventos do Azure. O Gerenciamento de API do Azure gera logs e métricas para Azure Monitor por padrão. O detalhamento do log pode ser configurado em uma base de todo o serviço e por API.

Além do Azure Monitor, o Gerenciamento de API do Azure pode ser integrado a um ou vários serviços do Application Insights do Azure. As configurações de log do Application Insights podem ser configuradas tanto por serviço quanto por API.

Como opção, habilite e integre dados ao Azure Sentinel ou a um SIEM (gerenciamento de eventos e informações de segurança) de terceiros.

- [Como definir as configurações de diagnóstico](/azure/azure-monitor/platform/diagnostic-settings#create-in-azure-portal)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introdução à integração do Azure Monitor e ao SIEM de terceiros](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Como criar um pipeline personalizado de log e análise](api-management-howto-log-event-hubs.md)

- [Como integrar com o Azure Application Insights](api-management-howto-app-insights.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitar o registro em log de auditoria para recursos do Azure

**Diretriz**: Para o log de auditoria do painel de controle, habilite as configurações de diagnóstico do log de atividades do Azure e envie logs de atividade para um workspace do Log Analytics para relatório e análise, para proteção de longo prazo do Armazenamento do Microsoft Azure, para Hubs de Eventos do Azure para exportar em outras soluções de análise no Azure e em outro lugar. Usando os dados do log de atividades do Azure, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) executada no nível do painel de controle para seu serviço de Gerenciamento de API do Azure.

Para log de auditoria do plano de dados, logs de diagnóstico fornecem informações avançadas sobre operações e erros importantes para auditoria, bem como para fins de solução de problemas. Os logs de diagnóstico são diferentes dos logs de atividades. Os logs de atividades fornecem informações sobre as operações realizadas em seus recursos do Azure. Os Logs de Diagnóstico fornecem informações em operações que o recurso realizou.

- [Como habilitar as configurações de diagnóstico para o Log de Atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Como habilitar as configurações de diagnóstico do Gerenciamento de API do Azure](/Azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento do log de segurança

**Diretriz**: No Azure Monitor, defina o período de retenção do workspace do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Use contas do Armazenamento do Microsoft Azure para armazenamento de longo prazo/arquivamento.

- [Como definir parâmetros de retenção de log para workspaces do Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Como arquivar os logs para uma conta de Armazenamento do Microsoft Azure](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="26-monitor-and-review-logs"></a>2,6: monitorar e examinar os logs

**Diretriz**: O Gerenciamento de API do Azure envia continuamente logs e métricas para o Azure Monitor, oferecendo visibilidade quase em tempo real do estado e da integridade de suas APIs. Com workspaces do Log Analytics e do Azure Monitor, você poderá revisar, consultar, visualizar, rotear, arquivar, configurar alertas e efetuar ações com relação a métricas e logs recebidos do Gerenciamento de API e recursos relacionados. Analise e monitore os logs em busca de comportamentos anormais e examine os resultados regularmente.

Como opção, você pode integrar o Gerenciamento de API com o Application Insights do Azure e usá-lo como ferramenta primária ou secundária de monitoramento, rastreamento, relatório e alerta.

- [Como monitorar e revisar os logs do Gerenciamento de API do Azure](/Azure/api-management/api-management-howto-use-azure-monitor)

- [Como realizar consultas personalizadas no Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Compreender o workspace do Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Como integrar com o Azure Application Insights](api-management-howto-app-insights.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: habilitar alertas para atividades anômalas

**Diretriz**: Habilite as configurações de diagnóstico do log de atividades do Azure, bem como as configurações de diagnóstico para as instâncias de Gerenciamento de API do Azure e envie os logs para um workspace do Log Analytics. Faça consultas no Log Analytics para pesquisar termos, identificar tendências, analisar padrões e fornecer muitos outros insights com base nos dados coletados. Você pode criar alertas com base em suas consultas do workspace do Log Analytics.

Crie alertas de métrica para avisá-lo quando algo inesperado estiver acontecendo. Por exemplo, obtenha notificações quando sua instância de Gerenciamento de API do Azure exceder a capacidade de pico esperada durante um determinado período ou se houver um determinado número de solicitações não autorizadas ou erros de gateway em um período predefinido.

Como opção, você pode integrar o Gerenciamento de API com o Application Insights do Azure e usá-lo como ferramenta primária ou secundária de monitoramento, rastreamento, relatório e alerta.

Opcionalmente, você pode habilitar e integrar dados ao Azure Sentinel ou a um SIEM de terceiros.

- [Como habilitar as configurações de diagnóstico para o log de atividades do Azure](/azure/azure-monitor/platform/activity-log)

- [Como habilitar as configurações de diagnóstico do Gerenciamento de API do Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

- [Como configurar uma regra de alerta para o gerenciamento de API do Azure](/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

- [Como ver as métricas de capacidade de uma instância de Gerenciamento de API do Azure](api-management-capacity.md)

- [Como integrar com o Azure Application Insights](api-management-howto-app-insights.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="identity-and-access-control"></a>Identidade e controle de acesso

*Para obter mais informações, consulte o [benchmark de segurança do Azure: identidade e controle de acesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário de contas administrativas

**Diretriz**: Mantenha um estoque de contas com acesso administrativo ao painel de controle do Gerenciamento de API do Azure (portal do Azure).

O Azure Active Directory (AD do Azure) tem funções internas que devem ser explicitamente atribuídas e que podem ser consultadas. O Gerenciamento de API depende dessas funções e do RBAC para habilitar o gerenciamento de acesso refinado de serviços e entidades do Gerenciamento de API.

Além disso, o Gerenciamento de API contém um grupo interno de administradores no sistema de usuário do Gerenciamento de API. Os grupos no Gerenciamento de API controlam a visibilidade das APIs no portal do desenvolvedor e os membros do grupo Administradores podem ver todas as APIs.

Siga as recomendações da Central de Segurança do Azure para o gerenciamento e a manutenção de contas administrativas.

- [Como usar o Controle de Acesso Baseado em Função no Gerenciamento de API do Azure](api-management-role-based-access-control.md)

- [Como obter uma lista de usuários sob a instância de Gerenciamento de API do Azure](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Como obter uma lista de usuários atribuídos a uma função do diretório no Azure Active Directory com o PowerShell](/powershell/module/az.resources/get-azroleassignment)

- [Como obter uma definição de função do diretório no Azure Active Directory com o PowerShell](/powershell/module/az.resources/get-azroledefinition)

- [Compreender as recomendações de identidade e acesso da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/recommendations-reference#identityandaccess-recommendations)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Alterar senhas padrão quando aplicável

**Diretriz**: O Gerenciamento de API do Azure não tem o conceito de senhas/chave padrão.

As assinaturas do Gerenciamento de API, que são uma maneira de garantir o acesso às APIs, vêm com um par gerado de chaves de assinatura. Os clientes podem regenerar essas chaves de assinatura a qualquer momento.

- [Compreender as assinaturas de Gerenciamento de API do Azure](api-management-subscriptions.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Usar contas administrativas dedicadas

**Diretriz**: Crie procedimentos operacionais padrão em relação ao uso de contas administrativas dedicadas. Use a identidade e o gerenciamento de acesso da Central de Segurança do Azure para monitorar a quantidade de contas administrativas.

Além disso, para ajudá-lo a controlar contas administrativas dedicadas, você pode seguir as recomendações da Central de Segurança do Azure ou as políticas internas do Azure, como:
- Deve haver mais de um proprietário atribuído à sua assinatura
- As contas preteridas com permissões de proprietário devem ser removidas de sua assinatura
- As contas externas com permissões de proprietário devem ser removidas de sua assinatura

- [Como usar a Central de Segurança do Azure para monitorar a identidade e o acesso (versão prévia)](../security-center/security-center-identity-access.md)

- [Como usar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usar o logon único (SSO) do Azure Active Directory

**Diretrizes**: o gerenciamento de API do Azure pode ser configurado para aproveitar Azure Active Directory (Azure AD) como um provedor de identidade para autenticar usuários no portal do desenvolvedor para se beneficiar dos recursos de SSO oferecidos pelo Azure AD. Uma vez configurados, os novos usuários do Portal do Desenvolvedor podem optar por seguir o processo de inscrição pronto para uso, em que primeiro autenticam pelo Azure Active Directory e, após a autenticação, concluem o processo de inscrição no portal.

- [Autorizar contas de desenvolvedor usando o Azure AD no gerenciamento de API do Azure](api-management-howto-aad.md)

Como alternativa, o processo de entrada/inscrição pode ser personalizado por meio de delegação. A delegação permite usar seu site existente para gerenciar a entrada/inscrição e assinatura de produtos feitas por desenvolvedores em vez de usar a funcionalidade integrada no portal do desenvolvedor. Ela permite que seu site tenha os dados dos usuários e realize a validação dessas etapas de forma personalizada.

- [Como delegar o registro de usuário e a assinatura do produto](api-management-howto-setup-delegation.md)

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

- [Entenda as detecções de risco do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risk-events)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gerenciar recursos do Azure provenientes somente de locais aprovados

**Diretriz**: Use localizações nomeadas de acesso condicional para permitir o acesso ao portal do Azure somente a agrupamentos lógicos de intervalos de endereços IP ou de países/regiões específicos.

- [Como configurar localizações nomeadas no Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="39-use-azure-active-directory"></a>3.9: Use o Azure Active Directory Domain Services

**Orientação**: sempre que possível, use Azure Active Directory (Azure AD) como o sistema de autenticação e autorização central. O Azure AD protege os dados usando criptografia forte para dados em repouso e em trânsito. O Azure Active Directory também inclui sais, hashes e armazena com segurança as credenciais do usuário.

Configure seu portal do desenvolvedor de gerenciamento de API do Azure para autenticar contas de desenvolvedor usando o Azure AD.

Configure sua instância do gerenciamento de API do Azure para proteger suas APIs usando o protocolo OAuth 2,0 com o Azure AD.

- [Como autorizar contas de desenvolvedor usando o Azure AD no gerenciamento de API do Azure](api-management-howto-aad.md)

- [Como proteger uma API usando o OAuth 2,0 com o Azure AD e o gerenciamento de API](api-management-howto-protect-backend-with-aad.md)

- [Como criar e configurar uma instância do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: revisar e reconciliar regularmente o acesso do usuário

**Diretrizes**: o Azure Active Directory (Azure AD) fornece logs para ajudar a descobrir contas obsoletas. Os clientes podem usar as revisões de acesso de identidade do Azure para gerenciar associações de grupo, acesso aos aplicativos empresariais e atribuições de função com eficiência. O acesso do usuário pode ser revisado regularmente para garantir que apenas os usuários certos mantenham o acesso apropriado.

Os clientes podem manter o estoque das contas de usuário do Gerenciamento de API e reconciliar o acesso conforme necessário. No Gerenciamento de API, os desenvolvedores são os consumidores das APIs que são expostas com o Gerenciamento de API. Por padrão, as contas de desenvolvedor criadas recentemente têm o estado “Ativa” e são associadas ao grupo Desenvolvedores. As contas de desenvolvedor que estão com estado “Ativa” podem ser utilizadas para acessar todas as APIs nas quais estão inscritas.

Os administradores podem criar grupos personalizados ou aproveitar grupos externos em locatários associados do Azure AD. Grupos personalizados e externos podem ser usados juntamente com grupos de sistema oferecendo visibilidade aos desenvolvedores e acesso a produtos de API.

- [Como gerenciar contas de usuário no Gerenciamento de API do Azure](api-management-howto-create-or-invite-developers.md)

- [Como obter a lista de usuários do Gerenciamento de API](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Como criar e usar grupos para gerenciar contas de desenvolvedor no Gerenciamento de API do Azure](api-management-howto-create-groups.md)

- [Como usar as revisões de acesso de identidade do Azure](../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: monitorar tentativas de acessar credenciais desativadas

**Diretrizes**: configure sua instância de gerenciamento de API do Azure para autenticar contas de desenvolvedor usando Azure Active Directory (Azure AD) como um provedor de identidade no gerenciamento de API do Azure.

Configure sua instância do gerenciamento de API do Azure para proteger suas APIs usando o protocolo OAuth 2,0 com o Azure AD.

Configure a política de validação de JWT para solicitações de API de entrada para ajudar a aplicar a existência e a validade de um token válido.

Crie configurações de diagnóstico para contas de usuário do Azure Active Directory e envie os logs de auditoria e os logs de entrada para um workspace do Log Analytics. Configure os alertas desejados no Log Analytics. Além disso, você pode integrar o workspace do Log Analytics ao Azure Sentinel ou a um SIEM de terceiros.

Configure o monitoramento avançado com o Gerenciamento de API usando a política `log-to-eventhub`, capture informações adicionais de contexto necessárias para análise de segurança e envie para o Azure Sentinel ou SIEM de terceiros.

- [Como autorizar contas de desenvolvedor usando o Azure AD no gerenciamento de API do Azure](api-management-howto-aad.md)

- [Como proteger uma API usando o OAuth 2,0 com o Azure AD e o gerenciamento de API](api-management-howto-protect-backend-with-aad.md)

- [Políticas de restrição de acesso do Gerenciamento de API](api-management-access-restriction-policies.md)

- [Como integrar os logs do Azure Active Directory no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Monitoramento avançado de APIs](api-management-log-to-eventhub-sample.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: alerta sobre o desvio do comportamento de entrada da conta

**Diretrizes**: para o desvio do comportamento de logon da conta no plano de controle (o portal do Azure), use os recursos de proteção de identidade e de detecção de riscos do Azure Active Directory (Azure AD) para configurar respostas automatizadas para detectar ações suspeitas relacionadas a identidades de usuário. Você também pode ingerir dados no Azure Sentinel para uma investigação mais aprofundada.

- [Como exibir entradas suspeitas do Azure Active Directory](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Como configurar e habilitar políticas de risco de proteção de identidade](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Como integrar o Azure Sentinel](../sentinel/quickstart-onboard.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Fornecer à Microsoft acesso a dados relevantes do cliente durante cenários de suporte

**Diretriz**: Não disponível no momento. O Sistema de Proteção de Dados do Cliente não dá suporte ao Gerenciamento de API do Azure.

- [Lista de serviços suportados do Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, confira o [Azure Security Benchmark: proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informações confidenciais

**Diretriz**: Use marcas para ajudar a controlar os recursos do Azure que armazenam ou processam informações confidenciais.

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolar sistemas que armazenam ou processam informações confidenciais

**Diretriz**: Implemente assinaturas e/ou grupos de gerenciamento separados para desenvolvimento, teste e produção. As instâncias de Gerenciamento de API do Azure devem ser separadas por rede virtual (VNet)/sub-rede e marcadas adequadamente.

- [Como criar assinaturas adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

- [Como usar o Gerenciamento de API do Azure com redes virtuais](api-management-using-with-vnet.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitore e bloqueie a transferência não autorizada de informações confidenciais

**Diretriz**: Não disponível no momento. Os recursos de identificação de dados, classificação e prevenção contra perda não estão disponíveis no momento para o Gerenciamento de API do Azure.

A Microsoft gerencia a infraestrutura subjacente para o Gerenciamento de API do Azure e implementou controles rigorosos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações confidenciais em trânsito

**Diretriz**: As chamadas do plano de gerenciamento são feitas pelo Azure Resource Manager por meio de TLS. Um JWT (token Web JSON) válido é necessário. As chamadas de plano de dados podem ser protegidas com TLS e um dos mecanismos de autenticação com suporte (por exemplo, certificado de cliente ou JWT).

- [Entender a proteção de dados no Gerenciamento de API do Azure](/azure/api-management/api-management-security-controls#data-protection)

- [Gerenciar configurações de TLS no Gerenciamento de API do Azure](api-management-howto-manage-protocols-ciphers.md)

- [Proteger APIs no gerenciamento de API do Azure com o Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Proteger APIs no gerenciamento de API do Azure com Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usar uma ferramenta de descoberta ativa para identificar dados confidenciais

**Orientação**: ainda não disponível; os recursos de identificação de dados, classificação e prevenção de perda ainda não estão disponíveis para o gerenciamento de API do Azure. Marque os serviços de Gerenciamento de API do Azure que podem processar informações confidenciais como tais e implemente uma solução de terceiros, se necessário, para fins de conformidade.

Para a plataforma subjacente que é gerenciada pela Microsoft, a Microsoft trata todo o conteúdo do cliente como confidencial e faz todo o esforço possível para proteger contra perda e exposição de dados do cliente. Para garantir que os dados do cliente no Azure permaneçam seguros, a Microsoft implementou e mantém um conjunto de recursos e controles robustos de proteção de dados.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usar o RBAC do Azure para controlar o acesso a recursos 

**Orientação**: Use o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso ao gerenciamento de API do Azure. O gerenciamento de API do Azure depende do controle de acesso baseado em função do Azure para habilitar o gerenciamento de acesso refinado para serviços e entidades de gerenciamento de API (por exemplo, APIs e políticas).

- [Como usar o controle de acesso baseado em função do Azure no gerenciamento de API do Azure](api-management-role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usar a prevenção contra perda de dados baseada em host para impor controle de acesso

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

A Microsoft gerencia a infraestrutura subjacente para o Gerenciamento de API do Azure e implementou controles rigorosos para evitar a perda ou a exposição dos dados do cliente.

- [Entender a proteção de dados do cliente no Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrar e alertar sobre alterações em recursos críticos do Azure

**Diretriz**: Use o Azure Monitor com o log de atividades do Azure para criar alertas para a ocorrência de alterações em aplicativos de Azure Functions de produção, bem como em outros recursos críticos ou relacionados.

- [Como criar alertas para eventos do log de atividades do Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Como usar o Azure Monitor e o log de atividades do Azure no Gerenciamento de API do Azure](api-management-howto-use-azure-monitor.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades

*Para obter mais informações, consulte o [benchmark de segurança do Azure: gerenciamento de vulnerabilidade](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Executar ferramentas automatizadas de verificação de vulnerabilidade

**Diretriz**: Não disponível no momento. A avaliação de vulnerabilidade na Central de Segurança do Azure não está disponível no momento para o Gerenciamento de API do Azure.

Plataforma subjacente verificada e corrigida pela Microsoft. Examine os controles de segurança disponíveis para reduzir as vulnerabilidades relacionadas à configuração de serviço.

- [Noções básicas dos controles de segurança disponíveis para o Gerenciamento de API do Azure](/azure/api-management/api-management-security-controls)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implantar solução automatizada de gerenciamento de patch de sistema operacional

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: implantar solução de gerenciamento de patch automatizado para títulos de software de terceiros

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Comparar verificações de vulnerabilidade consecutivas

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Usar um processo de avaliação de risco para priorizar a correção das vulnerabilidades descobertas

**Diretriz**: Não disponível no momento. A avaliação de vulnerabilidade na Central de Segurança do Azure não está disponível no momento para o Gerenciamento de API do Azure.

Plataforma subjacente verificada e corrigida pela Microsoft. O cliente deve examinar os controles de segurança disponíveis a ele para reduzir as vulnerabilidades relacionadas à configuração de serviço.

- [Noções básicas dos controles de segurança disponíveis para o Gerenciamento de API do Azure](/azure/api-management/api-management-security-controls)

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

- [Como criar e utilizar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Excluir recursos do Azure não autorizados

**Diretriz**: Use marcação, grupos de gerenciamento e assinaturas separadas, sempre que apropriado, para organizar e rastrear recursos do Azure. Reconcilie o inventário regularmente e garanta que os recursos não autorizados sejam excluídos da assinatura em tempo hábil.

Além disso, use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como criar assinaturas adicionais do Azure](/azure/billing/billing-create-subscription)

- [Como criar Grupos de Gerenciamento](../governance/management-groups/create-management-group-portal.md)

- [Como criar e usar marcas](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definir e manter o inventário de recursos aprovados do Azure

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorar recursos do Azure não aprovados

**Diretriz**: Use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados nas assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

Use o Azure Resource Graph para consultar/descobrir recursos em suas assinaturas. Verifique se todos os recursos do Azure presentes no ambiente foram aprovados.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como criar consultas com o Azure Graph](../governance/resource-graph/first-query-portal.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorar aplicativos de software não aprovados nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicativos de software não aprovados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="68-use-only-approved-applications"></a>6.8: usar somente aplicativos aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="69-use-only-approved-azure-services"></a>6.9: usar somente serviços do Azure aprovados

**Diretriz**: use o Azure Policy para colocar restrições nos tipos de recursos que podem ser criados em assinaturas do cliente usando as seguintes definições de política interna:
- Tipos de recursos não permitidos
- Tipos de recursos permitidos

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo de recurso específico com o Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: manter um inventário de títulos de software aprovados

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitar a capacidade dos usuários de interagir com Azure Resource Manager

**Diretriz**: Configure o acesso condicional do Azure para limitar a capacidade dos usuários de interagir com o Azure Resource Manager configurando "Bloquear acesso" para o aplicativo de “Gerenciamento do Microsoft Azure”.

- [Como configurar o acesso condicional para bloquear o acesso ao Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Usar o controle de acesso baseado em função no Gerenciamento de API do Azure](api-management-role-based-access-control.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitar a capacidade dos usuários de executar scripts nos recursos de computação

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separar física ou logicamente os aplicativos de alto risco

**Diretriz**: Não aplicável; essa recomendação destina-se a aplicativos Web em execução no Serviço de Aplicativo do Azure ou recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [benchmark de segurança do Azure: configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

**Diretriz**: Defina e implemente configurações de segurança padrão para seu serviço do Gerenciamento de API do Azure com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração dos seus serviços do Gerenciamento de API do Azure.

- [Como exibir os aliases disponíveis do Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: estabelecer configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos do Azure

**Diretriz**: Defina e implemente configurações de segurança padrão para seus serviços do Gerenciamento de API do Azure com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração das instâncias do Gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: manter configurações seguras de sistema operacional

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar configuração de recursos do Azure com segurança

**Diretriz**: Se você estiver usando definições personalizadas de política do Azure, use o Azure DevOps ou Azure Repos para armazenar e gerenciar com segurança sua configuração de serviço do Gerenciamento de API do Azure.

- [Como armazenar arquivos no Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Documentação do Azure Repos](/azure/devops/repos/)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: armazenar imagens personalizadas do sistema operacional com segurança

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: implantar as ferramentas de gerenciamento de configuração para recursos do Azure

**Diretriz**: Defina e implemente configurações de segurança padrão para seus serviços do Gerenciamento de API do Azure com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração das instâncias do Gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: implantar as ferramentas de gerenciamento de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementar o monitoramento automatizado de configuração para recursos do Azure

**Diretriz**: Use o kit de recursos do DevOps do Gerenciamento de API do Azure para executar gerenciamento de configuração para o Gerenciamento de API do Azure.

Além disso, defina e implemente configurações de segurança padrão para seus serviços do Gerenciamento de API do Azure com o Azure Policy. Use aliases do Azure Policy no namespace "Microsoft.ApiManagement" para criar políticas personalizadas para auditar ou impor a configuração das instâncias do Gerenciamento de API do Azure. Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

- [Como configurar e gerenciar o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Compreender os efeitos do Azure Policy](../governance/policy/concepts/effects.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementar monitoramento automatizado de configuração para sistemas operacionais

**Diretriz**: não aplicável; essa recomendação destina-se a recursos de computação.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gerenciar identidades de maneira segura e automática

**Orientação**: Use identidade de serviço gerenciada gerado pelo Azure Active Directory (AD do Azure) para permitir que sua instância de gerenciamento de API acesse com facilidade e segurança outros recursos protegidos pelo Azure AD, como Azure Key Vault.

- [Como criar uma identidade gerenciada para uma instância de Gerenciamento de API](api-management-howto-use-managed-service-identity.md)

- [Política para autenticar com a identidade gerenciada](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

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

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos de computação.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Gerenciamento de API do Azure), mas não é executado no conteúdo do cliente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Arquivos de pré-verificação a serem carregados para recursos não computados do Azure

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Gerenciamento de API do Azure), mas não é executado no conteúdo do cliente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Garantir que o software antimalware e as assinaturas sejam atualizados

**Diretriz**: Não aplicável. Esta recomendação destina-se a recursos que não são de computação e projetados para armazenar dados.

O antimalware da Microsoft está habilitado no host subjacente que oferece suporte aos serviços do Azure (por exemplo, Gerenciamento de API do Azure), mas não é executado no conteúdo do cliente.

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="data-recovery"></a>Recuperação de dados

*Para obter mais informações, consulte o [benchmark de segurança do Azure: recuperação de dados](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir back-ups automatizados regulares

**Diretriz**: Ao publicar e gerenciar suas APIs através do Gerenciamento de API do Azure, você está tirando proveito de muitos recursos de tolerância e infraestrutura que você precisaria criar, implementar e gerenciar manualmente. O Gerenciamento de API dá suporte à implantação em várias regiões, o que torna o plano de dados impenetrável a falhas regionais sem adicionar nenhuma sobrecarga operacional.

Os recursos de backup e de restauração de serviço do Gerenciamento de API fornecem os componentes necessários para implementar uma estratégia de recuperação de desastre. As operações de backup e restauração podem ser executadas manualmente ou de maneira automatizada.

- [Como implantar o plano de dados do Gerenciamento de API em várias regiões](api-management-howto-deploy-multi-region.md)

- [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Como chamar a operação de backup do Gerenciamento de API](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

- [Como chamar a operação de restauração do Gerenciamento de API](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

**Diretriz**: As operações de backup e restauração fornecidas pelo Gerenciamento de API do Azure executam backup e restauração completos do sistema.

As identidades gerenciadas podem ser usadas para obter certificados de Azure Key Vault para nomes de domínio personalizado do Gerenciamento de API. Faça backup dos certificados armazenados no Azure Key Vault.

- [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Como fazer backup de certificados do Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

**Diretriz**: Para validar os backups, execute uma restauração de teste do serviço e certificados dos backups.

- [Como chamar a operação de restauração do Gerenciamento de API](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Como restaurar certificados do Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

**Diretriz**: O Gerenciamento de API do Azure grava backups em contas do Armazenamento do Microsoft Azure de propriedade do cliente. Siga as recomendações de segurança do Armazenamento do Microsoft Azure para proteger seu backup.

- [Como implementar a recuperação de desastre usando o backup de serviço e restaurar no Gerenciamento de API no Azure](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Recomendação de segurança para o armazenamento de blobs](../storage/blobs/security-recommendations.md)

Habilite a exclusão reversível no Key Vault para proteger chaves contra exclusão acidental ou mal-intencionada.

- [Como habilitar a exclusão reversível no Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="incident-response"></a>Resposta a incidentes

*Para obter mais informações, confira o [Azure Security Benchmark: resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: criar um guia de resposta a incidentes

**Diretriz**: crie um guia de resposta a incidentes para sua organização. Verifique se há planos de resposta a incidentes escritos que definem todas as funções de pessoal, bem como as fases de tratamento/gerenciamento de incidentes, desde a detecção até a revisão após o incidente.

- [Orientação sobre como criar seu processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Aproveitar o Guia de tratamento de incidentes de segurança de computador do NIST para ajudar na criação de seu plano de resposta a incidentes](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: criar um procedimento de pontuação e priorização de incidentes

**Diretriz**: a Central de Segurança atribui uma severidade a cada alerta para ajudar você a priorizar quais alertas devem ser investigados primeiro. A gravidade se baseia em quão confiante a central de segurança está na localização ou na análise usada para emitir o alerta, bem como o nível de confiança de que houve uma intenção mal-intencionada por trás da atividade que levou ao alerta.

Além disso, marque claramente as assinaturas (por exemplo, produção, não produção) usando marcas e crie um sistema de nomeação para identificar claramente e categorizar os recursos do Azure, em especial aqueles que processam dados confidenciais. É sua responsabilidade priorizar a correção de alertas com base na criticalidade dos recursos do Azure e do ambiente em que o incidente ocorreu.

- [Alertas na Central de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Usar marcas para organizar seus recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="103-test-security-response-procedures"></a>10.3: Testar procedimentos de resposta de segurança

**Diretriz**: Conduza regularmente exercícios para testar os recursos de resposta a incidentes de seus sistemas para ajudar a proteger seus recursos do Azure. Identifique pontos fracos e lacunas e revise o plano conforme necessário.

- [Publicação do NIST - Guia para testar, treinar e exercitar programas para planos de TI e capacidades](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer detalhes de contato do incidente de segurança e configurar notificações de alerta para incidentes de segurança

**Diretriz**: As informações de contato do incidente serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que seus dados foram acessados por uma pessoa não autorizada ou ilegal. Examine os incidentes após o fato para garantir que os problemas sejam resolvidos.

- [Como definir o contato de segurança da Central de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança em seu sistema de resposta a incidentes

**Diretriz**: Exporte seus alertas e recomendações da Central de Segurança do Azure usando o recurso de exportação contínua para ajudar a identificar riscos para os recursos do Azure. A exportação contínua permite exportar alertas e recomendações de forma manual ou contínua. Você pode usar o conector de dados da Central de Segurança do Azure para transmitir os alertas do Azure Sentinel.

- [Como configurar a exportação contínua](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta a alertas de segurança

**Diretriz**: Use o recurso de automação de fluxo de trabalho na Central de Segurança do Azure para disparar automaticamente respostas por meio de “Aplicativos Lógicos” em alertas de segurança e recomendações.

- [Como configurar a automação de fluxo de trabalho e os Aplicativos Lógicos](../security-center/workflow-automation.md)

**Responsabilidade**: Cliente

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios de Red Team

*Para obter mais informações, consulte o [benchmark de segurança do Azure: testes de penetração e exercícios de equipe vermelho](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: realize testes de penetração regulares de seus recursos do Azure e garanta a correção de todas as descobertas de segurança críticas

**Diretrizes**: siga as regras de envolvimento da Microsoft para garantir que seus testes de penetração não estejam em violação às políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e a execução de Red Team e testes de penetração de sites ao vivo em infraestrutura, serviços e aplicativos de nuvem gerenciados pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidade**: Compartilhado

**Monitoramento da central de segurança do Azure**: nenhum

## <a name="next-steps"></a>Próximas etapas

- Confira a [Visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre a [Linhas de base de segurança do Azure](/azure/security/benchmarks/security-baselines-overview)
