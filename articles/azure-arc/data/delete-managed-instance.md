---
title: Excluir o SQL Instância Gerenciada habilitado para Arc do Azure
description: Excluir o SQL Instância Gerenciada habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933809"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Excluir o SQL Instância Gerenciada habilitado para Arc do Azure
Este artigo descreve como você pode excluir uma Instância Gerenciada SQL habilitada para o Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Exibir instâncias gerenciadas do SQL existentes do Arc habilitado do Azure
Para exibir instâncias gerenciadas do SQL, execute o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser semelhante a esta:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Excluir um SQL Instância Gerenciada habilitado para um arco do Azure
Para excluir um Instância Gerenciada SQL, execute o seguinte comando:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

A saída deve ser semelhante a esta:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Recuperar as kubernetes de declarações de volume persistentes (PVCs)

A exclusão de um Instância Gerenciada SQL não remove seus [PVCs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)associados. Isso ocorre por design. A intenção é ajudar o usuário a acessar os arquivos do banco de dados, se a exclusão da instância foi acidental. A exclusão de PVCs não é obrigatória. No entanto, é recomendada. Se você não recuperar esses PVCs, eventualmente acabará com erros, pois o cluster kubernetes ficará sem espaço em disco. Para recuperar os PVCs, siga estas etapas:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. liste os PVCs para o grupo de servidores que você excluiu
Para listar os PVCs, execute o seguinte comando:
```console
kubectl get pvc
```

No exemplo a seguir, observe os PVCs para as instâncias gerenciadas do SQL que você excluiu.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. excluir cada um dos PVCs
Exclua os dados e os PVCs de log para cada uma das instâncias gerenciadas do SQL que você excluiu.
O formato geral deste comando é: 
```console
kubectl delete pvc <name of pvc>
```

Por exemplo:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Cada um desses comandos kubectl confirmará a exclusão bem-sucedida do PVC. Por exemplo:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Como indicado, não excluir os PVCs pode eventualmente obter o cluster kubernetes em uma situação em que ele gerará erros. Alguns desses erros podem incluir não conseguir fazer logon no cluster kubernetes com azdata, pois o pods pode ser removido dele devido a esse problema de armazenamento (comportamento normal de kubernetes).
>
> Por exemplo, você pode ver mensagens nos logs semelhantes a:  
> - Anotações: microsoft.com/ignore-pod-health: true  
> - Status: falha  
> - Motivo: removido  
> - Mensagem: o nó estava com o recurso insuficiente: armazenamento efêmero. O controlador de contêiner estava usando 16372Ki, que excede sua solicitação de 0.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Recursos e funcionalidades da Instância Gerenciada de SQL habilitada para Azure Arc](managed-instance-features.md)

[Comece criando um Controlador de Dados](create-data-controller.md)

Já criou um Controlador de Dados? [Criar uma Instância Gerenciada de SQL habilitada para Azure Arc](create-sql-managed-instance.md)