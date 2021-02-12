---
title: Desativar APIs de implantação para Azure Monitor métricas e dimensionamento automático
description: APIs clássicas de métricas e de dimensionamento automático, também chamadas de Gerenciamento de Serviços do Azure (ASM) ou modelo de implantação RDFE sendo desativado
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536686"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Desativação do Azure Monitor de APIs do modelo de implantação clássico para métricas e dimensionamento automático

O Azure Monitor (anteriormente conhecido como o Azure Insights quando lançado pela primeira vez) atualmente oferece a capacidade de criar e gerenciar as configurações de dimensionamento automático e consumir as métricas de VMs clássicas e serviços de nuvem clássicos. O conjunto original de implantação clássica, as APIs baseadas no modelo serão **desativado após 30 de junho de 2019** em todas as nuvens do Azure públicas e privadas em todas as regiões.   

As mesmas operações são suportadas por meio de um conjunto do Azure Resource Manager com base em APIs para mais de um ano. O portal do Azure usa as novas APIs de REST para métricas e dimensionamento automático. Um novo SDK, PowerShell e CLI com base nessas APIs do Gerenciador de 
Recursos também estão disponíveis. Nossos serviços de parceiro para monitoramento consomem o novo Gerenciador de Recursos com base em APIs REST no Azure Monitor.  

## <a name="who-is-not-affected"></a>Quem não é afetado

Se você estiver gerenciando o dimensionamento automático por meio do portal do Azure, o [novo SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), modelos do PowerShell, CLI ou do Gerenciador de Recursos, nenhuma ação é necessária.  

Se você estiver consumindo métricas por meio do portal do Azure ou por meio de vários [serviços de parceiros de monitoramento](./partners.md), nenhuma ação é necessária. A Microsoft está trabalhando com parceiros para migrar para as novas APIs de monitoramento.

## <a name="who-is-affected"></a>Quem é afetado

Este artigo se aplica a você, se você estiver usando os seguintes componentes:

- **SDK clássico do Azure Insights** – se você estiver usando o [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), alterne para usar o novo SDK do Azure Monitor para [.NET](https://github.com/azure/azure-libraries-for-net#download) ou [Java](https://github.com/azure/azure-libraries-for-java#download). Baixe [pacote NuGet do SDK do Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Dimensionamento automático clássico** – se você estiver chamando as [APIs de configurações de dimensionamento automático clássico](/previous-versions/azure/reference/mt348562(v=azure.100)) de suas ferramentas personalizadas ou usando o [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), você deve passar a usar o [ API REST do Gerenciador de Recursos do Azure Monitor](/rest/api/monitor/autoscalesettings).

- **Métricas clássicas** – se você estiver consumindo métricas usando as [APIs REST clássicas](/previous-versions/azure/reference/dn510374(v=azure.100)) ou [SDK clássico do Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) de ferramentas personalizadas, você deve passar a usar o [ API REST do Gerenciador de Recursos do Azure Monitor](/rest/api/monitor/autoscalesettings). 

Se você não tiver certeza se seu código ou ferramentas personalizadas estão chamando as APIs clássicas, veja o seguinte:

- Revise o URI referenciado no seu código ou a ferramenta. As APIs clássicas usam o URI https://management.core.windows.net. Você deve usar o URI mais recente para o Gerenciador de Recursos baseados em APIs que começa com `https://management.azure.com/`.

- Compare o nome do assembly em seu computador. O assembly clássico mais antigo está no https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Se você estiver usando autenticação de certificado para acessar as métricas ou as APIs de dimensionamento automático, você está usando uma biblioteca e o ponto de extremidade clássico. As APIs do Gerenciador de Recursos mais recentes requerem autenticação do Active Directory do Azure por meio de uma entidade de serviço ou entidade de usuário.

- Se você estiver usando chamadas mencionadas na documentação em qualquer um dos links a seguir, você está usando as APIs de clássicas mais antigas.

  - [Biblioteca de classes Windows.Azure.Management.Monitoring](/previous-versions/azure/dn510414(v=azure.100))

  - [Monitoramento .NET (clássico)](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interface IMetricOperations](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Por que você deve mudar

Todos os recursos existentes para o dimensionamento automático e métricas continuarão funcionando por meio das novas APIs.  

Migrar para APIs mais recentes vem com recursos baseados no Resource Manager, como suporte para controle de acesso baseado em função (RBAC) do Azure consistente em todos os serviços de monitoramento. Você também ganha a funcionalidade adicional para métricas: 

- suporte para dimensões
- granularidade de métricas de 1 minuto consistente em todos os serviços 
- melhor consultar
- maior retenção de dados (93 dias de métricas versus 30 dias) 

No geral, como todos os outros serviços do Azure, o Gerenciador de Recursos com as APIs do Azure Monitor vêm com melhor desempenho, escalabilidade e confiabilidade. 

## <a name="what-happens-if-you-do-not-migrate"></a>O que acontece se você não migrar

### <a name="before-retirement"></a>Antes da desativação

Não haverá nenhum impacto direto sobre os serviços do Azure ou as cargas de trabalho.  

### <a name="after-retirement"></a>Após a desativação

Todas as chamadas para as APIs clássicas listadas anteriormente falharão e retornarão mensagens de erro semelhantes a:

Para dimensionamento automático: *essa API foi preterida. Use os modelos portal do Azure, SDK do Azure Monitor, PowerShell, CLI ou Resource Manager para gerenciar as configurações de dimensionamento automático*.  

Para métricas: *essa API foi preterida. Use o portal do Azure, o SDK do Azure Monitor, o PowerShell, a CLI para consultar métricas*.

## <a name="email-notifications"></a>Notificações por email

Uma notificação de desativação foi enviada para endereços para as seguintes funções de conta de email: 

- Administradores de conta e serviço
- Coadministradores  

Se você tiver alguma dúvida, entre em contato conosco em MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Referências

- [APIs REST mais recentes para o Azure Monitor](/rest/api/monitor/) 
- [SDK do Azure Monitor mais recente](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

