---
title: 'Guia de início rápido: como se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível usando a CLI do Azure'
description: Este guia de início rápido fornecerá vários modos de se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível usando a CLI do Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481082"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Guia de início rápido: como se conectar ao Banco de Dados do Azure para MySQL – Servidor Flexível e executar consultas nele usando a CLI do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este guia de início rápido demonstrará de que modo se conectar a um Servidor Flexível do Banco de Dados do Azure para MySQL usando a CLI do Azure com o comando ```az mysql flexible-server connect```. Esse comando permitirá testar a conectividade com o servidor de banco de dados e executar consultas diretamente no servidor.  Também será possível usar a opção Executar o comando de modo interativo para executar várias consultas.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
- Instalar a versão mais recente da [CLI do Azure](/cli/azure/install-azure-cli) (2.20.0 ou posterior)
- Fazer logon usando a CLI do Azure com o comando ```az login``` 
- Ativar a persistência de parâmetro usando o comando ```az config param-persist on```. A persistência de parâmetro ajudará você a usar o contexto local sem precisar repetir vários argumentos, como o grupo de recursos ou o local etc.

## <a name="create-an-mysql-flexible-server"></a>Criar um Servidor Flexível do MySQL

A primeira coisa que criaremos é um servidor MySQL gerenciado. No [Azure Cloud Shell](https://shell.azure.com/), execute o script a seguir e anote o **nome do servidor**, o **nome de usuário** e a **senha** gerados desse comando.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

É possível fornecer argumentos adicionais para esse comando a fim de personalizá-lo. Confira todos os argumentos do comando [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Criar um banco de dados
Execute o comando a seguir para criar um banco de dados, **newdatabase**, caso ainda não tenha criado um.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Exibir todos os argumentos
É possível exibir todos os argumentos desse comando usando o argumento ```--help```. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testar a conexão do servidor de banco de dados
Execute o script abaixo para testar e validar a conexão com o banco de dados do ambiente de desenvolvimento.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Será possível conferir a seguinte saída de uma conexão com êxito:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Caso haja falha na conexão, tente usar estas soluções:
- Verifique se a porta 3306 está aberta no computador cliente.
- Verifique se o nome de usuário e a senha do administrador do servidor estão corretos
- Verifique se a regra de firewall foi configurada para o computador cliente
- Caso tenha configurado o servidor com acesso privado na rede virtual, verifique se o computador cliente está na mesma rede virtual.

## <a name="run-single-query"></a>Executar uma consulta única
Execute o comando a seguir para executar uma consulta única usando o argumento ```--querytext```, ```-q```.

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Você verá uma saída semelhante ao mostrado abaixo:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
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
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Exemplo:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Você verá a experiência do shell do **MySQL**, conforme mostrado abaixo:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
* [Conectar-se ao Banco de Dados do Azure para MySQL – Servidor Flexível com conexões criptografadas](how-to-connect-tls-ssl.md)
* [Gerenciar o servidor](./how-to-manage-server-cli.md)

