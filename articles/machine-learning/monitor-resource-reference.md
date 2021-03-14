---
title: Monitorar Azure Machine Learning referência de dados | Microsoft Docs
description: Documentação de referência para monitoramento Azure Machine Learning. Saiba mais sobre os dados & recursos coletados e disponíveis no Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f130fc0c65c49c33c838812fc2758619e0d1bca0
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521332"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitorando a referência de dados do Azure Machine Learning

Saiba mais sobre os dados e recursos coletados por Azure Monitor do seu espaço de trabalho Azure Machine Learning. Consulte [monitoramento Azure Machine Learning](monitor-azure-machine-learning.md) para obter detalhes sobre como coletar e analisar dados de monitoramento.

## <a name="metrics"></a>Métricas

Esta seção lista todas as métricas de plataforma coletadas automaticamente coletadas para Azure Machine Learning. O provedor de recursos para essas métricas é [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modelo**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Falha na implantação do modelo | Contagem | O número de implantações de modelo que falharam. |
| Implantação de modelo iniciada | Contagem | O número de implantações de modelo iniciadas. |
| Implantação de modelo bem-sucedida | Contagem | O número de implantações de modelo que tiveram êxito. |
| Falha no registro do modelo | Contagem | O número de registros de modelo que falharam. |
| Registro de modelo bem-sucedido | Contagem | O número de registros de modelo que foram bem-sucedidos. |

**Cota**

As informações de cota são apenas para Azure Machine Learning computação.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Núcleos ativos | Contagem | O número de núcleos de computação ativos. |
| Nós ativos | Contagem | O número de nós ativos. |
| Núcleos ociosos | Contagem | O número de núcleos de computação ociosos. |
| Nós ociosos | Contagem | O número de nós de computação ociosos. |
| Deixando núcleos | Contagem | O número de cores de saída. |
| Saindo de nós | Contagem | O número de nós de saída. |
| Núcleos preempçãos | Contagem | O número de núcleos admitidos. |
| Nós com preempção | Contagem | O número de nós admitidos. |
| Porcentagem de utilização de cota | Porcentagem | A porcentagem da cota usada. |
| Total de núcleos | Contagem | O total de núcleos. |
| Total de nós | Contagem | O total de nós. |
| Núcleos inutilizáveis | Contagem | O número de núcleos inutilizáveis. |
| Nós inutilizáveis | Contagem | O número de nós inutilizáveis. |

**Recurso**

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| CpuUtilization | Porcentagem | Quanto percentual de CPU foi utilizado para um determinado nó durante uma execução/trabalho. Essa métrica é publicada somente quando um trabalho está em execução em um nó. Um trabalho pode usar um ou mais nós. Essa métrica é publicada por nó. |
| GpuUtilization | Porcentagem | Quanto percentual de GPU foi utilizado para um determinado nó durante uma execução/trabalho. Um nó pode ter uma ou mais GPUs. Essa métrica é publicada por GPU por nó. |

**Executar**

Informações sobre execuções de treinamento.

| Métrica | Unidade | Descrição |
| ----- | ----- | ----- |
| Execuções concluídas | Contagem | O número de execuções concluídas. |
| Execuções com falha | Contagem | O número de execuções com falha. |
| Execuções iniciadas | Contagem | O número de execuções iniciadas. |

## <a name="metric-dimensions"></a>Dimensões de métrica

Para obter mais informações sobre o que são dimensões de métrica, confira [Métricas multidimensionais](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning tem as seguintes dimensões associadas a suas métricas.

| Dimensão | Descrição |
| ---- | ---- |
| Nome do cluster | O nome do recurso de cluster de computação. Disponível para todas as métricas de cota. |
| Nome da família de VMs | O nome da família de VMs usada pelo cluster. Disponível para porcentagem de utilização de cota. |
| Prioridade da VM | A prioridade da VM. Disponível para porcentagem de utilização de cota.
| CreatedTime | Disponível somente para CpuUtilization e GpuUtilization. |
| DeviceId | ID do dispositivo (GPU). Disponível somente para GpuUtilization. |
| NodeId | ID do nó criado onde o trabalho está em execução. Disponível somente para CpuUtilization e GpuUtilization. |
| RunId | ID da execução/trabalho. Disponível somente para CpuUtilization e GpuUtilization. |
| ComputeType | O tipo de computação usado pela execução. Somente disponível para execuções concluídas, execuções com falha e iniciados é executado. |
| PipelineStepType | O tipo de [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) usado na execução. Somente disponível para execuções concluídas, execuções com falha e iniciados é executado. |
| PublishedPipelineId | A ID do pipeline publicado usado na execução. Somente disponível para execuções concluídas, execuções com falha e iniciados é executado. |
| RunType | O tipo de execução. Somente disponível para execuções concluídas, execuções com falha e iniciados é executado. |

Os valores válidos para a dimensão RunType são:

| Valor | Descrição |
| ----- | ----- |
| Experimento | Execuções sem pipeline. |
| PipelineRun | Uma execução de pipeline, que é o pai de um StepRun. |
| StepRun | Uma execução para uma etapa de pipeline. |
| ReusedStepRun | Uma execução para uma etapa de pipeline que reutiliza uma execução anterior. |

## <a name="activity-log"></a>Log de atividades

A tabela a seguir lista as operações relacionadas a Azure Machine Learning que podem ser criadas no log de atividades.

| Operação | Descrição |
|:---|:---|
| Criar ou atualizar um espaço de trabalho Machine Learning | Um espaço de trabalho foi criado ou atualizado |
| CheckComputeNameAvailability | Verificar se um nome de computação já está em uso |
| Criar ou atualizar os recursos de computação | Um recurso de computação foi criado ou atualizado |
| Exclui os recursos de computação | Um recurso de computação foi excluído |
| Listar segredos | Na operação de segredos listados para um espaço de trabalho Machine Learning |

## <a name="resource-logs"></a>Logs de recursos

Esta seção lista os tipos de logs de recursos que você pode coletar para Azure Machine Learning espaço de trabalho.

Provedor de recursos e tipo: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Categoria | Nome de exibição |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Esquemas

Os esquemas a seguir estão em uso pelo Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Propriedade | Descrição |
|:--- |:---|
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| JobId | ID do trabalho enviado |
| Experimentoid | ID do experimento |
| Experimentoname | Nome do experimento |
| CustomerSubscriptionId | SubscriptionId em que o teste e o trabalho são enviados |
| WorkspaceName | Nome do espaço de trabalho do Machine Learning |
| ClusterName | Nome do cluster |
| ProvisioningState | Estado do envio do trabalho |
| ResourceGroupName | Nome do grupo de recursos |
| JobName | Nome do trabalho |
| ClusterId | ID do cluster |
| EventType | Tipo do evento de trabalho. Por exemplo, JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Estado do trabalho (a execução). Por exemplo, em fila, em execução, com êxito, com falha |
| ErrorDetails | Detalhes do erro de trabalho |
| CreationApiVersion | Versão da API usada para criar o trabalho |
| ClusterResourceGroupName | Nome do grupo de recursos do cluster |
| TFWorkerCount | Contagem de trabalhos de TF |
| TFParameterServerCount | Contagem do servidor de parâmetros TF |
| Ferramenta de | Tipo de ferramenta usada |
| RunInContainer | Sinalizador que descreve se o trabalho deve ser executado dentro de um contêiner |
| JobErrorMessage | mensagem detalhada de erro de trabalho |
| NodeId | ID do nó criado onde o trabalho está em execução |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ProvisioningState | Estado de provisionamento do cluster |
| ClusterName | Nome do cluster |
| ClusterType | Tipo do cluster |
| CreatedBy | Usuário que criou o cluster |
| CoreCount | Contagem dos núcleos no cluster |
| VmSize | Tamanho da VM do cluster |
| VmPriority | Prioridade dos nós criados dentro de um cluster dedicado/LowPriority |
| Dimensionartype | Tipo de dimensionamento manual/automático do cluster |
| InitialNodeCount | Contagem de nós inicial do cluster |
| MinimumNodeCount | Contagem mínima de nós do cluster |
| MaximumNodeCount | Contagem máxima de nós do cluster |
| NodeDeallocationOption | Como o nó deve ser desalocado |
| Publisher | Editor do tipo de cluster |
| Oferta | Oferta com a qual o cluster é criado |
| Sku | SKU do nó/VM criado dentro do cluster |
| Versão | Versão da imagem usada enquanto o nó/VM é criado |
| SubnetId | Sub-redeid do cluster |
| AllocationState | Estado de alocação do cluster |
| CurrentNodeCount | Contagem de nós atual do cluster |
| TargetNodeCount | Contagem de nós de destino do cluster ao escalar/reduzir verticalmente |
| EventType | Tipo de evento durante a criação do cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo ocioso em segundos antes que o cluster seja reduzido verticalmente |
| PreemptedNodeCount | Contagem de nós admitida preempção do cluster |
| IsResizeGrow | Sinalizador que indica que o cluster está aumentando verticalmente |
| VmFamilyName | Nome da família de VMs dos nós que podem ser criados dentro do cluster |
| LeavingNodeCount | Saindo da contagem de nós do cluster |
| UnusableNodeCount | Contagem de nós inutilizáveis do cluster |
| IdleNodeCount | Contagem de nós ociosos do cluster |
| RunningNodeCount | Contagem de nós em execução do cluster |
| PreparingNodeCount | Preparando a contagem de nós do cluster |
| QuotaAllocated | Cota alocada para o cluster |
| QuotaUtilized | Cota utilizada do cluster |
| AllocationStateTransitionTime | Tempo de transição de um estado para outro |
| ClusterErrorCodes | Código de erro recebido durante a criação ou o dimensionamento do cluster |
| CreationApiVersion | Versão da API usada ao criar o cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Propriedade | Descrição |
|:--- |:--- |
| TimeGenerated | Hora em que a entrada de log foi gerada |
| OperationName | Nome da operação associada ao evento de log |
| Categoria | Nome do evento de log, AmlComputeClusterNodeEvent |
| ClusterName | Nome do cluster |
| NodeId | ID do nó de cluster criado |
| VmSize | Tamanho da VM do nó |
| VmFamilyName | Família de VMs à qual o nó pertence |
| VmPriority | Prioridade do nó criado dedicado/LowPriority |
| Publisher | Editor da imagem da VM. Por exemplo, Microsoft-dsvm |
| Oferta | Oferta associada à criação da VM |
| Sku | SKU do nó/VM criado |
| Versão | Versão da imagem usada enquanto o nó/VM é criado |
| ClusterCreationTime | Hora em que o cluster foi criado |
| ResizeStartTime | Hora em que o cluster foi iniciado verticalmente |
| ResizeEndTime | Tempo durante o aumento/redução do cluster concluído |
| NodeAllocationTime | Hora em que o nó foi alocado |
| NodeBootTime | Hora em que o nó foi inicializado |
| StartTaskStartTime | Hora em que a tarefa foi atribuída a um nó e iniciada |
| StartTaskEndTime | Hora em que a tarefa atribuída a um nó terminou |
| TotalE2ETimeInSeconds | O nó de tempo total estava ativo |


## <a name="see-also"></a>Consulte também

- Consulte [monitoramento Azure Machine Learning](monitor-azure-machine-learning.md) para obter uma descrição do Azure Machine Learning de monitoramento.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.
