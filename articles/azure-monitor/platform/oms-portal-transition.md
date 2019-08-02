---
title: Portal do OMS migrando para o Azure | Microsoft Docs
description: O portal do OMS está sendo desativado com toda a sua funcionalidade migrando para o portal do Azure. Este artigo fornece detalhes sobre essa transição.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bwren
ms.openlocfilehash: 80c4fa5fad574f1d6efe476df90a8396fa5cb4f3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205629"
---
# <a name="oms-portal-moving-to-azure"></a>Portal do OMS migrando para o Azure

> [!NOTE]
> Este artigo aplica-se a nuvem pública do Azure e a nuvem do governo, exceto quando indicado o contrário.

**O portal do OMS para a nuvem pública do Azure foi oficialmente desativado. O portal do OMS para a nuvem do Azure US Government foi oficialmente desativado em 15 de maio de 2019.** Estamos animados com a migração para o portal do Azure e esperamos que a transição seja fácil. No entanto, sabemos que alterações desse tipo são difíceis e que podem gerar inconvenientes. O restante deste artigo aborda os cenários principais e o roteiro para essa transição.

O portal do Azure é o hub para todos os serviços do Azure e oferece uma rica experiência de gestão, com funcionalidades como painéis para fixação de recursos, pesquisa inteligente para localização de recursos e marcação para gerenciamento de recursos. Para consolidar e simplificar o fluxo de trabalho de monitoramento e gerenciamento, começamos adicionando as funcionalidades do portal do OMS ao portal do Azure. Todos os recursos do portal do OMS agora fazem parte do portal do Azure. Na verdade, alguns dos novos recursos, como análise de tráfego só estão disponíveis no portal do Azure. Você poderá fazer tudo o que você estava fazendo no portal do OMS com o portal do Azure e muito mais. Se ainda não fez isso, você deve começar a usar o portal do Azure hoje mesmo.

## <a name="what-is-changing"></a>O que está mudando? 
As alterações a seguir estão sendo anunciadas com a substituição do portal do OMS. Cada uma dessas alterações é descrita mais detalhadamente nas seções a seguir.

- Você pode criar novos [workspaces somente](#new-workspaces) no portal do Azure.
- A nova experiência de gerenciamento de alertas [substitui a solução de Gerenciamento de Alertas](#changes-to-alerts).
- O [gerenciamento de acesso de usuário](#user-access-and-role-migration) agora é realizado no portal do Azure usando o controle de acesso baseado em função do Azure.
- O [Conector do Application Insights não é mais necessário](#application-insights-connector-and-solution), pois a mesma funcionalidade é habilitada por meio de consultas entre espaços de trabalho.
- O [aplicativo móvel do OMS](#oms-mobile-app) será preterido. 
- A [solução NSG está sendo substituída](#azure-network-security-group-analytics) pela funcionalidade aprimorada disponível por meio da solução de Análise de Tráfego.
- Novas conexões do System Center Operations Manager para o Log Analytics exigem [pacotes de gerenciamento atualizados](#system-center-operations-manager).
- Veja [Migrar suas implantações de atualização do OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter detalhes sobre as mudanças do [Gerenciamento de Atualizações](../../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>O que devo fazer agora?
Embora a maioria dos recursos continue a funcionar sem executar qualquer migração, você precisa executar as seguintes tarefas:

- Você precisa [migrar suas permissões de usuário](#user-access-and-role-migration) para o portal do Azure.
- Veja [Migrar suas implantações de atualização do OMS para o Azure](../../automation/migrate-oms-update-deployments.md) para obter detalhes sobre a transição da solução de Gerenciamento de Atualizações.

Consulte as [Perguntas comuns para a transição do portal do OMS para o portal do Azure para usuários do Log Analytics](oms-portal-faq.md) para obter informações sobre como fazer a transição para o portal do Azure. 

## <a name="user-access-and-role-migration"></a>Acesso do usuário e migração de função
O gerenciamento de acesso ao portal do Azure é mais avançado e mais poderoso do que o gerenciamento de acesso no Portal do OMS. Consulte [Gerenciar workspaces](manage-access.md#manage-accounts-and-users) para obter detalhes sobre o gerenciamento de acesso no Log Analytics.

> [!NOTE]
> As versões anteriores deste artigo diziam que as permissões seriam convertidas automaticamente do portal do OMS para o portal do Azure. Essa conversão automática não é mais planejada e você mesmo deve realizar a conversão.

Talvez você já tenha acesso apropriado no portal do Azure, caso em que você não precisa fazer nenhuma alteração. Há alguns casos em que talvez você não tenha acesso apropriado. Nesse caso, o administrador deve atribuir permissões a você.

- Você tem permissões de Usuário Somente Leitura no portal do OMS, mas nenhuma permissão no portal do Azure. 
- Você tem permissões de Colaborador no portal do OMS, mas apenas Acesso de leitor no portal do Azure.
 
Em ambos os casos, o administrador precisa atribuir manualmente a função apropriada da tabela a seguir. É recomendável que você atribua essa função no nível da assinatura ou do grupo de recursos.  Uma orientação mais detalhada será fornecida em breve para ambos os casos.

| Permissão de portal do OMS | Função do Azure |
|:---|:---|
| ReadOnly | Leitor do Log Analytics |
| Colaborador | Colaborador do Log Analytics |
| Administrador | Proprietário | 
 

## <a name="new-workspaces"></a>Novos workspaces
Você não pode mais criar novos workspaces usando o portal do OMS. Siga as orientações [criar um espaço de trabalho do Log Analytics no portal do Azure](../learn/quick-create-workspace.md) para criar um novo espaço de trabalho no portal do Azure.

## <a name="changes-to-alerts"></a>Alterações a alertas

### <a name="alert-extension"></a>Extensão de alerta  

Alertas foram [estendidos para o portal do Azure](alerts-extend.md) alertas existentes continuarão a ser listados no portal do OMS, mas você só pode gerenciá-los no portal do Azure. Se você acessar os alertas de forma programática usando a API REST de Alerta do Log Analytics ou o Modelo de Recurso de Alerta do Log Analytics, use grupos de ação em vez de ações nas chamadas à API, modelos do Azure Resource Manager e comandos do PowerShell.

### <a name="alert-management-solution"></a>solução de Gerenciamento de Alertas
Como uma alteração de um aviso anterior, a [Solução de gerenciamento de alertas](alert-management-solution.md) continuará disponível e com suporte total no portal do Azure. Você pode continuar a instalar a solução do Azure Marketplace.

Embora a solução de Gerenciamento de alertas continue disponível, recomendamos que você use a [Interface de alertas unificada do Azure Monitor](alerts-overview.md) para visualizar e gerenciar todos os alertas no Azure. Essa nova experiência agrega nativamente alertas de várias fontes no Azure, incluindo alertas de log do Log Analytics. Se você estiver usando a interface unificada de alertas do Azure Monitor, a solução de Gerenciamento de alertas só será necessária para habilitar a integração de alertas do System Center Operation Manager para o Azure. Na interface de alerta unificada do Azure Monitor, você pode ver as distribuições de seus alertas, aproveitar o agrupamento automatizado de alertas relacionados por meio de grupos inteligentes e exibir alertas entre várias assinaturas enquanto aplica filtros avançados. Os futuros avanços no gerenciamento de alertas estarão disponíveis principalmente com essa nova experiência. 

Os dados coletados pela solução de Gerenciamento de alertas (registros com um tipo de alerta) continuarão no Log Analytics enquanto a solução estiver instalada para o workspace. 

## <a name="oms-mobile-app"></a>Aplicativo móvel do OMS
O aplicativo móvel do OMS será desativado juntamente com o portal do OMS. Em lugar do aplicativo móvel do OMS, para acessar informações sobre sua infraestrutura de IT, painéis e consultas salvas, você pode acessar o portal do Azure diretamente do navegador em seu dispositivo móvel. Para obter alertas, você deve configurar [Grupos de Ação do Azure](action-groups.md) para receber notificações na forma de SMS ou uma chamada de voz

## <a name="application-insights-connector-and-solution"></a>Conector do Application Insights e solução
O [Conector do Application Insights](app-insights-connector.md) fornece uma maneira de incluir os dados do Application Insights em um workspace do Log Analytics. Essa duplicação de dados era necessária para permitir a visibilidade entre dados de aplicativos e de infraestrutura. Com p suporte para retenção de dados estendido do Application Insights em março de 2019 e a capacidade de realizar [consultas entre recursos](../log-query/cross-workspace-query.md) além de poder [exibir vários recursos do Azure Monitor Application Insights](../log-query/unify-app-resource-data.md), não é necessário duplicar os dados dos recursos do Application Insights e enviá-lo para o Log Analytics. Além disso, o Conector envia um subconjunto das propriedades de aplicativos para o Log Analytics, enquanto as consultas entre recursos dão flexibilidade aprimorada.  

Como tal, o conector do Application Insights foi preterido e removido do Azure Marketplace, juntamente com a substituição de portal do OMS no dia 30 de março de 2019. As conexões existentes continuarão a funcionar até 30 de junho de 2019. Com o preterimento do portal OMS, não há como configurar nem remover conexões do portal. Isso terá suporte usando a API REST que será disponibilizada em janeiro de 2019, e uma notificação será publicada nas [atualizações do Azure](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Análise de Grupo de Segurança de Rede do Azure
A [solução de Análise do Grupo de Segurança de Rede do Azure](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) será substituída pela recém-lançada [Análise de Tráfego](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) que fornece visibilidade sobre a atividade de usuário e do aplicativo em redes de nuvem. A Análise de Tráfego ajuda você a auditar a atividade de rede de sua organização, proteger aplicativos e dados, otimizar o desempenho da carga de trabalho e permanecer em conformidade. 

Esta solução analisa logs de fluxo NSG e fornece insights sobre os elementos a seguir.

- Fluxos de tráfego em suas redes entre o Azure e a Internet, regiões de nuvem pública, VNETs e sub-redes.
- Aplicativos e protocolos em sua rede, sem a necessidade de sniffers (farejadores) ou dispositivos de coleta de fluxo dedicados.
- Principais talkers, aplicativos chatty, conversas de VM na nuvem, pontos de acesso de tráfego.
- Origens e destinos do tráfego entre VNETs, inter-relacionamentos entre serviços críticos de negócios e aplicativos.
- Segurança, incluindo tráfego malicioso, portas abertas para a Internet, aplicativos ou VMs que tentam acessar a Internet.
- Utilização de capacidade, que lhe ajuda a eliminar problemas de sobreprovisionamento ou subutilização.

Você pode continuar a contar com as Configurações de Diagnóstico para enviar logs de NSG para o Log Analytics para que suas pesquisas salvas, alertas e painéis existentes continuem a funcionar. Os clientes que já instalaram a solução podem continuar a usá-la até nova ordem. A partir de 5 de setembro, a solução de análise do grupo de segurança de rede será removida do marketplace e disponibilizada por meio da comunidade como um [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Se você [conectou seu grupo de gerenciamento do Operations Manager ao Log Analytics](om-agents.md), ele continuará funcionando sem alterações. No entanto, para novas conexões, você deve seguir as orientações em [Pacote de Gerenciamento do Microsoft System Center Operations Manager para configurar o Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Próximas etapas
- Consulte [Perguntas comuns para a transição do portal do OMS para o portal do Azure para usuários do Log Analytics](oms-portal-faq.md) para obter diretrizes sobre como fazer a transição do portal do OMS para o portal do Azure.
