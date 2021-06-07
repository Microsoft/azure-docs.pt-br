---
title: Configurar o monitoramento de GPU com informações de contêiner | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters kubernetes com os nós habilitados para NVIDIA e AMD GPU com informações de contêiner.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731860"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Configurar o monitoramento de GPU com informações de contêiner

A partir do Agent versão *ciprod03022019*, o agente integrado do insights de contêineres agora dá suporte ao monitoramento do uso de GPU (unidades de processamento gráfico) em nós de cluster kubernetes com reconhecimento de GPU e monitor de pods/contêineres solicitando e usando recursos de GPU.

## <a name="supported-gpu-vendors"></a>Fornecedores de GPU com suporte

O insights de contêiner dá suporte ao monitoramento de clusters de GPU dos seguintes fornecedores de GPU:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

O insights de contêiner inicia automaticamente o monitoramento do uso de GPU em nós e a GPU solicitando pods e cargas de trabalho coletando as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics** .

>[!NOTE]
>Depois de provisionar o cluster com nós GPU, verifique se o [Driver GPU](../../aks/gpu-cluster.md) está instalado conforme exigido pelo AKs para executar cargas de trabalho de GPU. As informações de contêiner coletam métricas de GPU por meio de pods de driver de GPU em execução no nó. 

|Nome da métrica |Dimensão métrica (marcas) |Descrição |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor|Porcentagem de tempo sobre o período de exemplo passado (60 segundos) durante o qual a GPU estava ocupada/processando ativamente para um contêiner. O ciclo de serviço é um número entre 1 e 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Cada contêiner pode especificar limites como uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Cada contêiner pode solicitar uma ou mais GPUs. Não é possível solicitar ou limitar uma fração de uma GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Quantidade de memória de GPU em bytes disponíveis para uso em um contêiner específico. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Quantidade de memória de GPU em bytes usada por um contêiner específico. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número de GPUs em um nó que pode ser usado pelo kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Número total de GPUs em um nó. |

## <a name="gpu-performance-charts"></a>Gráficos de desempenho de GPU 

As informações de contêiner incluem gráficos pré-configurados para as métricas listadas anteriormente na tabela como uma pasta de trabalho GPU para cada cluster. Confira [pastas de trabalho em informações de contêiner](../insights/container-insights-reports.md) para obter uma descrição das pastas de trabalho disponíveis para o contêiner insights.

## <a name="next-steps"></a>Próximas etapas

- Consulte [usar GPUs para cargas de trabalho de computação intensiva no serviço de kubernetes do Azure](../../aks/gpu-cluster.md) (AKs) para saber como implantar um cluster AKs que inclui nós habilitados para GPU.

- Saiba mais sobre os [SKUs de VM otimizadas para GPU em Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Examine o [suporte a GPU no kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) para saber mais sobre o suporte experimental kubernetes para gerenciar GPUs em um ou mais nós em um cluster.
