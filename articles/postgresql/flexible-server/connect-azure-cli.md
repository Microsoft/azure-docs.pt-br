---
title: 'Guia de início rápido: como se conectar ao Banco de Dados do Azure para PostgreSQL – Servidor Flexível usando a CLI do Azure'
description: Este guia de início rápido fornecerá vários modos de se conectar ao Banco de Dados do Azure para PostgreSQL – Servidor Flexível usando a CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479229"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Guia de início rápido: como se conectar ao Banco de Dados do Azure para PostgreSQL – Servidor Flexível e executar consultas nele usando a CLI do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para PostgreSQL – Servidor Flexível atualmente está em versão prévia pública.

Este guia de início rápido demonstrará de que modo se conectar a um Servidor Flexível do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure com o comando ```az postgres flexible-server connect```. Esse comando permitirá testar a conectividade com o servidor de banco de dados e executar consultas. Também será possível executar várias consultas usando um modo interativo. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma conta do Azure. Se você não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
- Instalar a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) (2.20.0 ou posterior)
- Fazer logon usando a CLI do Azure com o comando ```az login``` 
- Ativar a persistência de parâmetro usando o comando ```az config param-persist on```. A persistência de parâmetro ajudará você a usar o contexto local sem precisar repetir vários argumentos, como o grupo de recursos ou o local.

## <a name="create-an-postgresql-flexible-server"></a>Criar um Servidor Flexível do PostgreSQL

A primeira coisa que criaremos é um servidor PostgreSQL gerenciado. No [Azure Cloud Shell](https://shell.azure.com/), execute o script a seguir e anote o **nome do servidor**, o **nome de usuário** e a **senha** gerados desse comando.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
É possível fornecer argumentos adicionais para esse comando a fim de personalizá-lo. Confira todos os argumentos do comando [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Exibir todos os argumentos
É possível exibir todos os argumentos desse comando usando o argumento ```--help```. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testar a conexão do servidor de banco de dados
É possível testar e validar a conexão com o banco de dados do ambiente de desenvolvimento usando o comando.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Exemplo:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Você verá a saída caso a conexão tenha sido estabelecida com êxito.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Caso haja falha na conexão, tente usar estas soluções:
- Verifique se a porta 5432 está aberta no computador cliente.
- Verifique se o nome de usuário e a senha do administrador do servidor estão corretos
- Verifique se a regra de firewall foi configurada para o computador cliente
- Caso tenha configurado o servidor com acesso privado na rede virtual, verifique se o computador cliente está na mesma rede virtual.

## <a name="run-single-query"></a>Executar uma consulta única
É possível executar uma consulta única usando o comando com o argumento ```--querytext```, ```-q```.

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Exemplo:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Você verá uma saída semelhante ao mostrado abaixo:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Executar várias consultas usando um modo interativo
É possível executar várias consultas usando um modo **interativo**. Execute o comando a seguir para habilitar o modo interativo

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Exemplo:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Você verá a experiência do shell do **psql**, conforme mostrado abaixo:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o servidor](./how-to-manage-server-cli.md)
