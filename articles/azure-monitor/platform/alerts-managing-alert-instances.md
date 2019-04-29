---
title: Gerenciar instâncias de alerta
description: Gerenciar instâncias de alerta no Azure
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: cb93f38c05156d7ab5acb89ffff810949583e507
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551769"
---
# <a name="manage-alert-instances"></a>Gerenciar instâncias de alerta
Com a [experiência de alertas unificada](https://aka.ms/azure-alerts-overview) no Azure Monitor, agora você pode ver todos os tipos de alertas no Azure, que abrangem várias assinaturas, em um único painel de vidro. Este artigo orienta você por meio de como exibir suas instâncias de alerta e como fazer uma análise aprofundada sobre o portal para localizar instâncias de alerta específicas para a solução de problemas.

1. Há três maneiras de acessar a página de alertas

   + No [portal](https://portal.azure.com/), selecione **Monitor** e na seção Monitor, escolha **Alertas**.  
     ![Monitoramento](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Você pode navegar até Alertas de dentro do contexto de um determinado **recurso**. Depois que um recurso é aberto, navegue pelo sumário até a seção Monitoramento e escolha **Alertas**, com a página de aterrissagem pré-filtrada para alertas no recurso específico.
   
     ![Monitoramento](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + Você pode navegar até Alertas de dentro do contexto de um determinado **grupo de recursos**. Depois que um grupo de recursos é aberto, navegue pelo sumário até a seção Monitoramento e escolha **Alertas**, com a página de aterrissagem pré-filtrada para alertas no grupo de recursos específico.    
   
     ![Monitoramento](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Você será levado até a página **Resumo de alertas**, que lhe dá uma visão geral de todas as instâncias de alerta no Azure. Você pode modificar a exibição de resumo selecionando **várias assinaturas** (um máximo de cinco) ou filtrando nos **grupos de recursos**, **recursos** específicos ou **intervalos de tempo**. Clique em Alertas totais ou em qualquer uma das faixas de gravidade para ir para a exibição de lista de alertas.     
   ![Resumo de alertas](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Você será levado até a página **Todos os alertas**, na qual você verá todas as instâncias de alerta no Azure listadas. Se estiver indo ao portal de uma notificação de alerta, você poderá usar os filtros disponíveis para restringir essa instância de alerta específica. (**Observação**: se acessou a página clicando em qualquer uma das faixas de gravidade, a lista é filtrada previamente para essa gravidade quando você acessar a página). Além dos filtros disponíveis na página anterior, você agora também pode filtrar com base no serviço do monitor (por exemplo, a plataforma para métricas), condição do monitor (disparado ou resolvido), severidade, estado de alerta (novo/confirmado/fechado) ou ID do grupo inteligente.

   ![Todos os Alertas](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Se acessou a página clicando em qualquer uma das faixas de gravidade, a lista é filtrada previamente para essa gravidade quando você acessar a página.
 
1. Clicar em qualquer instância de alerta abre a página **Detalhes do alerta**, permitindo que você se aprofunde nas informações sobre essa instância de alerta específica.   
   ![Detalhes do alerta](media/alerts-managing-alert-instances/alert-details.jpg)  

