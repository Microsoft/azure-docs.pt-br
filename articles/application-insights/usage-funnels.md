---
title: Funis do Azure Application Insights
description: "Saiba como você pode usar Funis para descobrir como os clientes estão interagindo com seu aplicativo."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 59c4dfafc102b26e3b9873f433065715f4aec9ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Descobrir como os clientes estão usando seu aplicativo com os Funis do Application Insights

Entender a experiência do cliente é de extrema importância para seus negócios. Se o aplicativo envolver vários estágios, você precisará saber se a maioria dos clientes está progredindo por todo o processo ou se eles estão encerrando o processo em algum momento. A progressão por uma série de etapas em um aplicativo Web é conhecida como “funil”. Use os Funis do Application Insights para obter informações sobre os usuários e monitorar as taxas de conversão passo a passo. 

## <a name="get-started-with-the-funnels-blade"></a>Introdução à folha Funis
A maneira mais fácil de saber mais sobre os Funis é acompanhar um exemplo. As ilustrações a seguir demonstram as etapas que os proprietários de uma empresa de comércio eletrônico realizarão para saber como seus clientes interagem com seu aplicativo Web.  

### <a name="create-your-funnel"></a>Criar o funil
Antes de criar o funil, você precisa decidir qual pergunta você deseja responder. Por exemplo, talvez você deseje saber quantos clientes que exibem a home page clicam em um anúncio. Neste exemplo, os proprietários da empresa Fabrikam Fiber desejam saber o percentual de clientes que fazem uma compra depois de adicionar itens ao carrinho de compras durante o mês passado.

Estas são as etapas que eles realizam para criar seu funil.

1. Clique no botão Novo na folha Funis.
1. Selecione o intervalo de tempo “Mês passado” na lista suspensa **Intervalo de Tempo**. 
1. Selecione o evento **Página do produto** na lista suspensa **Etapa 1**. 
1. Selecione o evento **Adicionar ao carrinho de compras** na lista suspensa **Etapa 2**.
1. Selecione o evento **Clicar em Comprar** na lista suspensa **Etapa 3**.
1. Adicione um nome ao funil e clique em **Salvar**.

A ilustração a seguir demonstra os dados gerados na folha Funis. Nessa folha, os proprietários da Fabrikam podem ver que, durante a última semana, 22,7% de seus clientes que adicionaram um item ao carrinho de compras concluíram a compra. Eles também podem ver que 1% dos clientes clicou em um anúncio antes de visitar a página do produto e que 20% de seus clientes saíram do site depois de concluírem a compra.


![Folha Funis com os dados](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Próximas etapas
  * [Visão geral do uso](app-insights-usage-overview.md)
  * [Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
  * [Retenção](app-insights-usage-retention.md)
  * [Pastas de trabalho](app-insights-usage-workbooks.md)
  * [Adicionar contexto de usuário](app-insights-usage-send-user-context.md)
