---
title: Configurar parâmetros do servidor-Azure PowerShell-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar os parâmetros de serviço no banco de dados do Azure para MariaDB usando o PowerShell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 891cb5c6e570269d0414e7b7cece760af143927f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662450"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Configurar parâmetros de servidor no banco de dados do Azure para MariaDB usando o PowerShell

Você pode listar, mostrar e atualizar parâmetros de configuração para um banco de dados do Azure para MariaDB Server usando o PowerShell. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente no nível do servidor. Use a [CLI do Azure](./howto-configure-server-parameters-cli.md), o [PowerShell](./howto-configure-server-parameters-using-powershell.md)ou o [portal do Azure](./howto-server-parameters.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Embora o módulo Az.MariaDb do PowerShell esteja em versão prévia, você precisará instalá-lo separadamente por meio do módulo do Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Depois que o módulo Az.MariaDb do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e ficará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Listar parâmetros de configuração do servidor para o Banco de Dados do Azure para MariaDB

Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o `Get-AzMariaDbConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para a definição de cada um dos parâmetros listados, consulte a seção de referência do MariaDB em [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o `Get-AzMariaDbConfiguration` cmdlet e especifique o parâmetro de **nome** .

Este exemplo mostra detalhes do parâmetro de configuração do servidor de **\_ \_ log de consulta lenta** para o servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor

Você também pode modificar o valor de um determinado parâmetro de configuração do servidor, que atualiza o valor de configuração subjacente para o mecanismo do servidor MariaDB. Para atualizar a configuração, use o `Update-AzMariaDbConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor **\_ \_ log de consulta lenta** do servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crescimento automático do armazenamento no banco de dados do Azure para MariaDB Server usando o PowerShell](howto-auto-grow-storage-powershell.md).