---
title: O Azure Monitor para mapeamentos de região de contêineres
description: Este artigo descreve os mapeamentos de região com suporte entre o Azure Monitor para contêineres, o espaço de trabalho do Log Analytics e métricas personalizadas.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518073"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região com suporte pelo Azure Monitor para contêineres

 Ao habilitar o Azure Monitor para contêineres, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e um cluster do AKS e coleta de métricas personalizadas enviado para o Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos de suporte do espaço de trabalho do log Analytics

O espaço de trabalho do Log Analytics ou recursos de cluster AKS pode residir em outras regiões, e a tabela a seguir mostra o nosso mapeamentos.

|**Região do Cluster do AKS** | **Região do espaço de trabalho de análise de log** |
|-----------------------|------------------------------------|
|**África** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrália** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Pacífico Asiático** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasil** | |
|BrazilSouth | SouthCentralUS |
|**Canadá** ||
|Canadá Central |Canadá Central |
|CanadaEast |Canadá Central |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Coreia do Sul** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**EUA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> devido a restrições de capacidade, a região não estiver disponível durante a criação de novos recursos. Isso inclui um espaço de trabalho do Log Analytics. No entanto, os recursos vinculados pré-existentes na região devem continuam a funcionar.

## <a name="custom-metrics-supported-regions"></a>Regiões com suporte de métricas personalizadas

Coleta de métricas de serviços de Kubernetes do Azure (AKS) de clusters de nós e pods têm suporte para a publicação como métricas personalizadas no seguinte [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar seu cluster do AKS, examine [como habilitar o Azure Monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  