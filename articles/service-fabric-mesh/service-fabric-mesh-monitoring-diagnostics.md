---
title: Monitoramento e diagnóstico em aplicativos de malha de Service Fabric do Azure
description: Saiba mais sobre como monitorar e diagnosticar aplicativos na Malha do Service Fabric no Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626987"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

> [!IMPORTANT]
> A visualização da malha de Service Fabric do Azure foi desativada. Novas implantações não serão mais permitidas por meio da API de malha Service Fabric. O suporte para implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, consulte desativação da [Visualização da malha de Service Fabric do Azure](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Monitoramento e diagnóstico para a malha de Service Fabric é categorizado em três principais tipos de dados de diagnóstico:

- Logs de aplicativos - estes são definidos como os logs de aplicativos em contêineres, com base em como você tiver instrumentado seu aplicativo (por exemplo, logs de docker)
- Eventos de plataforma – eventos da plataforma Malha relevantes à sua operação de contêiner, incluindo atualmente encerramento, desativação e a ativação de contêiner.
- Métricas de contêiner - as métricas de desempenho e a utilização de recursos para seus contêineres (estatísticas do docker)

Este artigo discute as opções de monitoramento e diagnóstico para a versão de visualização mais recente disponível.

## <a name="application-logs"></a>Logs de aplicativo

Você pode exibir os logs de docker de seus contêineres implantados, em uma base por contêiner. No modelo de aplicativo de Malha do Service Fabric, cada contêiner é um pacote de código em seu aplicativo. Para ver os logs associados com um pacote de códigos, use o seguinte comando:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Você pode usar o comando "az mesh service-replica" para obter o nome da réplica. Os nomes de réplica estão incrementando inteiros de 0.

Aqui é com o que isso se parece para ver os logs do contêiner do aplicativo de votação VotingWeb.Code:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contêiner 

O ambiente de malha expõe algumas métricas que indicam como os contêineres estão sendo executados. As métricas a seguir estão disponíveis por meio do portal do Azure e da CLI do Azure monitor:

| Métrica | Descrição | Unidades|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu como uma porcentagem | % |
| MemoryUtilization | ActualMem/AllocatedMem como uma porcentagem | % |
| AllocatedCpu | CPU alocada de acordo com o modelo de Azure Resource Manager | Milicores |
| AllocatedMemory | Memória alocada de acordo com o modelo de Azure Resource Manager | MB |
| ActualCpu | Uso da CPU | Milicores |
| ActualMemory | Uso de memória | MB |
| ContainerStatus | 0-inválido: o status do contêiner é desconhecido <br> 1-pendente: o contêiner foi agendado para iniciar <br> 2-iniciando: o contêiner está no processo de início <br> 3-iniciado: o contêiner foi iniciado com êxito <br> 4-parando: o contêiner está sendo interrompido <br> 5-parado: o contêiner foi interrompido com êxito | N/D |
| ApplicationStatus | 0-desconhecido: o status não é recuperável <br> 1-pronto: o aplicativo está sendo executado com êxito <br> 2-Atualizando: há uma atualização em andamento <br> 3-Criando: o aplicativo está sendo criado <br> 4-excluindo: o aplicativo está sendo excluído <br> 5-falha: falha ao implantar o aplicativo | N/D |
| ServiceStatus | 0-inválido: o serviço atualmente não tem um status de integridade <br> 1-OK: o serviço está íntegro  <br> 2-AVISO: pode haver algo errado ao exigir investigação <br> 3-erro: há algo errado que precisa de investigação <br> 4-desconhecido: o status não é recuperável | N/D |
| ServiceReplicaStatus | 0-inválido: a réplica não tem um status de integridade no momento <br> 1-OK: o serviço está íntegro  <br> 2-AVISO: pode haver algo errado ao exigir investigação <br> 3-erro: há algo errado que precisa de investigação <br> 4-desconhecido: o status não é recuperável | N/D | 
| RestartCount | Número de reinicializações de contêiner | N/D |

> [!NOTE]
> Os valores de perstatus e ServiceReplicaStatus são os mesmos que o [HealthState](/dotnet/api/system.fabric.health.healthstate) em Service Fabric.

Cada métrica está disponível em dimensões diferentes para que você possa ver agregações em diferentes níveis. A lista atual de dimensões é a seguinte:

* ApplicationName
* ServiceName
* Emréplicaname
* CodePackageName

> [!NOTE]
> A dimensão CodePackageName não está disponível para aplicativos Linux. 

Cada dimensão corresponde a diferentes componentes do [modelo de aplicativo Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>CLI do Azure Monitor

Uma lista completa de comandos está disponível nos [documentos da CLI do Azure monitor](/cli/azure/monitor/metrics#az-monitor-metrics-list) , mas incluímos alguns exemplos úteis abaixo 

Em cada exemplo, a ID de recurso segue esse padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização da CPU dos contêineres em um aplicativo

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização de memória para cada réplica de serviço
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicializações para cada contêiner em uma janela de 1 hora 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média da CPU entre serviços denominados "VotingWeb" em uma janela de 1 hora
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrics Explorer

O Metrics Explorer é uma folha no portal em que você pode visualizar todas as métricas para seu aplicativo de malha. Essa folha pode ser acessada na página do aplicativo no portal e na folha do Azure monitor, a última que você pode usar para exibir as métricas para todos os recursos do Azure que dão suporte a Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas Azure Monitor, confira os [documentos Azure monitor CLI](/cli/azure/monitor/metrics#az-monitor-metrics-list).
