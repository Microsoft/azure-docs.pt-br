---
title: "Introdução ao dimensionamento automático usando métricas personalizadas no Azure | Microsoft Docs"
description: "Saiba como dimensionar seu recurso usando métricas personalizadas no Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.openlocfilehash: de8f7acadc282e4b81c657b1723f00fd3e5fd4f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introdução ao dimensionamento automático usando métricas personalizadas no Azure
Este artigo descreve como dimensionar seu recurso usando métricas personalizadas no portal do Azure.

O dimensionamento automático do Azure Monitor se aplica somente aos VMSS (Conjuntos de Dimensionamento da Máquina Virtual), aos serviços de nuvem, aos planos do Serviço de Aplicativo e aos ambientes do Serviço de Aplicativo. 

# <a name="lets-get-started"></a>Vamos começar
Este artigo pressupõe que você tenha um aplicativo Web com o Application Insights configurado. Se ainda não tiver um, você poderá [configurar o Application Insights para seu site do ASP.NET][1]

- Abra o [portal do Azure][2]
- Clique no ícone do Azure Monitor no painel de navegação à esquerda.
  ![Inicie o Azure Monitor][3]
- Clique na configuração do dimensionamento automático para exibir todos os recursos a que ele se aplica, bem como o status de dimensionamento automático atual ![Descobrir o dimensionamento automático no Azure Monitor][4]
- Abra a folha “Dimensionamento Automático” no Azure Monitor e selecione um recurso que deseja dimensionar
> Observação: as etapas a seguir usam um plano do serviço de aplicativo associado a um aplicativo Web que tem o Application Insights configurado.
- Na folha de configuração de dimensionamento para o recurso, observe que a contagem de instâncias atual é 1. Clique em “Habilitar dimensionamento automático”.
  ![Configuração de dimensionamento para um novo aplicativo Web][5]
- Forneça um nome para a configuração de dimensionamento e clique em "Adicionar uma regra". Observe as opções de regra de dimensionamento que são abertas como um painel de contexto no lado direito. Por padrão, ele define a opção de dimensionar sua contagem de instâncias em 1 se o percentual de CPU do recurso ultrapassar 70%. Altere a origem da métrica na parte superior para "Application Insights", selecione o recurso do Application Insights na lista suspensa “Recurso” e, em seguida, selecione a métrica personalizada com base na qual você deseja fazer o dimensionamento.
  ![Dimensionamento por métrica personalizada][6]
- De forma semelhante à etapa anterior, adicione uma regra de dimensionamento que reduzirá horizontalmente e diminuirá a contagem de escala em 1 se a métrica personalizada estiver abaixo do limite.
  ![Dimensionamento com base na CPU][7]
- Defina os limites de sua instância. Por exemplo, se quiser dimensionar entre 2 e 5 instâncias, dependendo das flutuações da métrica personalizada, defina “mínimo” como “2”, “máximo” como “5” e “padrão” como “2”
> Observação: caso haja algum problema ao ler as métricas do recurso e a capacidade atual esteja abaixo da capacidade padrão, a fim de garantir a disponibilidade do recurso o dimensionamento automático escalará horizontalmente para o valor padrão. Se a capacidade atual já for maior que a capacidade padrão, o dimensionamento automático não reduzirá horizontalmente.
- Clique em "Salvar"

Parabéns. Você criou com êxito sua configuração de dimensionamento para fazer o dimensionamento automático de seu aplicativo Web com base em uma métrica personalizada.

> Observação: as mesmas etapas são aplicáveis para começar a usar uma função de serviço de nuvem ou VMSS.

<!--Reference-->
[1]: https://docs.microsoft.com/en-us/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/monitoring-autoscale-scale-by-custom-metric/azure-monitor-launch.png
[4]: ./media/monitoring-autoscale-scale-by-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-scale-by-custom-metric/scale-by-custom-metric.png
[7]: ./media/monitoring-autoscale-scale-by-custom-metric/autoscale-setting-custom-metrics-ai.png
