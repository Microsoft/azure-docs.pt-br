---
title: Implantação & métricas HPA com informações de contêiner | Microsoft Docs
description: Este artigo descreve quais as métricas de implantação & HPA (autoescalar de Pod horizontal) são coletadas com informações de contêiner.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717699"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Implantação & métricas HPA com informações de contêiner

A partir do Agent versão *ciprod08072020*, o agente integrado do insights-Agent agora coleta métricas para implantações & HPAs.

## <a name="deployment-metrics"></a>Métricas de implantação

O insights de contêiner inicia automaticamente as implantações de monitoramento, coletando as seguintes métricas em intervalos de 60 segundos e armazenando-as na tabela **InsightMetrics** :

|Nome da métrica |Dimensão métrica (marcas) |Descrição |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, CreationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Número total de pods prontos direcionados para esta implantação (status. readyReplicas). Abaixo estão as dimensões dessa métrica. <ul> <li> implantação-nome da implantação </li> <li> namespace k8sNamespace-kubernetes para a implantação </li> <li> deploymentStrategy-estratégia de implantação a ser usada para substituir pods por novas (spec. Strategy. Type)</li><li> CreationTime-carimbo de data/hora da criação da implantação </li> <li> spec_replicas-número de pods desejadas (espec. réplicas) </li> <li>status_replicas_available-número total de pods disponíveis (pronto para pelo menos minReadySeconds) direcionados por essa implantação (status. availableReplicas)</li><li>status_replicas_updated-número total de pods não terminadas direcionadas por essa implantação que têm a especificação de modelo desejada (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>Métricas de HPA

O insights de contêiner inicia automaticamente o monitoramento de HPAs, coletando as seguintes métricas em intervalos de 60 segundos e armazenando-as na tabela **InsightMetrics** :

|Nome da métrica |Dimensão métrica (marcas) |Descrição |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, CreationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | Número atual de réplicas de pods gerenciadas por este AutoScaler (status. currentReplicas). Abaixo estão as dimensões dessa métrica. <ul> <li> hPa-nome do HPA </li> <li> k8sNamespace-kubernetes namespace para o HPA </li> <li> lastScaleTime – última vez em que o HPA dimensionou o número de pods (status. lastScaleTime)</li><li> CreationTime-carimbo de data/hora de criação do HPA </li> <li> spec_max_replicas-limite superior para o número de pods que pode ser definido pelo dimensionador automática (spec. maxReplicas) </li> <li> spec_min_replicas limite inferior para o número de réplicas para as quais o dimensionamento automática pode reduzir verticalmente (spec. minReplicas) </li><li>status_desired_replicas-número desejado de réplicas de pods gerenciadas por este dimensionador automática (status. desiredReplicas)</li><li>targetKind-tipo de destino do HPA (spec. scaleTargetRef. Kind) </li><li>targetName-nome do destino do HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Implantação & gráficos HPA 

As informações de contêiner incluem gráficos pré-configurados para as métricas listadas anteriormente na tabela como uma pasta de trabalho para cada cluster. Você pode encontrar as implantações & **implantações** de pasta de trabalho do HPA & hPa diretamente de um cluster AKs selecionando **pastas de trabalho** no painel esquerdo e na lista suspensa **exibir pastas de trabalho** no Insight.

## <a name="next-steps"></a>Próximas etapas

- Examine as [métricas de estado Kube em kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) para saber mais sobre as métricas de estado Kube.