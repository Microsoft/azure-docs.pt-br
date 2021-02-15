---
title: Excluir um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc
description: Excluir um grupo de servidores de hiperescala postgres habilitados para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac620909996b03a97a311e5f06c31d6dab8f1a60
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218639"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Excluir um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

Este documento descreve as etapas para excluir um grupo de servidores da configuração de seu arco do Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Excluir o grupo de servidores

Como exemplo, vamos considerar que desejamos excluir a instância _postgres01_ da configuração abaixo:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

O formato geral do comando de exclusão é:
```console
azdata arc postgres server delete -n <server group name>
```
Para obter mais detalhes sobre o comando de exclusão, execute:
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>Excluir o grupo de servidores usado neste exemplo

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperar as kubernetes de declarações de volume persistentes (PVCs)

A exclusão de um grupo de servidores não remove seus [PVCs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)associados. Isso ocorre por design. A intenção é ajudar o usuário a acessar os arquivos do banco de dados, se a exclusão da instância foi acidental. A exclusão de PVCs não é obrigatória. No entanto, é recomendada. Se você não recuperar esses PVCs, haverá erros, pois o cluster do Kubernetes vai entender que está ficando sem espaço em disco. Para recuperar os PVCs, siga estas etapas:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. liste os PVCs para o grupo de servidores que você excluiu

Para listar os PVCs, execute este comando:

```console
kubectl get pvc [-n <namespace name>]
```

Ele retorna a lista de PVCs, em particular os PVCs para o grupo de servidores que você excluiu. Por exemplo:

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Há 8 PVCs para este grupo de servidores.

### <a name="2-delete-each-of-the-pvcs"></a>2. excluir cada um dos PVCs

Exclua os dados e os PVCs de log para cada um dos nós de hiperescala do PostgreSQL (coordenador e trabalhadores) do grupo de servidores que você excluiu.

O formato geral deste comando é: 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Por exemplo:

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

Cada um desses comandos kubectl confirmará a exclusão bem-sucedida do PVC. Por exemplo:

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> Como indicado, não excluir os PVCs pode eventualmente obter o cluster kubernetes em uma situação em que ele gerará erros. Alguns desses erros podem incluir não conseguir fazer logon no cluster kubernetes com azdata, pois o pods pode ser removido dele devido a esse problema de armazenamento (comportamento normal de kubernetes).
>
> Por exemplo, você pode ver mensagens nos logs semelhantes a:  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>Próxima etapa
Criar [hiperescala do PostgreSQL habilitado para Arc do Azure](create-postgresql-hyperscale-server-group.md)
