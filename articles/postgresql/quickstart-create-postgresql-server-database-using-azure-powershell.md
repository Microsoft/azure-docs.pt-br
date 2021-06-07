---
title: 'Início Rápido: Criar servidor – Azure PowerShell – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Guia de início rápido para criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando o Azure PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 79a070dbd49ed6ee00dd790c8157057aebde30df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606707"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Início Rápido: Criar um Banco de Dados do Azure para PostgreSQL – Servidor único usando o PowerShell

Este início rápido descreve como usar o PowerShell para criar um servidor de Banco de Dados do Azure para PostgreSQL no grupo de recursos do Azure. Você pode usar o PowerShell para criar e gerenciar recursos do Azure interativamente ou em scripts.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo Az.PostgreSql PowerShell está em versão prévia, você precisa instalá-lo separadamente do módulo Az PowerShell usando o seguinte comando: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Depois que o módulo Az.PostgreSql PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e estará disponível nativamente no Azure Cloud Shell.

Se esta é a primeira vez que você usa o serviço de Banco de Dados do Azure para PostgreSQL, registre o provedor de recursos **Microsoft.DBforPostgreSQL**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um Banco de Dados do Azure para PostgreSQL com o cmdlet `New-AzPostgreSqlServer`. Um servidor pode gerenciar vários bancos de dados. Normalmente, um banco de dados separado é usado para cada projeto ou para cada usuário.

A tabela a seguir contém uma lista de parâmetros usados com frequência e valores de exemplo para o cmdlet `New-AzPostgreSqlServer`.

|        **Configuração**         | **Valor de exemplo** |                                                                                                                                                             **Descrição**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nome                       | mydemoserver     | Escolha um nome globalmente exclusivo no Azure que identifique o servidor do Banco de Dados do Azure para PostgreSQL. O nome do servidor pode conter apenas letras, números e o caractere de hífen (-). Todos os caracteres de letras maiúsculas especificados são convertidos automaticamente em de letras minúsculas durante o processo de criação. Ele deve conter de 3 a 63 caracteres. |
| ResourceGroupName          | myresourcegroup  | Forneça o nome do grupo de recursos do Azure.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | O nome da SKU. Segue a convenção **pricing-tier\_compute-generation\_vCores** em resumo. Para obter mais informações sobre o parâmetro da SKU, confira as informações após esta tabela.                                                                                                                                           |
| BackupRetentionDay         | 7                | Quanto tempo um backup deve ser retido. A unidade é dias. O intervalo é de 7 a 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | habilitado          | Indica se os backups com redundância geográfica devem ser habilitados para este servidor ou não. Esse valor não pode ser habilitado para servidores no tipo de preço básico e não pode ser alterado após a criação do servidor. Valores permitidos: Habilitado, Desabilitado.                                                                                                      |
| Location                   | westus           | A região do Azure para o servidor.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | habilitado          | Se o SSL deve ser habilitado para este servidor. Valores permitidos: Habilitado, Desabilitado.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | A capacidade de armazenamento do servidor (a unidade é megabytes). O StorageInMb válido é um mínimo de 5120 MB e aumenta em incrementos de 1024 MB. Para obter mais informações sobre limites de tamanho de armazenamento, confira [Tipos de preço do Banco de Dados do Azure para PostgreSQL](./concepts-pricing-tiers.md).                                                                               |
| Versão                    | 9.6.              | A versão principal do PostgreSQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | O nome de usuário para o logon de administrador. Não pode ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** nem **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | A senha do usuário administrador na forma de uma cadeia de caracteres segura. Ele deve conter entre 8 e 128 caracteres. A senha precisa conter caracteres de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos.                                       |

O valor do parâmetro **SKU** segue a convenção **pricing-tier\_compute-generation\_vCores**, conforme mostrado nestes exemplos.

- `-Sku B_Gen5_1` é mapeado para Básico, Gen 5 e 1 vCore. Essa opção é o menor SKU disponível.
- `-Sku GP_Gen5_32` mapeia para Uso Geral, Gen 5 e 32 vCores.
- `-Sku MO_Gen5_2` mapeia para Otimizado para Memória, Gen 5 e 2 vCores.

Para obter informações sobre valores de **SKU** válidos por região e para camadas, confira [Tipos de preço do Banco de Dados do Azure para PostgreSQL](./concepts-pricing-tiers.md).

O exemplo a seguir cria um servidor PostgreSQL na região **Oeste dos EUA** denominada **mydemoserver** no grupo de recursos **myresourcegroup** com um logon de administrador do servidor de **myadmin**. É um servidor Gen 5 no tipo de preço de uso geral com dois vCores e backups com redundância geográfica habilitados. Documente a senha usada na primeira linha do exemplo, pois essa é a senha da conta do administrador do servidor PostgreSQL.

> [!TIP]
> Um nome de servidor mapeia para um nome DNS e deve ser globalmente exclusivo no Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Considere usar o tipo de preço Básico se computação leve e E/S forem adequadas para sua carga de trabalho.

> [!IMPORTANT]
> Os servidores criados no tipo de preço básico não podem depois ser dimensionados para uso geral ou com otimização de memória e não podem ser replicados geograficamente.

## <a name="configure-a-firewall-rule"></a>Configurar uma regra de firewall

Crie uma regra de firewall no nível de servidor do Banco de Dados do Azure para PostgreSQL usando o cmdlet `New-AzPostgreSqlFirewallRule`. Uma regra de firewall no nível de servidor permite que um aplicativo externo, como a ferramenta de linha de comando `psql` ou o PostgreSQL Workbench, conecte-se ao servidor por meio do firewall do serviço Banco de Dados do Azure para PostgreSQL.

O exemplo a seguir cria uma regra de firewall chamada **AllowMyIP**, que permite conexões de um endereço IP específico, 192.168.0.1. Substitua um endereço IP ou um intervalo de endereços IP que correspondam à localização da qual você está se conectando.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> As conexões ao Banco de Dados do Azure para PostgreSQL se comunicam pela porta 5432. Se estiver tentando se conectar em uma rede corporativa, talvez o tráfego de saída pela porta 5432 não seja permitido. Nesse cenário, você só poderá se conectar ao servidor se o departamento de TI abrir a porta 5432.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host. Use o exemplo a seguir para determinar as informações de conexão. Anote os valores de **FullyQualifiedDomainName** e o **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Conectar-se ao banco de dados PostgreSQL usando psql

Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Você também pode acessar uma versão pré-instalada da ferramenta de linha de comando `psql` no Azure Cloud Shell selecionando o botão **Experimentar** em um exemplo de código neste artigo. Outras maneiras de acessar o Azure Cloud Shell são selecionar o botão **>_** na barra de ferramentas superior direita no portal do Azure ou acessar [shell.azure.com](https://shell.azure.com/).

1. Conecte-se ao servidor PostgreSQL do Azure usando o utilitário de linha de comando `psql`.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL `mydemoserver.postgres.database.azure.com` usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Se você preferir usar um caminho de URL para se conectar ao Postgres, codifique a URL de entrada @ do nome de usuário com `%40`. Por exemplo, a cadeia de conexão para psql seria `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Quando já estiver conectado ao servidor, crie um banco de dados em branco no prompt.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado:

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Conectar-se ao Servidor PostgreSQL usando pgAdmin

pgAdmin é uma ferramenta de software livre usada com PostgreSQL. Instale o pgAdmin por meio do [site do pgAdmin](https://www.pgadmin.org/). A versão de pgAdmin que você está usando pode ser diferente da que é usada neste Início Rápido. Leia a documentação de pgAdmin se precisar de orientação adicional.

1. Abra o aplicativo pgAdmin no computador cliente.

1. Na barra de ferramentas, vá para **Objeto**, passe o mouse sobre **Criar** e selecione **Servidor**.

1. Na caixa de diálogo **Criar – Servidor**, na guia **Geral**, insira um nome amigável exclusivo para o servidor, como **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="A guia Geral":::

1. Na caixa de diálogo **Criar - Servidor** da guia **Conexão**, preencha a tabela de configurações.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="A guia Conexão":::

    parâmetro pgAdmin |Valor|Descrição
    ---|---|---
    Nome/endereço do host | Nome do servidor | O valor do nome do servidor usado ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Nosso servidor de exemplo é **mydemoserver.postgres.database.azure.com.** Use o nome de domínio totalmente qualificado ( **\*.postgres.database.azure.com**), conforme mostrado no exemplo. Caso não se lembre do nome do servidor, siga as etapas da seção anterior para obter as informações de conexão.
    Porta | 5432 | A porta a ser usada ao se conectar ao Banco de Dados do Azure para o servidor PostgreSQL.
    Banco de dados de manutenção | *postgres* | O nome do banco de dados padrão gerado pelo sistema.
    Nome de Usuário | Nome de logon do administrador do servidor | O nome de usuário de logon do administrador do servidor fornecido ao criar o Banco de Dados do Azure para o servidor PostgreSQL anteriormente. Caso não se lembre do nome de usuário, siga as etapas da seção anterior para obter as informações de conexão. O formato é *nome de usuário\@nome do servidor*.
    Senha | Sua senha de administrador | A senha que você escolheu ao criar o servidor anteriormente neste Guia de início rápido.
    Função | Deixar em branco | Não é necessário fornecer um nome de função neste momento. Deixe o campo em branco.
    Modo SSL | *Exigir* | Você pode definir o modo TLS/SSL na guia SSL do pgAdmin. Por padrão, todos os servidores do Banco de Dados do Azure para PostgreSQL são criados com a imposição de TLS ligada. Para desligar a imposição de TLS, confira [Configurar a imposição de TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Clique em **Salvar**.

1. No painel **Navegador** à esquerda, expanda o nó **Servidores**. Selecione o servidor, por exemplo, **mydemoserver**. Clique nele para se conectar a ele.

1. Expanda o nó do servidor e expanda **Bancos de Dados** nele. A lista deve conter o banco de dados *postgres* existente e outros bancos de dados criados. Você pode criar vários bancos de dados por servidor com o Banco de Dados do Azure para PostgreSQL.

1. Clique com o botão direito do mouse em **Bancos de Dados**, escolha o menu **Criar** e, em seguida, selecione **Banco de Dados**.

1. Digite um nome de banco de dados de sua escolha no campo **Banco de Dados**, como **mypgsqldb2**.

1. Selecione o **Proprietário** do banco de dados na caixa de listagem. Escolha o nome de logon do administrador do servidor, como o exemplo, **myadmin**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Criar um banco de dados em pgAdmin":::

1. Selecione **Salvar** para criar um novo banco de dados em branco.

1. No painel **Procurar**, veja o banco de dados criado na lista de bancos de dados com o nome do servidor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste guia de início rápido não forem necessários para outro guia de início rápido ou tutorial, você poderá excluí-los executando o exemplo a seguir.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste guia de início rápido no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para excluir somente o servidor criado neste guia de início rápido sem excluir o grupo de recursos, use o cmdlet `Remove-AzPostgreSqlServer`.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um Banco de Dados do Azure para PostgreSQL usando o PowerShell](tutorial-design-database-using-powershell.md)