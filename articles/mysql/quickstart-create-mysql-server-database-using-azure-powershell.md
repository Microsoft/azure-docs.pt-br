---
title: 'Início Rápido: Projetar um servidor – Azure PowerShell – Banco de Dados do Azure para MySQL'
description: Este início rápido descreve como usar o PowerShell para criar um servidor de Banco de Dados do Azure para MySQL no grupo de recursos do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d12d447acb3b6bf2b6f84e9768e9f063a9a36b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542296"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Início Rápido: Criar um servidor de Banco de Dados do Azure para MySQL usando o PowerShell

Este início rápido descreve como usar o PowerShell para criar um servidor de Banco de Dados do Azure para MySQL no grupo de recursos do Azure. Você pode usar o PowerShell para criar e gerenciar recursos do Azure interativamente ou em scripts.

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

A tabela a seguir contém uma lista de parâmetros usados com frequência e valores de exemplo para o cmdlet `New-AzMySqlServer`.

|        **Configuração**         | **Valor de exemplo** |                                                                                                                                                             **Descrição**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome                       | mydemoserver     | Escolha um nome globalmente exclusivo no Azure que identifique o servidor do Banco de Dados do Azure para MySQL. O nome do servidor pode conter apenas letras, números e o caractere de hífen (-). Todos os caracteres de letras maiúsculas especificados são convertidos automaticamente em de letras minúsculas durante o processo de criação. Ele deve conter de 3 a 63 caracteres. |
| ResourceGroupName          | myresourcegroup  | Forneça o nome do grupo de recursos do Azure.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | O nome da SKU. Segue a convenção **pricing-tier\_compute-generation\_vCores** em resumo. Para obter mais informações sobre o parâmetro da SKU, confira as informações após esta tabela.                                                                                                                                           |
| BackupRetentionDay         | 7                | Quanto tempo um backup deve ser retido. A unidade é dias. O intervalo é de 7 a 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | habilitado          | Indica se os backups com redundância geográfica devem ser habilitados para este servidor ou não. Esse valor não pode ser habilitado para servidores no tipo de preço básico e não pode ser alterado após a criação do servidor. Valores permitidos: Habilitado, Desabilitado.                                                                                                      |
| Location                   | westus           | A região do Azure para o servidor.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | habilitado          | Se o SSL deve ser habilitado para este servidor. Valores permitidos: Habilitado, Desabilitado.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | A capacidade de armazenamento do servidor (a unidade é megabytes). O StorageInMb válido é um mínimo de 5120 MB e aumenta em incrementos de 1024 MB. Para obter mais informações sobre limites de tamanho de armazenamento, confira [Tipos de preço do Banco de Dados do Azure para MySQL](./concepts-pricing-tiers.md).                                                                               |
| Versão                    | 5.7              | A versão principal do MySQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | O nome de usuário para o logon de administrador. Não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | A senha do usuário administrador na forma de uma cadeia de caracteres segura. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.                                       |

O valor do parâmetro **SKU** segue a convenção **pricing-tier\_compute-generation\_vCores**, conforme mostrado nestes exemplos.

- `-Sku B_Gen5_1` é mapeado para Básico, Gen 5 e 1 vCore. Essa opção é o menor SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Para obter informações sobre valores de **SKU** válidos por região e para camadas, confira [Tipos de preço do Banco de Dados do Azure para MySQL](./concepts-pricing-tiers.md).

O exemplo a seguir cria um servidor MySQL na região **Oeste dos EUA** denominada **mydemoserver** no grupo de recursos **myresourcegroup** com um logon de administrador do servidor de **myadmin**. É um servidor Gen 5 no tipo de preço de uso geral com dois vCores e backups com redundância geográfica habilitados. Documente a senha usada na primeira linha do exemplo, pois essa é a senha da conta do administrador do servidor MySQL.

> [!TIP]
> Um nome de servidor mapeia para um nome DNS e deve ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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

## <a name="configure-ssl-settings"></a>Configurar definições de SSL

Por padrão, as conexões SSL entre o servidor e os aplicativos cliente são impostas. Essa configuração padrão garante a segurança de dados _em movimento_ ao criptografar o fluxo de dados pela Internet. Para esse início rápido, desabilite as conexões SSL para o servidor. Para saber mais, veja [Configurar conectividade SSL em seu aplicativo para se conectar com segurança ao Banco de Dados do Azure para MySQL](./howto-configure-ssl.md).

> [!WARNING]
> Desabilitar SSL não é recomendável para servidores de produção.

O exemplo a seguir desabilita o SSL no servidor do Banco de Dados do Azure para MySQL.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

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

Conecte-se ao seu servidor usando a ferramenta de linha de comando `mysql`. Para baixar e instalar a ferramenta de linha de comando, confira [Downloads da Comunidade do MySQL](https://dev.mysql.com/downloads/shell/). Você também pode acessar uma versão pré-instalada da ferramenta de linha de comando `mysql` no Azure Cloud Shell selecionando o botão **Experimentar** em um exemplo de código neste artigo. Outras maneiras de acessar o Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita na portal do Azure ou acessar [shell.azure.com](https://shell.azure.com/).

1. Conectar-se ao servidor usando a ferramenta de linha de comando `mysql`.

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Exibir o status do servidor.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Para saber mais sobre outros comandos, veja [Manual de Referência do MySQL 5.7 – Capítulo 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mysql-workbench"></a>Conectar-se ao servidor usando MySQL Workbench

1. Inicie o aplicativo MySQL Workbench no computador cliente. Para baixar e instalar o MySQL Workbench, confira [Baixar MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. Na caixa de diálogo **Configurar Nova Conexão**, insira as seguintes informações na guia **Parâmetros**:

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png" alt-text="configurar nova conexão":::

    |    **Configuração**    |           **Valor Sugerido**           |                      **Descrição**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Nome da Conexão   | Minha Conexão                           | Especificar um rótulo para essa conexão                        |
    | Método de Conexão | Padrão (TCP/IP)                       | Usar o protocolo TCP/IP para se conectar ao Banco de Dados do Azure para MySQL |
    | Nome do host          | `mydemoserver.mysql.database.azure.com` | O nome do servidor que você anotou anteriormente                           |
    | Porta              | 3306                                    | A porta padrão para MySQL                                 |
    | Nome de Usuário          | myadmin@mydemoserver                    | O logon de administrador do servidor que você anotou anteriormente                |
    | Senha          | *************                           | Use a senha de conta do administrador que você configurou anteriormente      |

1. Para testar se os parâmetros estão configurados corretamente, clique no botão **Testar Conexão**.

1. Selecione a conexão para se conectar ao servidor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste guia de início rápido não forem necessários para outro guia de início rápido ou tutorial, você poderá excluí-los executando o exemplo a seguir.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste guia de início rápido no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para excluir somente o servidor criado neste guia de início rápido sem excluir o grupo de recursos, use o cmdlet `Remove-AzMySqlServer`.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um Banco de Dados do Azure para MySQL usando o PowerShell](tutorial-design-database-using-powershell.md)