---
title: Gerenciar instâncias de alerta no Azure Monitor
description: Gerenciar instâncias de alerta no Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734801"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Gerenciar instâncias de alerta com alertas unificados

Com a [experiência de alertas unificados](./alerts-overview.md) no Azure monitor, você pode ver todos os tipos de alertas diferentes no Azure. Isso abrange várias assinaturas, em um único painel. Este artigo mostra como você pode exibir suas instâncias de alerta e como encontrar instâncias de alertas específicas para solução de problemas.

> [!NOTE]
> Você só pode acessar alertas gerados nos últimos 30 dias.

## <a name="go-to-the-alerts-page"></a>Ir para a página de alertas

Você pode ir para a página alertas de qualquer uma das seguintes maneiras:

- Na [portal do Azure](https://portal.azure.com/), selecione **monitorar**  >  **alertas**.  

     ![Captura de tela de alertas do monitor](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Use o contexto de um recurso específico. Abra um recurso, vá para a seção **monitoramento** e escolha **alertas**. A página de aterrissagem é filtrada previamente para alertas sobre esse recurso específico.

     ![Captura de tela dos alertas de monitoramento de recursos](media/alerts-managing-alert-instances/alert-resource.JPG)

- Use o contexto de um grupo de recursos específico. Abra um grupo de recursos, vá para a seção **monitoramento** e escolha **alertas**. A página de aterrissagem é filtrada previamente para alertas nesse grupo de recursos específico.    

     ![Captura de tela de alertas de monitoramento de grupo de recursos](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Localizar instâncias de alerta

A página **Resumo de alertas** fornece uma visão geral de todas as suas instâncias de alerta no Azure. Você pode modificar o modo de exibição de resumo selecionando **várias assinaturas** (até um máximo de 5) ou filtrando entre **grupos de recursos**, **recursos** específicos ou **intervalos de tempo**. Selecione **total de alertas** ou qualquer uma das faixas de severidade para ir para o modo de exibição de lista para seus alertas.     

![Captura de tela da página de Resumo de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na página **todos os alertas** , todas as instâncias de alerta no Azure são listadas. Se estiver indo ao portal de uma notificação de alerta, você poderá usar os filtros disponíveis para restringir essa instância de alerta específica.

> [!NOTE]
> Se você veio à página selecionando qualquer uma das faixas de severidade, a lista é previamente filtrada para essa gravidade.

Além dos filtros disponíveis na página anterior, você também pode filtrar a base do serviço de monitor (por exemplo, plataforma para métricas), monitorar condição (acionada ou resolvida), gravidade, estado de alerta (novo/confirmado/fechado) ou a ID do grupo inteligente.

![Captura de tela de todas as páginas de alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Se você veio à página selecionando qualquer uma das faixas de severidade, a lista é previamente filtrada para essa gravidade.

A seleção de qualquer instância de alerta abre a página **detalhes do alerta** , permitindo que você veja mais detalhes sobre essa instância de alerta específica.   

![Captura de tela da página de detalhes do alerta](media/alerts-managing-alert-instances/alert-details.jpg)