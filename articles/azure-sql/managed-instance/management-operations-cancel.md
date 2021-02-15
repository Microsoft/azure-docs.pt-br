---
title: Cancelar operações de gerenciamento
titleSuffix: Azure SQL Managed Instance
description: Saiba como cancelar as operações de gerenciamento de Instância Gerenciada do SQL do Azure.
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
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861537"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Cancelando operações de gerenciamento de Instância Gerenciada do SQL do Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Azure SQL Instância Gerenciada fornece a capacidade de cancelar algumas [operações de gerenciamento](management-operations-overview.md), como quando você implanta uma nova instância gerenciada ou atualiza as propriedades da instância. 

## <a name="overview"></a>Visão geral

 Todas as operações de gerenciamento podem ser categorizadas da seguinte maneira:

- Implantação de instância (nova criação de instância).
- Atualização de instância (alterando Propriedades de instância, como vCores ou armazenamento reservado).
- Exclusão da instância.

Você pode [monitorar o progresso e o status das operações de gerenciamento](management-operations-monitor.md) e cancelar algumas delas, se necessário. 

A tabela a seguir resume as operações de gerenciamento, se você pode ou não cancelá-las e sua duração geral típica:

Category  |Operação  |Cancelável  |Duração estimada de cancelamento  |
|---------|---------|---------|---------|
|Implantação |Criação de instância |Sim |90% das operações são concluídas em 5 minutos. |
|Atualizar |Expansão/redução do armazenamento de instância (Uso Geral) |Não |  |
|Atualizar |Expansão/redução do armazenamento de instância (Comercialmente Crítico) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualizar |Expansão e redução verticais de computação de instância (vCores) (Uso Geral) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualizar |Expansão e redução verticais de computação de instância (vCores) (Comercialmente Crítico) |Sim |90% das operações são concluídas em 5 minutos. |
|Atualizar |Alteração da camada de serviço de instância (Uso Geral para Comercialmente Crítico e vice-versa) |Sim |90% das operações são concluídas em 5 minutos. |
|Excluir |Exclusão da instância |Não |  |
|Excluir |Exclusão de cluster virtual (como operação iniciada pelo usuário) |Não |  |

## <a name="cancel-management-operation"></a>Cancelar operação de gerenciamento

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para cancelar as operações de gerenciamento usando o portal do Azure, siga estas etapas:

1. Vá para o [portal do Azure](https://portal.azure.com)
1. Vá para a folha de **visão geral** do seu instância gerenciada do SQL. 
1. Selecione a caixa de **notificação** ao lado da operação em andamento para abrir a página **operação em andamento** . 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Selecione a caixa operação em andamento para abrir a página operação em andamento.":::

1. Selecione **cancelar a operação** na parte inferior da página. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="Selecione Cancelar para cancelar a operação.":::

1. Confirme que você deseja cancelar a operação. 


Se a solicitação de cancelamento for realizada com êxito, a operação de gerenciamento será cancelada e resultará em uma falha. Você receberá uma notificação de que o cancelamento é bem-sucedida ou falha.

![Cancelando resultado da operação](./media/management-operations-cancel/canceling-operation-result.png)


Se a solicitação de cancelamento falhar ou o botão Cancelar não estiver ativo, significa que a operação de gerenciamento entrou em estado não cancelável e que será concluída em breve.  A operação de gerenciamento continuará sua execução até que seja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se você ainda não tiver Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

Para cancelar uma operação de gerenciamento, você precisa especificar o nome da operação de gerenciamento. Portanto, primeiro use o comando Get para recuperar a lista de operações e, em seguida, cancele a operação específica.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Para obter uma explicação detalhada de comandos, consulte [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) e [Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se você ainda não tiver o CLI do Azure instalado, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

Para cancelar a operação de gerenciamento, você precisa especificar o nome da operação de gerenciamento. Portanto, primeiro use o comando Get para recuperar a lista de operações e, em seguida, cancele a operação específica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Para obter uma explicação detalhada de comandos, consulte [AZ SQL mi op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Solicitação de implantação cancelada

Com a versão de API 2020-02-02, assim que a solicitação de criação de instância é aceita, a instância começa a existir como um recurso, não importa o progresso do processo de implantação (o status da instância gerenciada é **provisionamento**). Se você cancelar a solicitação de implantação de instância (nova criação de instância), a instância gerenciada passará do estado de **provisionamento** para **FailedToCreate**.

As instâncias que falharam ao criar ainda estão presentes como um recurso e: 

- Não são cobrados
- Não contar com relação aos limites de recursos (cota de sub-rede ou vCore)


> [!NOTE]
> Para minimizar o ruído na lista de recursos ou instâncias gerenciadas, exclua as instâncias que falharam ao implantar ou instâncias com implantações canceladas. 


## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, confira o [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e de comparação, confira [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
