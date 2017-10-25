---
title: "Introdução ao Observador de Rede do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral do serviço Observador de Rede para monitorar e visualizar os recursos conectados em rede no Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: eecb20d4a53478471c238018d8fbd5a5f9cb79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-monitoring-overview"></a>Visão geral do monitoramento da rede do Azure

Os clientes criam uma rede de ponta a ponta no Azure administrando e compondo vários recursos de rede individuais, como VNet, ExpressRoute, Gateway de Aplicativo, Balanceadores de carga e mais. O monitoramento está disponível em cada um dos recursos da rede. Chamamos esse monitoramento de monitoramento no nível do recurso.

A rede de ponta a ponta pode ter configurações complexas e interações entre os recursos, criando cenários complexos que precisam de monitoramento baseado em cenários por meio do Observador de Rede.

Este artigo aborda o cenário e o monitoramento no nível do recurso. O monitoramento de rede no Azure é completo e abrange duas grandes categorias:

* [**Observador de Rede**](#network-watcher) - o monitoramento baseado em cenários é fornecido com os recursos do Observador de Rede. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.
* [**Monitoramento de recursos**](#network-resource-level-monitoring) - o monitoramento no nível do recurso é composto de quatro recursos, logs de diagnóstico, métricas, solução de problemas e integridade dos recursos. Todos esses recursos são compilados no nível do recurso da rede.

## <a name="network-watcher"></a>Observador de Rede

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

O Observador de Rede tem atualmente os seguintes recursos:

* **[Topologia](network-watcher-topology-overview.md)** - fornece uma exibição no nível da rede mostrando as diversas interconexões e associações entre os recursos de rede em um grupo de recursos.
* **[Captura de pacotes variável](network-watcher-packet-capture-overview.md)** - captura os dados do pacote dentro e fora de uma máquina virtual. As opções avançadas de filtragem e os controles ajustados, como ser capaz de definir o tempo e as limitações de tamanho, fornecem versatilidade. Os dados do pacote podem ser armazenados em um armazenamento de blobs ou no disco local no formato .cap.
* **[Verificação do fluxo de IP](network-watcher-ip-flow-verify-overview.md)** - verifica se um pacote é permitido ou negado com base nos parâmetros do pacote com cinco tuplas das informações do fluxo (IP de Destino, IP de Origem, Porta de Destino, Porta de Origem e Protocolo). Se o pacote for negado por um grupo de segurança, a regra e o grupo que negaram o pacote serão retornados.
* **[Próximo salto](network-watcher-next-hop-overview.md)** - determina o próximo salto para os pacotes serem roteados no Azure Network Fabric, permitindo diagnosticar quaisquer rotas definidas pelo usuário mal configuradas.
* **[Exibição do grupo de segurança](network-watcher-security-group-view-overview.md)** - obtém as regras de segurança efetivas e aplicadas que são usadas em uma VM.
* **[Log de fluxo de NSG](network-watcher-nsg-flow-logging-overview.md)** - os logs de fluxo para os Grupos de Segurança da Rede permitem capturar os logs relacionados ao tráfego que são permitidos ou negados pelas regras de segurança no grupo. O fluxo é definido por informações com cinco tuplas: IP de Origem, IP de Destino, Porta de Origem, Porta de Destino e Protocolo.
* **[Solução de problemas do Gateway de Rede Virtual e da Conexão](network-watcher-troubleshoot-manage-rest.md)** - fornece a capacidade de solucionar problemas dos Gateways de Rede Virtual e das Conexões.
* **[Limites de assinatura da rede](#network-subscription-limits)** - permite exibir o uso de recursos da rede em relação aos limites.
* **[Configurar o Log de Diagnóstico](#diagnostic-logs)** – fornece um painel único para habilitar ou desabilitar os logs de Diagnóstico para os recursos de rede em um grupo de recursos.
* **[Conectividade (Versão Prévia)](network-watcher-connectivity-overview.md)** – confirma a possibilidade de estabelecer uma conexão TCP direta de uma máquina virtual com determinado ponto de extremidade.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Controle de Acesso baseado em Funções (RBAC) no Observador de Rede

O Observador de Rede usa o [modelo RBAC (Controle de Acesso baseado em Funções) do Azure](../active-directory/role-based-access-control-what-is.md). As permissões a seguir são necessárias para o Observador de Rede. É importante verificar se a função usada para iniciar as APIs do Observador de Rede ou usar o Observador de Rede no portal tem o acesso necessário.

|Recurso| Permissão|
|---|---| 
|Microsoft.Storage/ |Ler|
|Microsoft.Authorization/| Ler| 
|Microsoft.Resources/subscriptions/resourceGroups/| Ler|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Ação|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Ação|
|Microsoft.Storage/storageAccounts/listKeys/ | Ação|
|Microsoft.Compute/virtualMachines/ |Ler|
|Microsoft.Compute/virtualMachines/ |Gravar|
|Microsoft.Compute/virtualMachineScaleSets/ |Ler|
|Microsoft.Compute/virtualMachineScaleSets/ |Gravar|
|Microsoft.Network/networkWatchers/packetCaptures/ |Ler|
|Microsoft.Network/networkWatchers/packetCaptures/| Gravar|
|Microsoft.Network/networkWatchers/packetCaptures/| Excluir|
|Microsoft.Network/networkWatchers/ |Gravar |
|Microsoft.Network/networkWatchers/| Ler |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Limites de assinatura da rede

Os limites de assinatura da rede fornecem a você os detalhes do uso de cada recurso de rede em uma assinatura em uma região em relação ao número máximo de recursos disponíveis.

![limite de assinatura da rede][nsl]

## <a name="network-resource-level-monitoring"></a>Monitoramento no nível do recurso da rede

Os seguintes recursos estão disponíveis para o monitoramento no nível do recurso:

### <a name="audit-log"></a>Log de auditoria

As operações executadas como parte da configuração das redes são registradas. Esses logs podem ser exibidos no portal do Azure ou recuperados usando as ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Os logs de auditoria estão disponíveis por meio do portal, PowerShell, CLI e API Rest. Para obter mais informações sobre os Logs de auditoria, consulte [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)

Os logs de auditoria estão disponíveis para as operações realizadas em todos os recursos da rede.

### <a name="metrics"></a>Métricas

As métricas são medidas de desempenho e contadores coletados em um período de tempo. As métricas estão disponíveis atualmente para o Gateway de Aplicativo. As métricas podem ser usadas para disparar alertas com base no limite. Consulte [Diagnóstico do Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md) para ver como as métricas podem ser usadas para criar alertas.

![exibição de métricas][metrics]

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os eventos periódicos e espontâneas são criados pelos recursos da rede e registrados nas contas de armazenamento, enviados para um Hub de Eventos ou Log Analytics. Esses logs fornecem informações sobre a integridade de um recurso. Esses logs podem ser visualizados em ferramentas como o Power BI e o Log Analytics. Para saber como exibir os logs de diagnóstico, visite [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

Os logs de diagnóstico estão disponíveis para o [Balanceador de Carga](../load-balancer/load-balancer-monitor-log.md), [Grupos de Segurança da Rede](../virtual-network/virtual-network-nsg-manage-log.md), Rotas e [Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md).

O Observador de Rede fornece uma exibição dos logs de diagnóstico. Essa exibição contém todos os recursos de rede que oferecem suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de modo rápido e prático.

![logs][logs]

### <a name="troubleshooting"></a>Solucionar problemas

A folha de solução de problemas, uma experiência no portal, é fornecida nos recursos da rede hoje para diagnosticar os problemas comuns associados a um recurso individual. Essa experiência está disponível para os seguintes recursos de rede: ExpressRoute, Gateway de VPN, Gateway de Aplicativo, Logs de Segurança da Rede, Rotas, DNS, Balanceador de Carga e Gerenciador de Tráfego. Para saber mais sobre a solução de problemas no nível do recurso, visite [Diagnosticar e resolver problemas com a Solução de Problemas do Azure](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![informações da solução de problemas][TS]

### <a name="resource-health"></a>Integridade de recursos

A integridade de um recurso de rede é fornecida periodicamente. Tais recursos incluem o Gateway de VPN e o túnel de VPN. A integridade do recurso é acessível no portal do Azure. Para saber mais sobre a integridade do recurso, visite [Visão Geral do Resource Health](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer o Observador de Rede, você pode aprender a:

fazer uma captura de pacotes em sua VM visitando [Captura de pacotes variável no portal do Azure](network-watcher-packet-capture-manage-portal.md)

executar o monitoramento proativo e diagnóstico usando a [captura de pacotes disparada por alertas](network-watcher-alert-triggered-packet-capture.md).

detectar as vulnerabilidades da segurança com [Analisar a captura de pacotes com o Wireshark](network-watcher-deep-packet-inspection.md), usando ferramentas de código-fonte aberto.

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











