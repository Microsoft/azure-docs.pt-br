---
title: Gerenciar réplicas de leitura-Azure PowerShell-banco de dados do Azure para PostgreSQL
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL usando o PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674524"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL usando o PowerShell

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço banco de dados do Azure para PostgreSQL usando o PowerShell. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Você pode criar e gerenciar réplicas de leitura usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell está em versão prévia, você precisa instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Depois que o módulo Az.PostgreSql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> O recurso ler réplica só está disponível para servidores do banco de dados do Azure para PostgreSQL nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor primário está em um desses tipos de preço.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O comando `New-AzPostgreSqlServerReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos em que o servidor de réplica é criado.  |
| Nome | mydemoreplicaserver | O nome do novo servidor de réplica criado. |

Para criar uma réplica de leitura entre regiões, use o parâmetro **Location** . O exemplo a seguir cria uma réplica na região **oeste dos EUA** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md).

Por padrão, as réplicas de leitura são criadas com a mesma configuração de servidor que a primária, a menos que o parâmetro **SKU** seja especificado.

> [!NOTE]
> É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que o primário para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-primary-server"></a>Listar réplicas para um servidor primário

Para exibir todas as réplicas de um determinado servidor primário, execute o seguinte comando:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O comando `Get-AzPostgreSQLReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou a ID do servidor primário. |

### <a name="stop-a-replica-server"></a>Parar um servidor de réplica

Parar um servidor de réplica de leitura promove a réplica de leitura a ser um servidor independente. Isso pode ser feito executando o `Update-AzPostgreSqlServer` cmdlet e definindo o valor de ReplicationRole como `None` .

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Excluir um servidor primário

> [!IMPORTANT]
> A exclusão de um servidor primário interrompe a replicação para todos os servidores de réplica e exclui o próprio servidor primário. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor primário, você pode executar o `Remove-AzPostgreSqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reiniciar o banco de dados do Azure para o servidor PostgreSQL usando o PowerShell](howto-restart-server-powershell.md)