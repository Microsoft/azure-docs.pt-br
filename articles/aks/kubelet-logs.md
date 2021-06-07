---
title: Exibir logs de kubelet no AKS (Serviço de Kubernetes do Azure)
description: Saiba como exibir informações de solução de problemas nos logs do kubelet dos nós do AKS (serviço kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 355c665db2627fe04595a8b519b16bd475ebcadf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735141"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Obter logs de Kubelet dos nós de cluster do AKS (Serviço de Kubernetes do Azure)

Como parte da operação de um cluster AKS, talvez seja necessário examinar os logs para solucionar um problema. Interno ao portal do Azure é a capacidade de exibir logs para os componentes ou contêineres [mestres][aks-master-logs] do AKS [em um cluster AKs][azure-container-logs]. Ocasionalmente, talvez seja necessário obter logs do *kubelet* de um nó AKs para fins de solução de problemas.

Este artigo mostra como você pode usar `journalctl` o para exibir os logs do *kubelet* em um nó AKs.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Criar uma conexão SSH

Primeiro, crie uma conexão SSH com o nó no qual você precisa exibir os logs de *kubelet*. Essa operação está detalhada no documento [SSH nos nós de cluster do AKS (Serviço de Kubernetes do Azure)][aks-ssh].

## <a name="get-kubelet-logs"></a>Obter logs de kubelet

Depois de se conectar ao nó, execute o seguinte comando para efetuar pull dos logs do *kubelet* :

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> Para nós do Windows, os dados de log estão em `C:\k` e podem ser exibidos usando o comando *more* :
> ```
> more C:\k\kubelet.log
> ```

A saída de exemplo a seguir mostra os dados do log de *kubelet*:

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Próximas etapas

Se você precisar de informações adicionais sobre a solução de problemas do mestre de Kubernetes, consulte [exibir logs do nó mestre do Kubernetes no AKS][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: ./view-control-plane-logs.md
[azure-container-logs]: ../azure-monitor/containers/container-insights-overview.md