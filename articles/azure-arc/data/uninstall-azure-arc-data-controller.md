---
title: Excluir controlador de dados de arco do Azure
description: Excluir controlador de dados de arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91660669"
---
# <a name="delete-azure-arc-data-controller"></a>Excluir controlador de dados de arco do Azure

O artigo a seguir descreve como excluir um controlador de dados de arco do Azure.

Antes de prosseguir, verifique se todos os serviços de dados que foram criados no controlador de dados foram removidos da seguinte maneira:

## <a name="log-in-to-the-data-controller"></a>Fazer logon no controlador de dados

Faça logon no controlador de dados que você deseja excluir:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Listar & excluir os serviços de dados existentes

Execute o seguinte comando para verificar se há qualquer instância gerenciada do SQL criada:

```
azdata arc sql mi list
```

Para cada instância gerenciada do SQL da lista acima, execute o comando delete da seguinte maneira:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Da mesma forma, para verificar instâncias de hiperescala do PostgreSQL, execute:

```
azdata arc postgres server list
```

E, para cada instância de hiperescala PostgreSQL, execute o comando delete da seguinte maneira:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Excluir controlador

Depois que todas as instâncias gerenciadas do SQL e as instâncias de hiperescala do PostgreSQL tiverem sido removidas, o controlador de dados poderá ser excluído da seguinte maneira:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Remover SCCs (somente Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Excluir objetos de nível de cluster

Além dos objetos com escopo de namespace, se você também quiser excluir os objetos de nível de cluster, como o CRDs, `clusterroles` e `clusterrolebindings` executar os seguintes comandos:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Opcionalmente, exclua o namespace do controlador de dados Arc do Azure


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Próximas etapas

[O que são os serviços de dados habilitados para o Azure Arc?](overview.md)
