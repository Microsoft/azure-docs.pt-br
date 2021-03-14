---
title: Monitorar operações de gerenciamento
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre as diferentes maneiras de monitorar as operações de gerenciamento de Instância Gerenciada do SQL do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589341"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Monitorando operações de gerenciamento de Instância Gerenciada do SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Azure SQL Instância Gerenciada fornece monitoramento de [operações de gerenciamento](management-operations-overview.md) que você usa para implantar novas instâncias gerenciadas, atualizar propriedades de instância ou excluir instâncias quando não for mais necessário. 

## <a name="overview"></a>Visão geral

Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (nova criação de instância).
- Atualização de instância (alterando Propriedades de instância, como vCores ou armazenamento reservado).
- Exclusão da instância.

A maioria das operações de gerenciamento são [operações de longa execução](management-operations-overview.md#duration). Portanto, há a necessidade de monitorar o status ou seguir o andamento das etapas da operação. 

Há várias maneiras de monitorar as operações gerenciadas de gerenciamento de instância:

- [Implantações de grupo de recursos](../../azure-resource-manager/templates/deployment-history.md)
- [Log de atividades](../../azure-monitor/essentials/activity-log.md)
- [API de operações de instância gerenciada](#managed-instance-operations-api)


A tabela a seguir compara as opções de monitoramento da operação de gerenciamento: 

| Opção | Retenção | Dá suporte a cancelar | Criar | Atualizar | Excluir | Cancelar | Etapas |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Implantações de grupo de recursos | Infinito<sup>1</sup> | Não<sup>2</sup> | Visible | Visible | Não visível | Visible | Não visível |
| Log de atividades | 90 dias | No | Visible | Visible | Visible | Visible |  Não visível |
| API de operações de instância gerenciada | 24 horas | [Sim](management-operations-cancel.md) | Visible | Visible | Visible | Visible | Visible |
|  |  |  |  |  |  |  | |

<sup>1</sup> o histórico de implantação de um grupo de recursos é limitado a implantações de 800.

<sup>2</sup> as implantações de grupo de recursos dão suporte à operação cancelar. No entanto, devido à lógica de cancelamento, somente uma operação agendada para implantação depois que a ação de cancelamento é executada será cancelada. A implantação em andamento não é cancelada quando a implantação do grupo de recursos é cancelada. Como a implantação da instância gerenciada consiste em uma etapa de execução longa (da perspectiva do Azure Resource Manager), cancelar a implantação do grupo de recursos não cancelará a implantação da instância gerenciada e a operação será concluída. 

## <a name="managed-instance-operations-api"></a>API de operações de instância gerenciada

As APIs de operações de gerenciamento são especialmente projetadas para monitorar operações. O monitoramento de operações de instância gerenciada pode fornecer informações sobre parâmetros de operação e etapas de operação, bem como [Cancelar operações específicas](management-operations-cancel.md). Além dos detalhes da operação e do comando Cancel, essa API pode ser usada em scripts de automação com implantações de vários recursos-com base na etapa de progresso, você pode disparar uma implantação de recursos dependente.

Estas são as APIs: 

| Comando | Descrição |
| --- | --- |
|[Operações de Instância Gerenciada-obter](/rest/api/sql/managedinstanceoperations/get)|Obtém uma operação de gerenciamento em uma instância gerenciada.|
|[Operações de Instância Gerenciada-cancelar](/rest/api/sql/managedinstanceoperations/cancel)|Cancela a operação assíncrona na instância gerenciada.|
|[Instância Gerenciada operações-listar por Instância Gerenciada](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Obtém uma lista de operações executadas na instância gerenciada.|

> [!NOTE]
> Use a versão de API 2020-02-02 para ver a operação de criação de instância gerenciada na lista de operações. Essa é a versão padrão usada no portal do Azure e os pacotes mais recentes do PowerShell e do CLI do Azure.

## <a name="monitor-operations"></a>Monitorar operações

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, use a página **visão geral** da instância gerenciada para monitorar operações de instância gerenciada. 

Por exemplo, a **operação de criação** está visível no início do processo de criação na página **visão geral** : 

![Progresso da criação da instância gerenciada](./media/management-operations-monitor/monitoring-create-operation.png)

Selecione **operação em andamento** para abrir a página **operação em andamento** e exibir operações de **criação** ou **atualização** . Também é possível [Cancelar](management-operations-cancel.md) as operações dessa página.  

![Detalhes da operação de instância gerenciada](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> As operações de criação enviadas por meio de portal do Azure, PowerShell, CLI do Azure ou outras ferramentas usando a API REST versão 2020-02-02 [podem ser canceladas](management-operations-cancel.md). As versões da API REST mais antigas que 2020-02-02 usadas para enviar uma operação de criação iniciarão a implantação da instância, mas a implantação não será listada na API de operações e não poderá ser cancelada.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O cmdlet Get-AzSqlInstanceOperation Obtém informações sobre as operações em uma instância gerenciada. Você pode exibir todas as operações em uma instância gerenciada ou exibir uma operação específica fornecendo o nome da operação.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Para obter uma explicação detalhada de comandos, consulte [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A lista de op de Mi do AZ SQL Obtém uma lista de operações executadas na instância gerenciada. Se você ainda não tiver o CLI do Azure instalado, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Para obter uma explicação detalhada de comandos, consulte [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, confira o [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).