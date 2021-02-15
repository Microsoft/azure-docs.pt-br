---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001128"
---
A configuração `ApplicationInsights` permite que você adicione suporte a dados telemétricos do [Azure Application Insights](/azure/application-insights) para seu contêiner. O Application Insights fornece um monitoramento detalhado do seu contêiner. Você pode monitorar facilmente seu contêiner quanto a disponibilidade, desempenho e uso. Você pode identificar e diagnosticar erros em seu contêiner rapidamente.

A tabela a seguir descreve as definições de configuração com suporte sob o `ApplicationInsights` seção.

|Obrigatório| Name | Tipo de dados | Descrição |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | String | A chave de instrumentação da instância do Application Insights para o qual telemetria de dados para o contêiner são enviados. Para obter mais informações, consulte [Application Insights para ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|