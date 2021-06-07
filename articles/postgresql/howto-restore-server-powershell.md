---
title: Backup e restauração-Azure PowerShell-banco de dados do Azure para PostgreSQL
description: Saiba como fazer backup e restaurar um servidor no banco de dados do Azure para PostgreSQL usando Azure PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63fffb5998b0b6a245db3f1c8fcf16f2d576936e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489754"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-postgresql-server-using-powershell"></a>Como fazer backup e restaurar um servidor do Banco de Dados do Azure para PostgreSQL usando o PowerShell

O backup do banco de dados do Azure para servidores PostgreSQL é feito periodicamente para habilitar os recursos de restauração. Com esse recurso de backup automático, você pode restaurar o servidor e todos os seus bancos de dados para um ponto anterior em um novo servidor.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

- O [módulo AZ PowerShell](/powershell/azure/install-az-ps) instalado localmente ou [Azure cloud Shell](https://shell.azure.com/) no navegador
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell está em versão prévia, você precisa instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Depois que o módulo Az.PostgreSql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se você optar por usar o PowerShell localmente, conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Definir configuração de backup

Na criação do servidor, você faz a escolha entre configurar o servidor para backups com redundância local ou geograficamente redundante.

> [!NOTE]
> Depois que um servidor é criado, o tipo de redundância que ele tem, geograficamente redundante versus redundância local, não pode ser alterado.

Ao criar um servidor por meio do `New-AzPostgreSqlServer` comando, o parâmetro **GeoRedundantBackup** decide sua opção de redundância de backup. Se **habilitado**, os backups com redundância geográfica serão feitos. Ou, se estiverem **desabilitados**, os backups com redundância local serão feitos.

O período de retenção de backup é definido pelo parâmetro **BackupRetentionDay** .

Para obter mais informações sobre como definir esses valores durante a criação do servidor, consulte [criar um banco de dados do Azure para o servidor PostgreSQL usando o PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md).

O período de retenção de backup de um servidor pode ser alterado da seguinte maneira:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

O exemplo anterior altera o período de retenção de backup de mydemoserver para 10 dias.

O período de retenção de backup determina quanto tempo uma restauração pontual pode ser recuperada, pois ela é baseada em backups disponíveis. A restauração pontual é descrita mais detalhadamente na próxima seção.

## <a name="server-point-in-time-restore"></a>Restauração pontual do servidor

Você pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela for removida acidentalmente, você poderá restaurar até o momento em que a remoção ocorreu. Depois, você pode recuperar a tabela e os dados ausentes da cópia restaurada do servidor.

Para restaurar o servidor, use o cmdlet `Restore-AzPostgreSqlServer` do PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando restore

Para restaurar o servidor, execute o exemplo do PowerShell a seguir.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

O conjunto de parâmetros **PointInTimeRestore** do `Restore-AzPostgreSqlServer` cmdlet requer os seguintes parâmetros:

| Configuração | Valor sugerido | Descrição  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  O grupo de recursos em que o servidor de origem existe.  |
| Nome | mydemoserver-restored | O nome do novo servidor que é criado pelo comando de restauração. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selecione um ponto no tempo para restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu próprio fuso horário local, como **2020-03-13T05:59:00-08:00**. Você também pode usar o formato UTC Zulu, por exemplo, **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Use o modo point-in-time para restaurar. |

Quando você restaura um servidor para um ponto anterior no tempo, é criado um servidor. O servidor original e seus bancos de dados do ponto no tempo especificado são copiados para o novo servidor.

Os valores de local e tipo de preço para o servidor restaurado permanecem iguais aos do servidor de origem.

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras devem ser configuradas separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="geo-restore"></a>Restauração geográfica

Se você configurou o servidor para backups com redundância geográfica, um novo servidor pode ser criado a partir do backup do servidor existente. Esse novo servidor pode ser criado em qualquer região em que o Banco de Dados do Azure para PostgreSQL esteja disponível.

Para criar um servidor usando um backup com redundância geográfica, use o `Restore-AzPostgreSqlServer` comando com o parâmetro **UseGeoRestore** .

> [!NOTE]
> Quando um servidor é criado pela primeira vez, talvez não fique imediatamente disponível para restauração geográfica. Pode demorar algumas horas para que os metadados necessários sejam preenchidos.

Para restaurar a localização geográfica do servidor, execute o seguinte exemplo do PowerShell:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Este exemplo cria um novo servidor chamado **mydemoserver-georestore** na região leste dos EUA que pertence ao **MyResource**. É um Uso geral, servidor Gen 5 com 8 vCores. O servidor é criado a partir do backup com redundância geográfica de **mydemoserver**, também no grupo de recursos **MyResource** Group.

Para criar o novo servidor em um grupo de recursos diferente do servidor existente, especifique o novo nome do grupo de recursos usando o parâmetro **ResourceGroupName** , conforme mostrado no exemplo a seguir:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

O conjunto de parâmetros **Georestore** do `Restore-AzPostgreSqlServer` cmdlet requer os seguintes parâmetros:

| Configuração | Valor sugerido | Descrição  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | O nome do grupo de recursos ao qual o novo servidor pertence.|
|Nome | mydemoserver-georestored | O nome do novo servidor. |
|Location | eastus | A localização do novo servidor. |
|UseGeoRestore | `<SwitchParameter>` | Use o modo geográfico para restaurar. |

Ao criar um novo servidor usando a restauração geográfica, ele herda o mesmo tamanho de armazenamento e tipo de preço do servidor de origem, a menos que o parâmetro **SKU** seja especificado.

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras devem ser configuradas separadamente para esse novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como gerar uma cadeia de conexão do banco de dados do Azure para PostgreSQL com o PowerShell](howto-connection-string-powershell.md)