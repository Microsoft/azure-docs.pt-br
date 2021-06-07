---
title: 'Tutorial: Projetar um servidor – Azure PowerShell – Banco de Dados do Azure para MySQL'
description: Este tutorial explica como criar e gerenciar o Banco de Dados do Azure para o servidor e banco de dados MySQL usando o PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: fd8294d60ed0af4e8d1eeb8a3cd07c737b69aadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011448"
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-powershell"></a>Tutorial: Criar um Banco de Dados do Azure para MySQL usando o PowerShell

O Banco de Dados do Azure para MySQL é um serviço de banco de dados relacional na nuvem da Microsoft, baseado no mecanismo de banco de dados MySQL Community Edition. Neste tutorial, você usa o PowerShell e outros utilitários para aprender a:

> [!div class="checklist"]
> - Criar um Banco de Dados do Azure para MySQL
> - Configurar o firewall do servidor
> - Use a [ferramenta de linha de comando mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) para criar um banco de dados
> - Carregar dados de exemplo
> - Consultar dados
> - Atualizar dados
> - Restaurar dados

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo Az.MySql PowerShell está em versão prévia, você deve instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Depois que o módulo Az.MySql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se esta for a primeira vez que você usa o serviço de Banco de Dados do Azure para MySQL, registre o provedor de recursos **Microsoft.DBforMySQL**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma ID de assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O seguinte exemplo cria um grupo de recursos chamado **myresourcegroup** na região **Oeste dos EUA**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL

Crie um Banco de Dados do Azure para MySQL com o cmdlet `New-AzMySqlServer`. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

O exemplo a seguir cria um servidor MySQL na região **Oeste dos EUA** denominada **mydemoserver** no grupo de recursos **myresourcegroup** com um logon de administrador do servidor de **myadmin**. É um servidor Gen 5 no tipo de preço de uso geral com dois vCores e backups com redundância geográfica habilitados. Documente a senha usada na primeira linha do exemplo, pois essa é a senha da conta do administrador do servidor MySQL.

> [!TIP]
> Um nome de servidor mapeia para um nome DNS e deve ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

O valor do parâmetro **SKU** segue a convenção **pricing-tier\_compute-generation\_vCores**, conforme mostrado nestes exemplos.

- `-Sku B_Gen5_1` é mapeado para Básico, Gen 5 e 1 vCore. Essa opção é o menor SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Para obter informações sobre valores de **SKU** válidos por região e para camadas, confira [Tipos de preço do Banco de Dados do Azure para MySQL](./concepts-pricing-tiers.md).

Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho.

> [!IMPORTANT]
> Os servidores criados no tipo de preço básico não podem depois ser dimensionados para uso geral ou com otimização de memória e não podem ser replicados geograficamente.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para MySQL usando o cmdlet `New-AzMySqlFirewallRule`. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando `mysql` ou o MySQL Workbench, conecte-se ao servidor por meio do firewall do serviço Banco de Dados do Azure para MySQL.

O exemplo a seguir cria uma regra de firewall chamada **AllowMyIP**, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou um intervalo de endereços IP que correspondam à localização da qual você está se conectando.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para MySQL se comunicam pela porta 3306. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 3306 não seja permitido. Nesse cenário, você só poderá se conectar ao servidor se o departamento de TI abrir a porta 3306.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host. Use o exemplo a seguir para determinar as informações de conexão. Anote os valores de **FullyQualifiedDomainName** e o **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Conectar-se ao servidor usando a ferramenta de linha de comando mysql

Conecte-se ao seu servidor usando a ferramenta de linha de comando `mysql`. Para baixar e instalar a ferramenta de linha de comando, confira [Downloads da Comunidade do MySQL](https://dev.mysql.com/downloads/shell/). Você também pode acessar uma versão pré-instalada da ferramenta de linha de comando `mysql` no Azure Cloud Shell selecionando o botão **Experimentar** em um exemplo de código neste artigo. Outras maneiras de acessar o Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita no portal do Azure ou acessar [shell.azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Criar um banco de dados

Quando você estiver conectado ao servidor, crie um banco de dados em branco.

```sql
mysql> CREATE DATABASE mysampledb;
```

No prompt, execute o seguinte comando para mudar a conexão para esse banco de dados recém-criado:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados

Agora que você sabe como se conectar ao Banco de Dados do Azure para MySQL, conclua algumas tarefas básicas.

Primeiro, crie uma tabela e carregue-a com alguns dados. Vamos criar uma tabela que armazena informações de inventário.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas

Agora que você tem uma tabela, insira alguns dados nela. Na janela do prompt de comando aberta, execute a consulta a seguir para inserir algumas linhas de dados.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Agora, você tem duas linhas de dados de exemplo na tabela criada anteriormente.

## <a name="query-and-update-the-data-in-the-tables"></a>Consultar e atualizar os dados nas tabelas

Execute a seguinte consulta para recuperar as informações da tabela do banco de dados.

```sql
SELECT * FROM inventory;
```

Também é possível atualizar os dados nas tabelas.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A linha é atualizada à medida que você recupera os dados.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo

Você pode restaurar o servidor para um ponto anterior no tempo. Os dados restaurados são copiados para um novo servidor e o servidor existente é deixado como está. Por exemplo, se uma tabela for removida acidentalmente, você poderá restaurar até o momento em que a remoção ocorreu. Depois, você pode recuperar a tabela e os dados ausentes da cópia restaurada do servidor.

Para restaurar o servidor, use o cmdlet `Restore-AzMySqlServer` do PowerShell.

### <a name="run-the-restore-command"></a>Executar o comando restore

Para restaurar o servidor, execute o exemplo do PowerShell a seguir.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Quando você restaura um servidor para um ponto anterior no tempo, é criado um servidor. O servidor original e seus bancos de dados do ponto no tempo especificado são copiados para o novo servidor.

Os valores de local e tipo de preço para o servidor restaurado permanecem iguais aos do servidor de origem.

Depois que o processo de restauração é concluído, localize o novo servidor e verifique se os dados são restaurados como esperado. O novo servidor tem o mesmo nome de logon e senha do administrador válidos para o servidor existente no momento em que a restauração foi iniciada. A senha pode ser alterada na página **Visão geral** do servidor.

O servidor criado durante uma restauração não tem o ponto de extremidade de serviço VNet existentes no servidor original. Essas regras devem ser configuradas separadamente para o novo servidor. As regras de firewall do servidor original são restauradas.

## <a name="clean-up-resources"></a>Limpar recursos

Se os recursos criados neste tutorial não forem necessários para outro guia de início rápido ou tutorial, exclua-os executando o exemplo a seguir.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste tutorial no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para excluir somente o servidor criado neste tutorial sem excluir o grupo de recursos, use o cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como fazer backup e restaurar um banco de dados do Azure para o servidor MySQL usando o PowerShell](howto-restore-server-powershell.md)