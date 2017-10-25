---
title: Criar um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI Azure | Microsoft Docs
description: "Guia de início rápido para criar e gerenciar o Banco de Dados do Azure para o servidor PostgreSQL usando a CLI(interface de linha de comando) do Azure."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 06/13/2017
ms.openlocfilehash: d78243abc140c7b3f0b99bdf56821b7920568550
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Criar um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
O Banco de Dados do Azure para PostgreSQL é um serviço gerenciado que permite executar, gerenciar e dimensionar os bancos de dados altamente disponíveis do PostgreSQL na nuvem. A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como criar um Banco de Dados do Azure para o servidor PostgreSQL em um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) usando a CLI do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

Se tiver várias assinaturas, escolha a que for adequada para cobrança do recurso. Selecione uma ID da assinatura específica em sua conta usando o comando [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myresourcegroup` no local `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Criar um Banco de Dados do Azure para o servidor PostgreSQL

Crie um [Banco de Dados do Azure para PostgreSQL](overview.md) usando o comando [az postgres server create](/cli/azure/postgres/server#create). Um servidor contém um grupo de bancos de dados gerenciados conjuntamente. 

O exemplo a seguir cria um servidor chamado `mypgserver-20170401` em seu grupo de recursos `myresourcegroup` com o logon de administrador de servidor `mylogin`. O nome de um servidor é mapeado para o nome DNS e, portanto, deve ser globalmente exclusivo no Azure. Substitua o `<server_admin_password>` com seu próprio valor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste início rápido. Lembre-se ou registre essas informações para o uso posterior.

Por padrão, o banco de dados **postgres** é criado em seu servidor. O [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) é um banco de dados padrão destinado a uso por usuários, utilitários e aplicativos de terceiros. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall no nível de servidor

Crie uma regra de firewall no nível de servidor do PostgreSQL do Azure com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create). Uma regra de firewall de nível de servidor permite que um aplicativo externo, tal como [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), conecte-se ao servidor por meio do firewall do serviço Azure PostgreSQL. 

Você pode definir uma regra de firewall que abranja um intervalo de IP aos quais você possa se conectar de sua rede. O exemplo a seguir usa [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) para criar uma regra de firewall `AllowAllIps` para um intervalo de endereços IP. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> O servidor PostgreSQL do Azure se comunica pela porta 5432. Ao se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 5432 talvez não seja permitido pelo firewall de sua rede. Faça seu departamento de TI abrir a porta 5432 para se conectar ao seu servidor de Banco de Dados SQL do Azure.

## <a name="get-the-connection-information"></a>Obter informações de conexão

Para se conectar ao servidor, é preciso fornecer credenciais de acesso e informações do host.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

O resultado está no formato JSON. Anote o **administratorLogin** e o **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>Conectar-se ao banco de dados PostgreSQL usando psql

Se o computador cliente tiver o PostgreSQL instalado, você poderá usar uma instância local do [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) para se conectar a um servidor PostgreSQL do Azure. Usaremos agora o utilitário de linha de comando psql para nos conectarmos ao servidor PostgreSQL do Azure.

1. Execute o comando psql a seguir para se conectar a um Banco de Dados do Azure para servidor PostgreSQL
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Por exemplo, o comando a seguir se conecta ao banco de dados padrão chamado **postgres** no seu servidor PostgreSQL **mypgserver-20170401.postgres.database.azure.com** usando as credenciais de acesso. Insira o `<server_admin_password>` que você escolheu quando uma senha foi solicitada a você.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Quando já estiver conectado ao servidor, crie um banco de dados em branco no prompt.
```sql
CREATE DATABASE mypgsqldb;
```

3.  No prompt, execute o seguinte comando para mudar a conexão para o banco de dados **mypgsqldb** recém-criado:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Conectar-se ao banco de dados PostgreSQL usando pgAdmin

Para se conectar ao servidor PostgreSQL do Azure usando a ferramenta GUI _pgAdmin_
1.  Inicie o aplicativo _pgAdmin_ no computador cliente. Você pode instalar o _pgAdmin_ de http://www.pgadmin.org/.
2.  Escolha **Adicionar Novo Servidor** no menu **Links Rápidos**.
3.  Na guia **geral** da caixa de diálogo **Criar – Servidor**, insira um nome amigável exclusivo para o servidor. Por exemplo, **Servidor PostgreSQL do Azure**.
 ![ferramenta pgAdmin – criar – servidor](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  Na caixa de diálogo **Criar – Servidor**, na guia **Conexão**:
    - Insira o nome totalmente qualificado do servidor (por exemplo, **mypgserver-20170401.postgres.database.azure.com**) na caixa **Nome do Host/Endereço**. 
    - Insira a porta 5432 na caixa **Porta**. 
    - Insira o **Logon de administrador do servidor (user@mypgserver)** obtido anteriormente neste início rápido e a senha que você inseriu quando criou o servidor nas caixas **Nome de Usuário** e **Senha**, respectivamente.
    - Selecione **Modo SSL** como **Requerer**. Por padrão, todos os servidores PostgreSQL do Azure são criados com a imposição de SSL ligada. Para DESLIGAR a imposição de SSL, consulte os detalhes em [Imposição de SSL](./concepts-ssl-connection-security.md).

    ![pgAdmin – Criar – Servidor](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Clique em **Salvar**.
6.  No painel esquerdo do Navegador, expanda os **Grupos de Servidores**. Escolha o **Servidor PostgreSQL do Azure**.
7.  Escolha o **Servidor** ao qual você se conectou e, em seguida, escolha **Bancos de Dados** sob ele. 
8.  Clique com o botão direito do mouse em **Bancos de Dados** para criar um banco de dados.
9.  Escolha um nome de banco de dados **mypgsqldb** e o proprietário para ele como o logon de administrador de servidor **mylogin**.
10. Clique em **Salvar** para criar um banco de dados em branco.
11. No **Navegador**, expanda o grupo **Servidores**. Expanda o servidor que você criou e veja o banco de dados **mypgsqldb** sob ele.
 ![pgAdmin – Criar – Banco de Dados](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que você criou no início rápido, excluindo o [Grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Se não planeja continuar, siga estas etapas para excluir todos os recursos criados por esse início rápido na CLI do Azure.

```azurecli-interactive
az group delete --name myresourcegroup
```

Se você apenas deseja excluir o servidor recém-criado, você pode executar o comando [az postgres server delete](/cli/azure/postgres/server#delete).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)
