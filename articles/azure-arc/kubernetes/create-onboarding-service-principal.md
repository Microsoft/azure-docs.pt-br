---
title: Criar uma entidade de serviço de integração para o kubernetes habilitado para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Criar uma entidade de serviço de integração habilitada para o Azure Arc '
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: 610b355073473f0e492350753a523b7943666f13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121738"
---
# <a name="create-an-onboarding-service-principal-for-azure-arc-enabled-kubernetes"></a>Criar uma entidade de serviço de integração para o kubernetes habilitado para Arc do Azure

## <a name="overview"></a>Visão geral

Você pode conectar clusters kubernetes ao arco do Azure usando entidades de serviço com atribuições de função de privilégio limitado. Esse recurso é útil em pipelines de integração contínua e implantação contínua (CI/CD), como ações Azure Pipelines e GitHub.

Percorra as etapas a seguir para saber como usar entidades de serviço para conectar clusters kubernetes ao arco do Azure.

## <a name="create-a-new-service-principal"></a>Criar uma nova entidade de serviço

Crie uma nova entidade de serviço com um nome informativo que seja exclusivo para seu locatário Azure Active Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Saída:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Atribuir permissões

Atribua a função "cluster kubernetes-integração de arco do Azure" à entidade de serviço recém-criada. Essa função interna do Azure com permissões limitadas permite apenas que a entidade de segurança Registre clusters no Azure. A entidade com essa função atribuída não pode atualizar, excluir ou modificar outros clusters ou recursos dentro da assinatura.

Dadas as capacidades limitadas, os clientes podem facilmente reutilizar essa entidade para integrar vários clusters.

Você pode limitar as permissões ainda mais passando o `--scope` argumento apropriado ao atribuir a função. Isso permite que os administradores restrinjam o registro de cluster para a assinatura ou o escopo do grupo de recursos. Os cenários a seguir são compatíveis com vários parâmetros `--scope`:

| Recurso  | Argumento `scope`| Efeito |
| ------------- | ------------- | ------------- |
| Subscription | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | A entidade de serviço pode registrar o cluster em qualquer grupo de recursos sob essa assinatura. |
| Grupo de recursos | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | A entidade de serviço __só__ pode registrar clusters no grupo de recursos `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Saída:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Usar a entidade de serviço com o CLI do Azure

Referencie a entidade de serviço recém-criada com os seguintes comandos:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Próximas etapas

Regem a configuração do cluster [usando Azure Policy](./use-azure-policy.md).
