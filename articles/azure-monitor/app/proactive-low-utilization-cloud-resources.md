---
title: Detecção Inteligente - Baixa utilização de recursos de nuvem detectada pelo Azure Application Insights | Microsoft Docs
description: Monitorar aplicativos com o Azure Application Insights para baixa utilização de recursos de nuvem.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61297494"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Baixa utilização de CPU nos recursos de nuvem (versão prévia)

O Application Insights analisa automaticamente o consumo de CPU de cada instância de função no seu aplicativo e detecta instâncias com baixa utilização de CPU. Essa detecção permite diminuir os recursos do Azure e reduzir os custos, diminuindo o número de instâncias de função que cada função utiliza ou reduzindo o número de funções.

Este recurso não exige nenhuma configuração especial, diferente de [configurar contadores de desempenho](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) para seu aplicativo. Ele fica ativo quando seu aplicativo gera telemetria de contador de desempenho da CPU suficiente (% de tempo do processador).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Uma notificação comum ocorre quando muitas de suas instâncias de função web/trabalho exibem a baixa utilização da CPU.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Meu aplicativo realmente consume muitos recursos?
Não, uma notificação não significa que seu aplicativo realmente consuma muitos recursos. Embora esses padrões de baixa utilização da CPU geralmente indicam que o consumo de recursos pode ser diminuído, esse comportamento pode ser comum para sua função específica, ou pode ter uma justificativa de negócios natural e pode ser ignorado. Por exemplo, é possível que várias instâncias são necessárias para outros recursos, como memória/rede e não da CPU.

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de diagnóstico:
1. **Triagem.** A notificação mostra as funções em seu aplicativo que exibem a baixa utilização da CPU. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** Quantas funções exibiram baixa utilização da CPU, e quantas instâncias de cada função utilizam pouca CPU? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** A detecção contém a porcentagem da CPU utilizada, mostrando a utilização de CPU de cada instância ao longo do tempo. Você também pode usar o relatórios de vinculação para dar suporte a informações, como percentuais da utilização da CPU e itens relacionados para ajudá-lo a diagnosticar o problema.
