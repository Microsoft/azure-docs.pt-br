---
title: Exibir vários recursos no Gerenciador de métricas do Azure
description: Saiba como Visualizar vários recursos usando o Gerenciador de métricas do Azure.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.openlocfilehash: 85fa252f999407d5cd7f75e954554e3b14357071
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037312"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Exibir vários recursos no Gerenciador de métricas do Azure

O seletor de escopo de recurso permite exibir métricas em vários recursos que estão dentro da mesma assinatura e região. Este artigo explica como exibir vários recursos usando o recurso do Gerenciador de métricas do Azure da Azure Monitor. 

## <a name="select-a-resource"></a>Selecionar um recurso 

Selecione **Métricas** no menu do **Azure Monitor** ou na seção **Monitoramento** do menu de um recurso. Em seguida, escolha **selecionar um escopo** para abrir o seletor de escopo. 

Use o seletor de escopo para selecionar os recursos cujas métricas você deseja ver. O escopo deverá ser preenchido se você tiver aberto o Metrics Explorer no menu de um recurso. 

![Captura de tela mostrando como abrir o seletor de escopo de recurso.](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>Selecionar vários recursos 

Alguns tipos de recursos podem consultar métricas em vários recursos. As métricas devem estar dentro da mesma assinatura e local. Encontre esses tipos de recursos na parte superior do menu de **tipos de recursos** .

![Captura de tela que mostra um menu de recursos que são compatíveis com vários recursos.](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> Você deve ter a permissão de leitor de monitoramento no nível da assinatura para visualizar as métricas em vários recursos, grupos de recursos ou uma assinatura. Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

Para visualizar as métricas em vários recursos, comece selecionando vários recursos no seletor de escopo de recurso. 

![Captura de tela que mostra como selecionar vários recursos.](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> Os recursos selecionados devem estar dentro do mesmo tipo de recurso, local e assinatura. Os recursos que não se ajustam a esses critérios não são selecionáveis. 

Quando terminar, escolha **aplicar** para salvar suas seleções. 

## <a name="select-a-resource-group-or-subscription"></a>Selecionar um grupo de recursos ou uma assinatura 

> [!WARNING]
> Você deve ter a permissão de leitor de monitoramento no nível da assinatura para visualizar as métricas em vários recursos, grupos de recursos ou uma assinatura. 

Para tipos que são compatíveis com vários recursos, você pode consultar métricas em uma assinatura ou em vários grupos de recursos. Comece selecionando uma assinatura ou um ou mais grupos de recursos: 

![Captura de tela que mostra como consultar em vários grupos de recursos.](./media/metrics-dynamic-scope/022.png)

Selecione um tipo de recurso e um local. 

![Captura de tela que mostra os grupos de recursos selecionados.](./media/metrics-dynamic-scope/023.png)

Você pode expandir os escopos selecionados para verificar os recursos aos quais suas seleções se aplicam.

![Captura de tela que mostra os recursos selecionados dentro dos grupos.](./media/metrics-dynamic-scope/024.png)

Quando terminar de selecionar escopos, selecione **aplicar**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Dividir e filtrar por grupo de recursos ou recursos

Depois de plotar seus recursos, você pode usar a divisão e a filtragem para obter mais informações sobre seus dados. 

A divisão permite visualizar como os diferentes segmentos da métrica se comparam entre si. Por exemplo, ao plotar uma métrica para vários recursos, você pode escolher **aplicar divisão** para dividir por ID de recurso ou grupo de recursos. A divisão permite que você compare uma única métrica entre vários recursos ou grupos de recursos.  

Por exemplo, o gráfico a seguir mostra a porcentagem de CPU em nove VMs. Ao dividir por ID de recurso, você verá como a porcentagem de CPU difere por VM. 

![Captura de tela que mostra como usar a divisão para ver a porcentagem de CPU entre VMs.](./media/metrics-dynamic-scope/026.png)

Juntamente com a divisão, você pode usar a filtragem para exibir apenas os grupos de recursos que deseja ver.  Por exemplo, para exibir a porcentagem de CPU para VMs para um determinado grupo de recursos, você pode selecionar **Adicionar filtro** para filtrar por grupo de recursos. 

Neste exemplo, filtramos por TailspinToysDemo. Aqui, o filtro remove as métricas associadas aos recursos em TailspinToys. 

![Captura de tela que mostra como filtrar por grupo de recursos.](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>Fixar gráficos de vários recursos 

Gráficos de vários recursos que visualizam métricas entre grupos de recursos e assinaturas exigem que o usuário tenha a permissão de *leitor de monitoramento* no nível da assinatura. Verifique se todos os usuários dos painéis para os quais você está fixando gráficos de vários recursos têm permissões suficientes. Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

Para fixar o gráfico de vários recursos em um painel, consulte [fixação em painéis](../essentials/metrics-charts.md#pinning-to-dashboards). 

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas do Metrics Explorer](../essentials/metrics-troubleshoot.md)
* [Veja uma lista das métricas disponíveis para os serviços do Azure](./metrics-supported.md)
* [Veja exemplos de gráficos configurados](../essentials/metric-chart-samples.md)