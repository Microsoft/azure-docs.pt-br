---
title: Gerenciar réplicas de leitura-Azure PowerShell-banco de dados do Azure para MySQL
description: Saiba como configurar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe33730fc11bfc18b7d67471e1077fb9490385d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541922"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Como criar e gerenciar réplicas de leitura no banco de dados do Azure para MySQL usando o PowerShell

Neste artigo, você aprenderá a criar e gerenciar réplicas de leitura no serviço de banco de dados do Azure para MySQL usando o PowerShell. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Você pode criar e gerenciar réplicas de leitura usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell está em versão prévia, você deve instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Depois que o módulo Az.MySql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> O recurso de réplica de leitura está disponível apenas para bancos de dados do Azure para servidores MySQL nas camadas de preços de uso geral ou de otimização de memória. Verifique se o servidor de origem está em um desses tipos de preço.

### <a name="create-a-read-replica"></a>Criar uma réplica de leitura

> [!IMPORTANT]
> Quando você cria uma réplica para uma fonte que não tem réplicas existentes, a origem será reiniciada primeiro para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

Um servidor de réplica de leitura pode ser criado usando o seguinte comando:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

O comando `New-AzMySqlReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos em que o servidor de réplica é criado.  |
| Nome | mydemoreplicaserver | O nome do novo servidor de réplica criado. |

Para criar uma réplica de leitura entre regiões, use o parâmetro **Location** . O exemplo a seguir cria uma réplica na região **oeste dos EUA** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Para saber mais sobre em quais regiões você pode criar uma réplica, visite o artigo [conceitos de réplica de leitura](concepts-read-replicas.md).

Por padrão, as réplicas de leitura são criadas com a mesma configuração de servidor que a origem, a menos que o parâmetro **SKU** seja especificado.

> [!NOTE]
> É recomendável que a configuração do servidor de réplica seja mantida em valores iguais ou maiores do que a origem para garantir que a réplica seja capaz de acompanhar o mestre.

### <a name="list-replicas-for-a-source-server"></a>Listar réplicas para um servidor de origem

Para exibir todas as réplicas de um determinado servidor de origem, execute o seguinte comando:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

O comando `Get-AzMySqlReplica` exige os seguintes parâmetros:

| Configuração | Valor de exemplo | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos para o qual o servidor de réplica será criado.  |
| ServerName | mydemoserver | O nome ou a ID do servidor de origem. |

### <a name="delete-a-replica-server"></a>Excluir um servidor de réplica

A exclusão de um servidor de réplica de leitura pode ser feita executando o `Remove-AzMySqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Excluir um servidor de origem

> [!IMPORTANT]
> A exclusão de um servidor de origem interrompe a replicação para todos os servidores de origem e exclui o próprio servidor mestre. Os servidores de réplica tornam-se servidores independentes que agora suportam leitura e gravação.

Para excluir um servidor de origem, você pode executar o `Remove-AzMySqlServer` cmdlet.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reiniciar o banco de dados do Azure para servidor MySQL usando o PowerShell](howto-restart-server-powershell.md)
