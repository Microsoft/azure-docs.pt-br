---
title: Configurar parâmetros do servidor-Azure PowerShell-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros de serviço no banco de dados do Azure para MySQL usando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 973025dfd8c0141ed0884539fe5207cc64ec822c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541854"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Configurar parâmetros de servidor no banco de dados do Azure para MySQL usando o PowerShell

Você pode listar, mostrar e atualizar parâmetros de configuração para um servidor de banco de dados do Azure para MySQL usando o PowerShell. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado.

>[!Note]
> Os parâmetros do servidor podem ser atualizados globalmente no nível do servidor. Use a [CLI do Azure](./howto-configure-server-parameters-using-cli.md), o [PowerShell](./howto-configure-server-parameters-using-powershell.md)ou o [portal do Azure](./howto-server-parameters.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell está em versão prévia, você deve instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Depois que o módulo Az.MySql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração de servidor para o Banco de Dados do Azure para MySQL

Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o `Get-AzMySqlConfiguration` cmdlet.

O exemplo a seguir lista os parâmetros de configuração do servidor para o **mydemoserver** do servidor no grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor

Para mostrar detalhes sobre um parâmetro de configuração específico para um servidor, execute o `Get-AzMySqlConfiguration` cmdlet e especifique o parâmetro de **nome** .

Este exemplo mostra detalhes do parâmetro de configuração do servidor de **\_ \_ log de consulta lenta** para o servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor

Você também pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor MySQL. Para atualizar a configuração, use o `Update-AzMySqlConfiguration` cmdlet.

Para atualizar o parâmetro de configuração do servidor **\_ \_ log de consulta lenta** do servidor **mydemoserver** em grupo de recursos **MyResource** Group.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Crescimento automático do armazenamento no banco de dados do Azure para servidor MySQL usando o PowerShell](howto-auto-grow-storage-powershell.md).
