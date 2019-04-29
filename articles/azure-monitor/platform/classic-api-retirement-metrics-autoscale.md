---
title: Desativação do Azure Monitor de APIs do modelo de implantação clássico para métricas e dimensionamento automático
description: APIs clássicas de métricas e de dimensionamento automático, também chamadas de Gerenciamento de Serviços do Azure (ASM) ou modelo de implantação RDFE sendo desativado
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709976"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Desativação do Azure Monitor de APIs do modelo de implantação clássico para métricas e dimensionamento automático

O Azure Monitor (anteriormente conhecido como o Azure Insights quando lançado pela primeira vez) atualmente oferece a capacidade de criar e gerenciar as configurações de dimensionamento automático e consumir as métricas de VMs clássicas e serviços de nuvem clássicos. O conjunto original de implantação clássica, as APIs baseadas no modelo serão **desativado após 30 de junho de 2019** em todas as nuvens do Azure públicas e privadas em todas as regiões.   

As mesmas operações são suportadas por meio de um conjunto do Azure Resource Manager com base em APIs para mais de um ano. O portal do Azure usa as novas APIs de REST para métricas e dimensionamento automático. Um novo SDK, PowerShell e CLI com base nessas APIs do Gerenciador de 
Recursos também estão disponíveis. Nossos serviços de parceiro para monitoramento consomem o novo Gerenciador de Recursos com base em APIs REST no Azure Monitor.  

## <a name="who-is-not-affected"></a>Quem não é afetado

Se você estiver gerenciando o dimensionamento automático por meio do portal do Azure, o [novo SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), modelos do PowerShell, CLI ou do Gerenciador de Recursos, nenhuma ação é necessária.  

Se você estiver consumindo métricas por meio do portal do Azure ou por meio de vários [serviços de parceiros de monitoramento](../../azure-monitor/platform/partners.md), nenhuma ação é necessária. A Microsoft está trabalhando com parceiros para migrar para as novas APIs de monitoramento.

## <a name="who-is-affected"></a>Quem é afetado

Este artigo se aplica a você, se você estiver usando os seguintes componentes:

- **SDK clássico do Azure Insights** – se você estiver usando o [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), alterne para usar o novo SDK do Azure Monitor para [.NET](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Baixe [pacote NuGet do SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Dimensionamento automático clássico** – se você estiver chamando as [APIs de configurações de dimensionamento automático clássico](https://msdn.microsoft.com/library/azure/mt348562.aspx) de suas ferramentas personalizadas ou usando o [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), você deve passar a usar o [ API REST do Gerenciador de Recursos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Métricas clássicas** – se você estiver consumindo métricas usando as [APIs REST clássicas](https://msdn.microsoft.com/library/azure/dn510374.aspx) ou [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) de ferramentas personalizadas, você deve passar a usar o [ API REST do Gerenciador de Recursos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Se você não tiver certeza se seu código ou ferramentas personalizadas estão chamando as APIs clássicas, veja o seguinte:

- Revise o URI referenciado no seu código ou a ferramenta. As APIs clássicas usam o URI https://management.core.windows.net. Você deve usar o URI mais recente para o Gerenciador de Recursos baseados em APIs que começa com https://management.azure.com/.

- Compare o nome do assembly em seu computador. O assembly clássico mais antigo está no https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Se você estiver usando autenticação de certificado para acessar as métricas ou as APIs de dimensionamento automático, você está usando uma biblioteca e o ponto de extremidade clássico. As APIs do Gerenciador de Recursos mais recentes requerem autenticação do Active Directory do Azure por meio de uma entidade de serviço ou entidade de usuário.

- Se você estiver usando chamadas mencionadas na documentação em qualquer um dos links a seguir, você está usando as APIs de clássicas mais antigas.

  - [Biblioteca de classes Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitoramento .NET (clássico)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interface IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que você deve mudar

Todos os recursos existentes para o dimensionamento automático e métricas continuarão funcionando por meio das novas APIs.  

Migrar o failover para o mais recente de APIs vêm com recursos baseado no Gerenciador de Recursos, como o suporte consistente baseado em função para controle de acesso (RBAC) em todos os seus serviços de monitoramento. Você também ganha a funcionalidade adicional para métricas: 

- suporte para dimensões
- granularidade de métricas de 1 minuto consistente em todos os serviços 
- melhor consultar
- retenção de dados maior (93 dias de métricas vs. 30 dias) 

No geral, como todos os outros serviços do Azure, o Gerenciador de Recursos com as APIs do Azure Monitor vêm com melhor desempenho, escalabilidade e confiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se você não migrar

### <a name="before-retirement"></a>Antes da desativação

Não haverá nenhum impacto direto sobre os serviços do Azure ou as cargas de trabalho.  

### <a name="after-retirement"></a>Após a desativação

Todas as chamadas para as APIs clássicas listadas anteriormente falharão e retornarão mensagens de erro semelhantes a:

Para dimensionamento automático: *Essa API foi preterida. Usar o portal do Azure, modelos do Gerenciador de Recursos, PowerShell, CLI ou SDK do Azure Monitor para gerenciar as configurações de dimensionamento automático*.  

Para métricas: *Essa API foi preterida. Use o portal do Azure, SDK do Azure Monitor, PowerShell, CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por email

Uma notificação de desativação foi enviada para endereços para as seguintes funções de conta de email: 

- Administradores de conta e serviço
- Coadministradores  

Se você tiver alguma dúvida, entre em contato conosco em MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referências

- [APIs REST mais recentes para o Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [SDK do Azure Monitor mais recente](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
