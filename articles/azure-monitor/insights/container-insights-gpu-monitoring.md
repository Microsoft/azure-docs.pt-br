---
title: Configurar o monitoramento de GPU com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o monitoramento de clusters kubernetes com os nós habilitados para NVIDIA e AMD GPU com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: e391117ab57211aa5d178d11c27b934b4ccd37f8
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905580"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Configurar o monitoramento de GPU com Azure Monitor para contêineres

A partir da versão do Agent *ciprod03022019*, o agente integrado do Azure monitor para contêineres agora dá suporte ao monitoramento do uso de GPU (unidades de processamento gráfico) em nós de cluster kubernetes com reconhecimento de GPU e monitor de pods/contêineres solicitando e usando recursos de GPU.

## <a name="supported-gpu-vendors"></a>Fornecedores de GPU com suporte

Azure Monitor para contêineres dá suporte ao monitoramento de clusters GPU dos seguintes fornecedores de GPU:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor para contêineres inicia automaticamente o monitoramento do uso de GPU em nós e a GPU solicitando pods e cargas de trabalho coletando as seguintes métricas em intervalos de 60sec e armazenando-as na tabela **InsightMetrics** .

>[!NOTE]
>Depois de provisionar o cluster com nós GPU, verifique se o [Driver GPU](../../aks/gpu-cluster.md) está instalado conforme exigido pelo AKs para executar cargas de trabalho de GPU. Azure Monitor para contêineres coletam métricas de GPU por meio de pods de driver de GPU em execução no nó. 

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

Azure Monitor para contêineres inclui gráficos pré-configurados para as métricas listadas anteriormente na tabela como uma pasta de trabalho GPU para cada cluster. Consulte [pastas de trabalho no Azure monitor para contêineres](container-insights-reports.md) para obter uma descrição das pastas de trabalho disponíveis para Azure monitor para contêineres.

## <a name="next-steps"></a>Próximas etapas

- Consulte [usar GPUs para cargas de trabalho de computação intensiva no serviço de kubernetes do Azure](../../aks/gpu-cluster.md) (AKs) para saber como implantar um cluster AKs que inclui nós habilitados para GPU.

- Saiba mais sobre os [SKUs de VM otimizadas para GPU em Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Examine o [suporte a GPU no kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) para saber mais sobre o suporte experimental kubernetes para gerenciar GPUs em um ou mais nós em um cluster.
