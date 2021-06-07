---
title: Configurar parâmetros do servidor-Azure PowerShell-banco de dados do Azure para PostgreSQL
description: Este artigo descreve como configurar os parâmetros de serviço no banco de dados do Azure para PostgreSQL usando o PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6e9bf55aa46e2206928a5f93285a4001dc731f64
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604287"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Personalizar os parâmetros de servidor do banco de dados do Azure para PostgreSQL usando o PowerShell

Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor de banco de dados do Azure para PostgreSQL usando o PowerShell. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell está em versão prévia, você precisa instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Depois que o módulo Az.PostgreSql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar os parâmetros de configuração de servidor para o bando de dados do Azure para servidor PostgreSQL

Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o `Get-AzPostgreSqlConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do PostgreSQL em [variáveis de ambiente](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o `Get-AzPostgreSqlConfiguration` cmdlet e especifique o parâmetro de **nome** .

Este exemplo mostra detalhes do parâmetro de configuração do servidor de **\_ \_ log de consulta lenta** para o servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor

Você pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar a configuração, use o `Update-AzPostgreSqlConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor **\_ \_ log de consulta lenta** do servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crescimento automático do armazenamento no banco de dados do Azure para servidor PostgreSQL usando o PowerShell](howto-auto-grow-storage-powershell.md).