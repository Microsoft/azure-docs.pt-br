---
title: Alta disponibilidade para contêineres do Azure SQL Edge – Azure SQL Edge
description: Saiba mais sobre a alta disponibilidade para contêineres do Azure SQL Edge
keywords: Borda do SQL, contêineres, alta disponibilidade
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90932931"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Alta disponibilidade para contêineres do Azure SQL Edge

Crie e gerencie suas instâncias do Azure SQL Edge nativamente no kubernetes. Implante o Azure SQL Edge em contêineres do Docker gerenciados pelo [kubernetes](https://kubernetes.io/). No kubernetes, um contêiner com uma instância do Azure SQL Edge pode se recuperar automaticamente em caso de falha de um nó de cluster. Você pode configurar a imagem de contêiner do SQL Edge com uma declaração de volume persistente de kubernetes (PVC). O kubernetes monitora o processo do Azure SQL Edge no contêiner. Se o processo, Pod, contêiner ou nó falhar, o Kubernetes inicializará outra instância automaticamente e se reconectará ao armazenamento.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Contêineres do Azure SQL Edge no kubernetes

O kubernetes 1,6 e posterior tem suporte para [*classes de armazenamento*](https://kubernetes.io/docs/concepts/storage/storage-classes/), declarações de [*volume persistentes*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

Nessa configuração, o Kubernetes desempenha a função do orquestrador de contêineres. 

![Diagrama do Azure SQL Edge em um cluster kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

No diagrama anterior, `azure-sql-edge` é um contêiner em um [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). O Kubernetes orquestra os recursos no cluster. Um [conjunto de réplicas](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) verifica se o pod está recuperado automaticamente após uma falha de nó. Os aplicativos conectam-se ao serviço. Nesse caso, o serviço representa um balanceador de carga que hospeda um endereço IP que permanece o mesmo após a falha do `azure-sql-edge`.

No diagrama a seguir, o contêiner `azure-sql-edge` falhou. Como o orquestrador, o Kubernetes garante que a contagem correta de instâncias íntegras no conjunto de réplicas e inicia um novo contêiner de acordo com a configuração. O orquestrador inicia um novo pod no mesmo nó e `azure-sql-edge` se reconecta ao mesmo armazenamento persistente. O serviço conecta-se ao `azure-sql-edge` recriado.

![SQL do Azure no Edge em um cluster de Kubernetes após a falha do pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

No diagrama a seguir, o nó que hospeda o contêiner `azure-sql-edge` falhou. O orquestrador inicia o novo pod em um nó diferente e `azure-sql-edge` reconecta-se ao mesmo armazenamento persistente. O serviço conecta-se ao `azure-sql-edge` recriado.

![SQL do Azure no Edge em um cluster de Kubernetes após a falha do nó](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Para criar um contêiner no kubernetes, consulte [implantar um contêiner do Azure SQL Edge no kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Próximas etapas

Para implantar contêineres do Azure SQL Edge no serviço de kubernetes do Azure (AKS), consulte os seguintes artigos:
- [Implantar um contêiner do Azure SQL Edge no kubernetes](deploy-Kubernetes.md)
- [Machine learning e inteligência artificial com o ONNX no SQL no Edge](onnx-overview.md).
- [Como criar uma solução de IoT de ponta a ponta com o SQL no Edge usando o IoT Edge](tutorial-deploy-azure-resources.md).
- [Streaming de dados no SQL do Azure no Edge](stream-data.md)