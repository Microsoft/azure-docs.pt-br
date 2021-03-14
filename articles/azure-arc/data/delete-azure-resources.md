---
title: Excluir recursos do Azure
description: Excluir recursos do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202035"
---
# <a name="delete-resources-from-azure"></a>Excluir recursos do Azure

> [!NOTE]
>  As opções para excluir recursos neste artigo são irreversíveis!

> [!NOTE]
>  Como o único modo de conectividade que é oferecido para os serviços de dados habilitados para Arc do Azure atualmente é o modo conectado indireto, a exclusão de uma instância do kubernetes não a removerá do Azure e a exclusão de uma instância do Azure não a removerá do kubernetes.  Por enquanto, a exclusão de um recurso é um processo de duas etapas e isso será melhorado no futuro.  No futuro, o kubernetes será a fonte de verdade e o Azure será atualizado para refletir.

Em alguns casos, talvez seja necessário excluir manualmente os recursos dos serviços de dados habilitados para o Azure Arc no Azure Resource Manager (ARM).  Você pode excluir esses recursos usando qualquer uma das opções a seguir.

- [Excluir recursos do Azure](#delete-resources-from-azure)
  - [Excluir um grupo de recursos inteiro](#delete-an-entire-resource-group)
  - [Excluir recursos específicos no grupo de recursos](#delete-specific-resources-in-the-resource-group)
  - [Excluir recursos usando o CLI do Azure](#delete-resources-using-the-azure-cli)
    - [Excluir recursos de instância gerenciada do SQL usando o CLI do Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Excluir recursos do grupo de servidores de hiperescala PostgreSQL usando o CLI do Azure](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Excluir recursos do controlador de dados de arco do Azure usando o CLI do Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Excluir um grupo de recursos usando o CLI do Azure](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Excluir um grupo de recursos inteiro
Se você estiver usando um grupo de recursos específico e dedicado para os serviços de dados habilitados para Arc do Azure e quiser excluir *tudo* dentro do grupo de recursos, poderá excluir o grupo de recursos que excluirá tudo dentro dele.  

Você pode excluir um grupo de recursos no portal do Azure fazendo o seguinte:

- Navegue até o grupo de recursos na portal do Azure em que os recursos dos serviços de dados habilitados para Arc do Azure foram criados.
- Clique no botão **excluir grupo de recursos** .
- Confirme a exclusão inserindo o nome do grupo de recursos e clique em **excluir**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Excluir recursos específicos no grupo de recursos

Você pode excluir recursos específicos de serviços de dados habilitados para o Azure ARC em um grupo de recursos no portal do Azure fazendo o seguinte:

- Navegue até o grupo de recursos na portal do Azure em que os recursos dos serviços de dados habilitados para Arc do Azure foram criados.
- Selecione todos os recursos a serem excluídos.
- Clique no botão excluir.
- Confirme a exclusão digitando ' Sim ' e clique em **excluir**.

## <a name="delete-resources-using-the-azure-cli"></a>Excluir recursos usando o CLI do Azure

Você pode excluir recursos específicos de serviços de dados habilitados para o Azure ARC usando o CLI do Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Excluir recursos de instância gerenciada do SQL usando o CLI do Azure

Para excluir recursos de instância gerenciada do SQL do Azure usando o CLI do Azure substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Excluir recursos do grupo de servidores de hiperescala PostgreSQL usando o CLI do Azure

Para excluir um recurso de grupo de servidores de hiperescala PostgreSQL do Azure usando o CLI do Azure substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Excluir recursos do controlador de dados de arco do Azure usando o CLI do Azure

> [!NOTE]
> Antes de excluir um controlador de dados de arco do Azure, você deve excluir todos os recursos da instância de banco de dado que ele está gerenciando.

Para excluir um controlador de dados de arco do Azure do Azure usando o CLI do Azure substitua os valores de espaço reservado no comando abaixo e execute-o.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Excluir um grupo de recursos usando o CLI do Azure

Você também pode usar o CLI do Azure para [excluir um grupo de recursos](../../azure-resource-manager/management/delete-resource-group.md).