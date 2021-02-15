---
title: Azure Monitor para mapeamentos de região de contêineres
description: Descreve os mapeamentos de região com suporte entre Azure Monitor para contêineres, espaço de trabalho Log Analytics e métricas personalizadas.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272898"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região com suporte pelo Azure Monitor para contêineres

 Ao habilitar Azure Monitor para contêineres, somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e um cluster AKS e coletar métricas personalizadas enviadas para Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos com suporte do Log Analytics Workspace

Regiões AKS com suporte são listadas em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). O espaço de trabalho Log Analytics deve estar na mesma região, exceto pelas regiões listadas na tabela a seguir. Assista às [notas de versão do AKS](https://github.com/Azure/AKS/releases) para obter atualizações.


|**Região de cluster AKS** | **Região do espaço de trabalho Log Analytics** |
|-----------------------|------------------------------------|
|**África** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrália** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brasil** | |
|BrazilSouth | SouthCentralUS |
|**Canadá** ||
|CanadaEast |Canadá Central |
|**Europa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**Índia** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanWest |JapanEast |
|**Coreia do Sul** | |
|KoreaSouth |KoreaCentral |
|**EUA** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isso inclui um espaço de trabalho Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="custom-metrics-supported-regions"></a>Regiões com suporte para métricas personalizadas

A coleta de métricas dos nós de clusters AKS (serviços Kubernetess do Azure) e pods tem suporte para publicação como métricas personalizadas somente nas seguintes [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
