---
title: Migrar todo o seu banco de dados MariaDB utilizando o descarte e a resconstituição no Banco de Dados do Azure para MariaDB
description: Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados no Banco de Dados do Azure para MariaDB, usando ferramentas como mysqldump, MySQL Workbench e PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bcb76fcbba02bf53b48cc462e3dad8f264db02ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745922"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrar seu banco de dados MariaDB para o banco de dados do Azure para MariaDB usando despejo e restauração
Este artigo explica duas maneiras comuns de fazer backup e restaurar bancos de dados no banco de dados do Azure para MariaDB
- Despejo e restauração da linha de comando (usando mysqldump) 
- Despejo e restauração usando o PHPMyAdmin

## <a name="before-you-begin"></a>Antes de começar
Para acompanhar este guia de instruções, você precisa do seguinte:
- [Criar banco de dados do Azure para o servidor MariaDB - portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- Utilitário da linha de comando [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) instalado em um computador.
- MySQL Workbench [Download do MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat ou qualquer outra ferramenta de terceiros do MySQL para executar os comandos de despejo e de restauração.

## <a name="use-common-tools"></a>Usar ferramentas comuns
Use utilitários e ferramentas comuns, como o MySQL Workbench, o mysqldump, o Toad ou o Navicat para conectar e restaurar remotamente dados no Banco de Dados do Azure para o MariaDB. Use essas ferramentas em sua máquina cliente com uma conexão com a Internet para se conectar ao Banco de Dados do Azure para MariaDB. Use uma conexão criptografada SSL para obter melhores práticas de segurança, consulte também [Configurar conectividade SSL no Banco de Dados do Azure para o MariaDB](concepts-ssl-connection-security.md). Você não precisa mover os arquivos de despejo para nenhum local de nuvem especial ao migrar para o Banco de Dados do Azure para o MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos comuns de despejo e restauração
Você pode usar os utilitários MySQL, como mysqldump e mysqlpump, para despejar e carregar bancos de dados em um Banco de Dados do Azure para o servidor MariaDB em vários cenários comuns. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Use despejos do banco de dados quando você estiver migrando um banco de dados inteiro. Essa recomendação é válida ao mover uma grande quantidade de dados ou quando você deseja minimizar a interrupção do serviço de sites ou aplicativos ativos. 
-  Certifique-se de que todas as tabelas no banco de dados usem o mecanismo de armazenamento InnoDB ao carregar dados no Banco de Dados do Azure para o MariaDB. O Banco de Dados do Azure para MariaDB oferece suporte apenas ao mecanismo InnoDB Storage e, portanto, não oferece suporte a mecanismos de armazenamento alternativos. Se suas tabelas estiverem configuradas com outros mecanismos de armazenamento, converta-as no formato do mecanismo InnoDB antes da migração para o Banco de Dados do Azure para o MariaDB.
   Por exemplo, se você tiver um WordPress ou WebApp usando as tabelas MyISAM, primeiro converta essas tabelas migrando para o formato InnoDB antes de restaurar para o Banco de Dados do Azure para MariaDB. Use a cláusula `ENGINE=InnoDB` para definir o mecanismo usado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidade, certifique-se de que a mesma versão do MariaDB seja usada nos sistemas de origem e de destino ao despejar bancos de dados. Por exemplo, se o seu servidor MariaDB existente for a versão 10.2, você deverá migrar para o Banco de Dados do Azure para o MariaDB configurado para executar a versão 10.2. O comando `mysql_upgrade` não funciona em um Banco de Dados do Azure para o servidor MariaDB e não é suportado. Se você precisar atualizar as versões do MariaDB, primeiro descarte ou exporte seu banco de dados de versão inferior para uma versão superior do MariaDB em seu próprio ambiente. Em seguida, execute `mysql_upgrade` antes de tentar migrar para um banco de dados do Azure para MariaDB.

## <a name="performance-considerations"></a>Considerações sobre o desempenho
Para otimizar o desempenho, observe essas considerações ao despejar grandes bancos de dados:
-   Use a opção `exclude-triggers` em mysqldump ao despejar bancos de dados. Exclua os gatilhos de arquivos de despejo para evitar que os comandos de gatilho acionem durante a restauração de dados. 
-   Use a opção `single-transaction` para definir o modo de isolamento da transação para REPEATABLE READ e enviar uma instrução SQL START TRANSACTION para o servidor antes de despejar os dados. Despejar muitas tabelas em uma única transação pode fazer com que alguns armazenamentos adicionais sejam consumidos durante a restauração. A opção `single-transaction` e a opção `lock-tables` são mutuamente exclusivas, porque LOCK TABLES faz com que as transações pendentes sejam confirmadas implicitamente. Para despejar tabelas grandes, combine a opção `single-transaction` com a opção `quick`. 
-   Use a sintaxe de várias linhas `extended-insert`, que inclui várias listas VALUE. O resultado será um arquivo de despejo menor e inserções mais rápidas quando o arquivo é recarregado.
-  Use a opção `order-by-primary` em mysqldump ao despejar bancos de dados, para que os dados sejam executados na ordem de chave primária.
-   Use a opção `disable-keys` em mysqldump ao despejar dados, para desabilitar as restrições de chave estrangeira antes da carga. Desabilitar as verificações de chave estrangeira proporciona ganhos de desempenho. Habilite as restrições e verifique os dados após o carregamento para garantir a integridade referencial.
-   Use tabelas particionadas, quando apropriado.
-   Carregar dados em paralelo. Evite o excesso de paralelismo que poderá fazer com que você atinja um limite de recursos e monitore os recursos usando as métricas disponíveis no portal do Azure. 
-   Use a opção `defer-table-indexes` em mysqlpump ao despejar bancos de dados, para que a criação de índice ocorra após os dados de tabelas que são carregados.
-   Copie os arquivos de backup para um blob/armazenamento do Azure e execute a restauração de lá, o que deve ser muito mais rápido do que executar a restauração pela Internet.

## <a name="create-a-backup-file"></a>Crie um arquivo de backup
Para fazer backup de um banco de dados MariaDB existente no servidor local ou em uma máquina virtual, execute o seguinte comando usando mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os seguintes parâmetros devem ser fornecidos:
- [uname] Seu nome de usuário do banco de dados 
- [pass] A senha do banco de dados (observe que não há nenhum espaço entre -p e a senha) 
- [dbname] O nome do banco de dados 
- [backupfile.sql] O nome do arquivo para o backup do banco de dados 
- [-opt] A opção mysqldump 

Por exemplo, para fazer backup de um banco de dados denominado 'testdb' em seu servidor MariaDB com o nome de usuário 'testuser' e sem senha para um arquivo testdb_backup.sql, use o seguinte comando. O comando faz backup do banco de dados `testdb` em um arquivo chamado `testdb_backup.sql`, que contém todas as instruções SQL necessárias para recriar o banco de dados. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Para selecionar tabelas específicas em seu banco de dados para backup, liste os nomes de tabela, separados por espaços. Por exemplo, para fazer backup somente das tabelas table1 e table2 de “testdb”, siga este exemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para fazer backup de mais de um banco de dados ao mesmo tempo, use a opção --database e liste os nomes dos banco de dados separados por espaços. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para fazer backup de todos os bancos de dados no servidor ao mesmo tempo, você deve usar a opção --all-databases.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Crie um banco de dados no servidor de destino
Crie um banco de dados vazio no Banco de Dados do Azure de destino para o servidor MariaDB no qual você deseja migrar os dados. Use uma ferramenta, como o MySQL Workbench, Toad ou Navicat para criar o banco de dados. O banco de dados pode ter o mesmo nome que o banco de dados que contém os dados de despejo ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na **Visão geral** do Banco de Dados do Azure para MariaDB.

![Encontrar as informações de conexão no portal do Azure](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Adicione as informações de conexão ao MySQL Workbench.

![Cadeia de Conexão do MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaurar seu banco de dados do MariaDB
Depois de criar o banco de dados de destino, você pode usar o comando mysql ou o MySQL Workbench para restaurar os dados no banco de dados recém-criado do arquivo de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados no banco de dados recém-criado no banco de dados de Azure de destino para o servidor MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportar usando PHPMyAdmin
Para exportar, você pode usar a ferramenta phpMyAdmin que você pode já ter instalado localmente em seu ambiente. Para exportar seu banco de dados MariaDB usando o PHPMyAdmin:
1. Abra o phpMyAdmin.
2. Selecione o banco de dados. Clique no nome do banco de dados na lista à esquerda. 
3. Clique no link **Exportar**. Aparece uma nova página para exibir o despejo do banco de dados.
4. Na área de Exportação, clique no link **Selecionar Tudo** para selecionar as tabelas no banco de dados. 
5. Na área de opções do SQL, clique nas opções apropriadas. 
6. Clique na opção **Salvar como arquivo** e na opção de compactação correspondente e, em seguida, clique no botão **Ir**. Uma caixa de diálogo deve aparecer solicitando que você salve o arquivo localmente.

## <a name="import-using-phpmyadmin"></a>Importar usando PHPMyAdmin
Importar o banco de dados é semelhante à exportação. As seguintes ações ocorrem:
1. Abra o phpMyAdmin. 
2. Na página de configuração do phpMyAdmin, clique em **Adicionar** para adicionar seu banco de dados do Azure para o servidor MariaDB. Forneça os detalhes de conexão e informações de logon.
3. Crie um banco de dados com o nome adequado e selecione-o na lista à esquerda da tela. Para reconfigurar o banco de dados existente, clique no nome do banco de dados, selecione todas as caixas de seleção ao lado dos nomes da tabela e selecione **Remover** para excluir as tabelas existentes. 
4. Clique no link **SQL** para mostrar a página onde você pode digitar os comandos SQL ou fazer upload do arquivo SQL. 
5. Use o botão **procurar** para localizar o arquivo do banco de dados. 
6. Clique no botão **Ir** para exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="next-steps"></a>Próximas etapas
- [Conecte aplicativos ao Banco de Dados do Azure para MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
