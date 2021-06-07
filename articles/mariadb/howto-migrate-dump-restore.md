---
title: Migrar com despejo e restauração-banco de dados do Azure para MariaDB
description: Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados em seu Azure Database para MariaDB usando ferramentas como mysqldump, MySQL Workbench e phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628213"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrar seu banco de dados MariaDB para um banco de dados do Azure para MariaDB usando despejo e restauração

Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados no banco de dados do Azure para MariaDB:
- Despejar e restaurar usando uma ferramenta de linha de comando (usando mysqldump) 
- Despejar e restaurar usando phpMyAdmin

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a migrar seu banco de dados, faça o seguinte:
- Criar um [banco de dados do Azure para MariaDB Server-portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Instale o utilitário de linha de comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) .
- Baixe e instale o [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para executar comandos de despejo e restauração.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use ferramentas e utilitários comuns, como MySQL Workbench ou mysqldump, para se conectar e restaurar dados remotamente para o banco de dado do Azure para MariaDB. Use essas ferramentas no computador cliente com uma conexão com a Internet para se conectar ao banco de dados do Azure para MariaDB. Use uma conexão criptografada com SSL como uma prática recomendada de segurança. Para obter mais informações, consulte [configurar conectividade SSL no banco de dados do Azure para MariaDB](concepts-ssl-connection-security.md). Você não precisa mover os arquivos de despejo para qualquer local de nuvem especial ao migrar dados para o banco de dado do Azure para MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns de despejo e restauração
Você pode usar os utilitários do MySQL, como mysqldump e mysqlpump, para despejar e carregar bancos de dados em um Azure database for MariaDB Server em vários cenários comuns. 

- Use despejos de banco de dados quando estiver migrando um banco de dados inteiro. Essa recomendação é mantida quando você está movendo uma grande quantidade de dados ou quando deseja minimizar a interrupção do serviço para sites ou aplicativos em tempo real. 
-  Certifique-se de que todas as tabelas no banco de dados usem o mecanismo de armazenamento InnoDB quando você estiver carregando dados em seu Azure Database para MariaDB. O banco de dados do Azure para MariaDB dá suporte apenas ao mecanismo de armazenamento InnoDB e não a outros mecanismos de armazenamento. Se as tabelas estiverem configuradas com outros mecanismos de armazenamento, converta-as no formato de mecanismo InnoDB antes de migrá-las para o banco de dados do Azure para MariaDB.
   
   Por exemplo, se você tiver um aplicativo do WordPress ou um aplicativo Web que usa tabelas MyISAM, primeiro converta essas tabelas migrando-as para o formato InnoDB antes de restaurá-las para o banco de dados do Azure para MariaDB. Use a cláusula `ENGINE=InnoDB` para definir o mecanismo a ser usado para criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes de restaurá-los. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade quando estiver despejando bancos de dados, verifique se você está usando a mesma versão do MariaDB nos sistemas de origem e de destino. Por exemplo, se o servidor MariaDB existente for a versão 10,2, você deverá migrar para o banco de dados do Azure para MariaDB que está configurado para executar a versão 10,2. O `mysql_upgrade` comando não funciona em um banco de dados do Azure para o servidor MariaDB e não tem suporte. Se você precisar atualizar em versões do MariaDB, primeiro despeje ou exporte o banco de dados de versão anterior para uma versão posterior do MariaDB em seu próprio ambiente. Em seguida, você pode executar `mysql_upgrade` antes de tentar migrar para o banco de dados do Azure para MariaDB.

## <a name="performance-considerations"></a>Considerações sobre o desempenho
Para otimizar o desempenho quando você estiver despejando grandes bancos de dados, tenha em mente as seguintes considerações:
-   Use a `exclude-triggers` opção em mysqldump. Exclua os gatilhos de arquivos de despejo para evitar que os comandos do gatilho sejam acionados durante a restauração de dados. 
-   Use a `single-transaction` opção para definir o modo de isolamento da transação para leitura repetida e enviar uma instrução SQL de iniciar transação para o servidor antes de despejar dados. Despejar muitas tabelas em uma única transação faz com que um armazenamento extra seja consumido durante a restauração. A `single-transaction` opção e a `lock-tables` opção são mutuamente exclusivas. Isso ocorre porque as tabelas de bloqueio fazem com que todas as transações pendentes sejam confirmadas implicitamente. Para despejar tabelas grandes, combine a opção `single-transaction` com a opção `quick`. 
-   Use a sintaxe de várias linhas `extended-insert`, que inclui várias listas VALUE. Essa abordagem resulta em um arquivo de despejo menor e acelera as inserções quando o arquivo é recarregado.
-  Use a `order-by-primary` opção em mysqldump quando você estiver despejando bancos de dados, para que eles sejam inseridos no script na ordem de chave primária.
-   Use a `disable-keys` opção em mysqldump quando você estiver despejando dados, para desabilitar as restrições de chave estrangeira antes da carga. A desabilitação das verificações de chave estrangeira ajuda a melhorar o desempenho. Habilite as restrições e verifique os dados após o carregamento para garantir a integridade referencial.
-   Use tabelas particionadas, quando apropriado.
-   Carregar dados em paralelo. Evite muito paralelismo, o que pode fazer com que você atinja um limite de recursos e monitore recursos usando as métricas disponíveis no portal do Azure. 
-   Use a `defer-table-indexes` opção em mysqlpump quando você estiver despejando bancos de dados, para que a criação do índice ocorra após o carregamento do dado da tabela.
-   Copie os arquivos de backup para um repositório de blob do Azure e execute a restauração a partir daí. Essa abordagem deve ser muito mais rápida do que a realização da restauração pela Internet.

## <a name="create-a-backup-file"></a>Crie um arquivo de backup

Para fazer backup de um banco de dados MariaDB existente no servidor local ou em uma máquina virtual, execute o seguinte comando usando mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

Os seguintes parâmetros devem ser fornecidos:
- *\<uname>*: Seu nome de usuário do banco de dados 
- *\<pass>*: A senha do seu banco de dados (Observe que não há nenhum espaço entre-p e a senha) 
- *\<dbname>*: O nome do seu banco de dados 
- *\<backupfile.sql>*: O nome do arquivo para o backup do banco de dados 
- *\<--opt>*: A opção mysqldump 

Por exemplo, para fazer backup de um banco de dados chamado *TestDB* no servidor do MariaDB com o nome de usuário *testuser* e sem senha para um arquivo testdb_backup. SQL, use o comando a seguir. O comando faz backup do banco de dados `testdb` em um arquivo chamado `testdb_backup.sql`, que contém todas as instruções SQL necessárias para recriar o banco de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas para fazer backup em seu banco de dados, liste os nomes de tabela, separados por espaços. Por exemplo, para fazer backup somente das tabelas Table1 e Table2 do *TestDB*, siga este exemplo: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Para fazer backup de mais de um banco de dados de uma só vez, use a opção--Database e liste os nomes de banco de dados, separados por espaços. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Crie um banco de dados no servidor de destino
Crie um banco de dados vazio no Banco de Dados do Azure de destino para o servidor MariaDB no qual você deseja migrar os dados. Use uma ferramenta, como o MySQL Workbench, para criar o banco de dados. O banco de dados pode ter o mesmo nome do banco de dados que contém os dados despejados ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão no painel **visão geral** do banco de dados do Azure para MariaDB.

![Captura de tela do painel Visão geral de um banco de dados do Azure para MariaDB Server no portal do Azure.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

No MySQL Workbench, adicione as informações de conexão.

![Captura de tela do painel conexões MySQL no MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaurar seu banco de dados do MariaDB
Depois de criar o banco de dados de destino, você pode usar o comando MySQL ou o MySQL Workbench para restaurar os dados para o banco de dado recém-criado a partir do arquivo de despejo.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

Neste exemplo, você restaura os dados no banco de dados recém-criado no banco de dados do Azure de destino para o servidor MariaDB.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Exportar seu banco de dados MariaDB usando o phpMyAdmin

Para exportar, você pode usar a ferramenta phpMyAdmin comum, que pode já estar instalada localmente em seu ambiente. Para exportar o banco de dados MariaDB, faça o seguinte:
1. Abra o phpMyAdmin.
1. No painel esquerdo, selecione seu banco de dados e, em seguida, selecione o link **Exportar** . Aparece uma nova página para exibir o despejo do banco de dados.
1. Na área **Exportar** , selecione o link **selecionar tudo** para escolher as tabelas em seu banco de dados. 
1. Na área **Opções SQL** , selecione as opções apropriadas. 
1. Selecione a opção **salvar como arquivo** e a opção de compactação correspondente e, em seguida, selecione **ir**. No prompt, salve o arquivo localmente.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importar seu banco de dados usando o phpMyAdmin

O processo de importação é semelhante ao processo de exportação. Faça o seguinte:
1. Abra o phpMyAdmin. 
1. Na página configuração do phpMyAdmin, selecione **Adicionar** para adicionar o banco de dados do Azure para o servidor MariaDB. 
1. Insira os detalhes da conexão e as informações de logon.
1. Crie um banco de dados nomeado adequadamente e, em seguida, selecione-o no painel esquerdo. Para reescrever o banco de dados existente, selecione o nome do banco de dados, marque todas as caixas de seleção ao lado dos nomes de tabela e selecione **descartar** para excluir as tabelas existentes. 
1. Selecione o link **SQL** para mostrar a página onde você pode inserir comandos SQL ou carregar o arquivo SQL. 
1. Selecione o botão **procurar** para localizar o arquivo de banco de dados. 
1. Selecione o botão **ir** para exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="next-steps"></a>Próximas etapas
- [Conecte aplicativos ao banco de dados do Azure para MariaDB](./howto-connection-string.md).
