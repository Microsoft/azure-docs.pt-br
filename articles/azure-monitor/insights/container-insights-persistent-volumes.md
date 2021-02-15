---
title: Configurar o monitoramento de PV com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters kubernetes com volumes persistentes com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704466"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Configurar o monitoramento de PV com Azure Monitor para contêineres

A partir do Agent versão *ciprod10052020*, o agente integrado do Azure monitor para contêineres agora dá suporte ao monitoramento do uso de PV (volume persistente).

## <a name="pv-metrics"></a>Métricas de PV

Azure Monitor para contêineres inicia automaticamente o monitoramento VP, coletando as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics** .

|Nome da métrica |Dimensão métrica (marcas) |Descrição |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Espaço usado em bytes para um volume persistente específico com uma declaração usada por um pod específico. `pvCapacityBytes` é dobrado como uma dimensão no campo marcas para reduzir o custo de ingestão de dados e simplificar as consultas.|

## <a name="monitor-persistent-volumes"></a>Monitorar volumes persistentes

Azure Monitor para contêineres inclui gráficos pré-configurados para essa métrica em uma pasta de trabalho para cada cluster. Você pode encontrar os gráficos na guia volume persistente da pasta de trabalho **detalhes da carga** de trabalho diretamente de um cluster AKs selecionando pastas de trabalho no painel esquerdo e na lista suspensa **exibir pastas de trabalho** no Insight. Você também pode habilitar um alerta recomendado para uso de PV, bem como consultar essas métricas em Log Analytics.  

![Exemplo de pasta de trabalho do PV de Azure Monitor](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as métricas de PV coletadas [aqui](./container-insights-agent-config.md).
