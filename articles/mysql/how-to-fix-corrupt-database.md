---
title: Resolver corrupção de banco de dados-banco de dados do Azure para MySQL
description: Neste artigo, você aprenderá como corrigir problemas de corrupção de banco de dados no banco de dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766889"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Solucionar problemas de danos no banco de dados do Azure para MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

A corrupção do banco de dados pode causar tempo de inatividade para seu aplicativo. Também é essencial resolver problemas de corrupção no tempo para evitar a perda de dados. Quando ocorrer corrupção do banco de dados, você verá esse erro nos logs do servidor: `InnoDB: Database page corruption on disk or a failed.`

Neste artigo, você aprenderá a resolver problemas de corrupção de banco de dados ou tabela. O banco de dados do Azure para MySQL usa o mecanismo InnoDB. Ele apresenta operações de reparo e verificação de corrupção automatizadas. O InnoDB verifica as páginas corrompidas executando somas de verificação em todas as páginas que lê. Se encontrar uma discrepância de soma de verificação, ela irá parar automaticamente o servidor MySQL.

Tente as seguintes opções para reduzir rapidamente os problemas de corrupção do banco de dados.

## <a name="restart-your-mysql-server"></a>Reiniciar o servidor MySQL

Normalmente, você percebe que um banco de dados ou tabela está corrompido quando seu aplicativo acessa a tabela ou o banco de dados. O InnoDB apresenta um mecanismo de recuperação de falhas que pode resolver a maioria dos problemas quando o servidor é reiniciado. Portanto, reiniciar o servidor pode ajudar o servidor a se recuperar de uma falha que fez com que o banco de dados esteja em estado inadequado.

## <a name="use-the-dump-and-restore-method"></a>Usar o método dump e Restore

Recomendamos que você resolva os problemas de corrupção usando um método de *despejo e restauração* . Esse método envolve:
1. Acessando a tabela corrompida.
1. Usando o utilitário mysqldump para criar um backup lógico da tabela. O backup manterá a estrutura da tabela e os dados contidos nela.
1. Recarregando a tabela no banco de dados.

### <a name="back-up-your-database-or-tables"></a>Fazer backup de seu banco de dados ou tabelas

> [!Important]
> - Verifique se você configurou uma regra de firewall para acessar o servidor do computador cliente. Para obter mais informações, consulte [Configurar uma regra de firewall em um único servidor](howto-manage-firewall-using-portal.md) e [Configurar uma regra de firewall no servidor flexível](flexible-server/how-to-connect-tls-ssl.md).
> - Use a opção SSL `--ssl-cert` para mysqldump se você tiver o SSL habilitado.

Crie um arquivo de backup da linha de comando usando mysqldump. Use este comando:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Descrições de parâmetros:
- `[ssl-cert=/path/to/pem]`: O caminho para o certificado SSL. Baixe o certificado SSL no computador cliente e defina o caminho nele no comando. Não use esse parâmetro se o SSL estiver desabilitado.
- `[host]`: Seu banco de dados do Azure para servidor MySQL.
- `[uname]`: O nome de usuário do administrador do servidor.
- `[pass]`: A senha para o usuário administrador.
- `[dbname]`: O nome do seu banco de dados.
- `[backupfile.sql]`: O nome de arquivo do backup do banco de dados.

> [!Important]
> - Para um único servidor, use o formato `admin-user@servername` para substituir `myserveradmin` os comandos a seguir.
> - Para o servidor flexível, use o formato `admin-user` para substituir `myserveradmin` os comandos a seguir.

Se uma tabela específica estiver corrompida, selecione tabelas específicas no banco de dados para fazer backup:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer backup de um ou mais bancos de dados, use a `--database` opção e liste os nomes de banco, separados por espaços:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Restaurar seu banco de dados ou tabelas

As etapas a seguir mostram como restaurar seu banco de dados ou tabelas. Depois de criar o arquivo de backup, você pode restaurar as tabelas ou os bancos de dados usando o utilitário MySQL. Execute este comando:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Aqui está um exemplo que restaura `testdb` de um arquivo de backup criado com mysqldump: 

> [!Important]
> - Para um único servidor, use o formato `admin-user@servername` para substituir `myserveradmin` o comando a seguir.
> - Para servidor flexível, use o formato ```admin-user``` para substituir `myserveradmin` o comando a seguir. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Próximas etapas
Se as etapas anteriores não resolverem o problema, você sempre poderá restaurar todo o servidor:
- [Restaurar servidor no banco de dados do Azure para MySQL-servidor único](howto-restore-server-portal.md)
- [Restaurar servidor no banco de dados do Azure para MySQL-servidor flexível](flexible-server/how-to-restore-server-portal.md)



