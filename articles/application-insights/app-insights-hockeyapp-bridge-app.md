---
title: Explorar dados do HockeyApp no Azure Application Insights | Microsoft Docs
description: Analise o uso e o desempenho de seu aplicativo do Azure com o Application Insights.
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: bwren
ms.openlocfilehash: 450ca10613d137393090578619f3766734d1d493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Exploração de dados do HockeyApp no Application Insights
[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é a plataforma recomendada para o monitoramento de aplicativos móveis e de desktop dinâmico. Do HockeyApp, você pode enviar telemetria personalizada e de rastreamento para monitorar o uso e ajudar no diagnóstico (além de obter dados de falha). Esse fluxo de telemetria pode ser consultado usando o poderoso recurso [Análise](app-insights-analytics.md) do [Azure Application Insights](app-insights-overview.md). Além disso, você pode [exportar a telemetria personalizada e de rastreamento](app-insights-export-telemetry.md). Para habilitar esses recursos, você configura uma ponte que retransmite dados personalizados do HockeyApp ao Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>O aplicativo de ponte HockeyApp
O aplicativo de ponte HockeyApp é o principal recurso que permite a você acessar sua telemetria de rastreamento e personalizada do HockeyApp no Application Insights por meio dos recursos Analytics e Exportação Contínua. Eventos personalizados e de rastreamento coletados pelo HockeyApp após a criação do aplicativo de ponte HockeyApp estarão acessíveis por meio desses recursos. Vamos ver como configurar esses aplicativos de ponte.

No HockeyApp, abra Configurações de Conta, [Tokens de API](https://rink.hockeyapp.net/manage/auth_tokens). Crie um novo token ou reutilize um existente. Os direitos mínimos necessários são "somente leitura". Faça uma cópia do token da API.

![Obter um token da API do HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Abra o portal do Microsoft Azure e [crie um recurso do Application Insights](app-insights-create-new-resource.md). Defina o Tipo de Aplicativo como "Aplicativo de ponte HockeyApp":

![Novo recurso do Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

Você não precisa definir um nome - isso será definido automaticamente a partir do nome do HockeyApp.

Os campos da ponte HockeyApp aparecem. 

![Inserir campos de ponte](./media/app-insights-hockeyapp-bridge-app/03.png)

Insira o token do HockeyApp que você anotou anteriormente. Essa ação preenche o menu suspenso "Aplicativo HockeyApp" com todos os seus aplicativos do HockeyApp. Selecione o que você deseja usar e conclua o restante dos campos. 

Abra o novo recurso. 

Observe que os dados demoram um pouco para começar a fluir.

![Recurso do Application Insights aguardando os dados](./media/app-insights-hockeyapp-bridge-app/04.png)

É isso! De agora em diante, dados personalizados e de rastreamento coletados em seu aplicativo instrumentado pelo HockeyApp também estão disponíveis para você nos recursos Analytics e Exportação Contínua do Application Insights.

Vamos analisar rapidamente cada um desses recursos agora disponíveis para você.

## <a name="analytics"></a>Análise
O Analytics é uma ferramenta eficiente para consultas ad hoc de seus dados, permitindo que você diagnostique e analise a telemetria e descubra rapidamente as causas raiz e os padrões.

![Análise](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Saiba mais sobre o Analytics](app-insights-analytics-tour.md)

## <a name="continuous-export"></a>Exportação contínua
A Exportação Contínua permite que você exporte os dados para um contêiner do Armazenamento de Blobs do Azure. Isso será muito útil se você precisar manter seus dados por mais tempo que o período de retenção atualmente oferecido pelo Application Insights. Você pode manter os dados no armazenamento de blobs, processá-los em um banco de dados SQL ou a solução de data warehouse preferencial.

[Saiba mais sobre a Exportação Contínua](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Próximas etapas
* [Aplicar a análise aos seus dados](app-insights-analytics-tour.md)

