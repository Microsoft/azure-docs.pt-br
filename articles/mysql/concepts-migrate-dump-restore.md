---
title: Migrar usando despejo e restauração - Banco de Dados do Azure para MySQL
description: Este artigo descreve duas maneiras comuns de fazer backup e restaurar bancos de dados no seu Banco de dados do Azure para MySQL, usando ferramentas, como mysqldump, MySQL Workbench e PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94537910"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrar seu banco de dados MySQL para o Banco de Dados do Azure para MySQL usando despejo e restauração

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Este artigo descreve duas maneiras comuns de fazer backup e restaurar bancos de dados no seu Banco de dados do Azure para MySQL
- Despejo e restauração da linha de comando (usando mysqldump)
- Despejo e restauração usando o PHPMyAdmin

Você também pode consultar o [Guia de migração de banco de dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) para obter informações detalhadas e casos de uso sobre como migrar bancos de dados para o banco de dados do Azure para MySQL. Este guia fornece diretrizes que levarão o planejamento e a execução bem-sucedidos de uma migração do MySQL para o Azure.

## <a name="before-you-begin"></a>Antes de começar
Para acompanhar este guia de instruções, você precisa do seguinte:
- [Criar Banco de Dados do Azure para servidor MySQL - portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilitário da linha de comando [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) instalado em um computador.
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou outra ferramenta MySQL de terceiros para executar comandos de despejo e restauração.

> [!TIP]
> Se você estiver procurando migrar grandes bancos de dados com tamanhos de banco de dados mais de 1 TBs, convém considerar o uso de ferramentas da Comunidade como **mydumpr/myuploader** , que dá suporte à importação e exportação paralelas. Saiba [como migrar grandes bancos de dados MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Casos de uso comuns para despejo e restauração

Os casos de uso mais comuns são:

- **Mudando de outro provedor de serviços gerenciado** -o provedor de serviços mais gerenciado pode não fornecer acesso ao arquivo de armazenamento físico por motivos de segurança para que o backup e a restauração lógicos sejam a única opção para migrar.
- **Migrando do ambiente local ou da máquina virtual** -o banco de dados do Azure para MySQL não dá suporte à restauração de backups físicos, o que faz backup e restauração lógicas como a única abordagem.
- **Movendo o armazenamento de backup de localmente redundante para armazenamento com redundância geográfica-o** banco de dados do Azure para MySQL permite configurar o armazenamento com redundância local ou com redundância geográfica para backup só é permitido durante a criação do servidor. Quando o servidor é provisionado, você não pode alterar a opção de redundância do armazenamento de backup. Para mover o armazenamento de backup do armazenamento com redundância local para o armazenamento com redundância geográfica, o dump e a restauração são a única opção. 
-  **A migração de mecanismos de armazenamento alternativos para o InnoDB** -banco de dados do Azure para MySQL dá suporte apenas ao mecanismo de armazenamento do InnoDB e, portanto, não oferece suporte a mecanismos de armazenamento alternativos. Se as tabelas foram configuradas com outros mecanismos de armazenamento, converta-as para o formato do mecanismo do InnoDB antes da migração para o Banco de dados do Azure para MySQL.

    Por exemplo, se você tiver um WordPress ou WebApp usando as tabelas MyISAM, primeiro converta essas tabelas migrando em formato de InnoDB antes de restaurar o banco de dados do Azure para MySQL. Use a cláusula `ENGINE=InnoDB` para definir o mecanismo usado ao criar uma nova tabela e, em seguida, transfira os dados para a tabela compatível antes da restauração.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - Para evitar problemas de compatibilidade, verifique se a mesma versão do MySQL é usada nos sistemas de origem e de destino ao despejar bancos de dados. Por exemplo, se seu servidor existente do MySQL tem a versão 5.7, então você deve migrar para o banco de dados do Azure para MySQL configurado para executar a versão 5.7. O comando `mysql_upgrade` não funciona em um banco de dados do Azure para o servidor MySQL e não é suportado. 
> - Se você precisar fazer upgrade das versões do MySQL, primeiro despeje ou exporte seu banco de dados da versão inferior para uma versão posterior do MySQL no seu ambiente. Em seguida, execute `mysql_upgrade`, antes de tentar migrar para um banco de dados do Azure para MySQL.

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
-   Use a opção `skip-definer` no mysqlpump para omitir as cláusulas definer e SQL SECURITY das instruções create para exibições e procedimentos armazenados.  Ao recarregar o arquivo de despejo, ele cria objetos que usam os valores DEFINER e SQL SECURITY.
-   Copie os arquivos de backup para um blob/armazenamento do Azure e execute a restauração de lá, o que deve ser muito mais rápido do que executar a restauração pela Internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Criar um banco de dados no Banco de dados de destino do Azure para o serviço MySQL
Crie um banco de dados vazio no Banco de dados de destino do Azure para servidor MySQL para o qual você deseja migrar os dados. Use uma ferramenta como o MySQL Workbench ou mysql.exe para criar o banco de dados. O banco de dados pode ter o mesmo nome que o banco de dados que contém os dados de despejo ou você pode criar um banco de dados com um nome diferente.

Para se conectar, localize as informações de conexão na página **Visão Geral** do Banco de Dados do Azure para MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="Encontrar as informações de conexão no portal do Azure":::

Adicione as informações de conexão ao MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="Cadeia de Conexão do MySQL Workbench":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Preparar o servidor do Banco de Dados do Azure para MySQL de destino para cargas de dados rápidas
Para preparar o servidor do Banco de Dados do Azure para MySQL de destino para carregamentos de dados mais rápidos, os parâmetros de servidor e a configuração a seguir precisam ser alterados.
- max_allowed_packet – defina como 1073741824 (ou seja, 1 GB) para evitar qualquer problema de excedente devido a linhas longas.
- slow_query_log – defina como OFF para desativar o log de consultas lentas. Isso eliminará a sobrecarga causada pelo log de consulta lento durante cargas de dados.
- query_store_capture_mode – defina como nenhum para desligar o Repositório de Consultas. Isso eliminará a sobrecarga causada pelas atividades de amostragem por Repositório de Consultas.
- innodb_buffer_pool_size – escale verticalmente o servidor para 32 de SKU com otimização de memória vCore do tipo de preço do portal durante a migração para aumentar o innodb_buffer_pool_size. Innodb_buffer_pool_size só pode ser aumentado escalando verticalmente a computação para o servidor do Banco de Dados do Azure para MySQL.
- innodb_io_capacity & innodb_io_capacity_max-altere para 9000 dos parâmetros do servidor no portal do Azure para melhorar a utilização de e/s para otimizar a velocidade de migração.
- innodb_write_io_threads & innodb_write_io_threads-altere para 4 dos parâmetros do servidor no portal do Azure para melhorar a velocidade da migração.
- Escalar verticalmente a camada de armazenamento – Os IOPs do Banco de Dados do Azure para MySQL aumenta progressivamente com o aumento na camada de armazenamento. Para cargas mais rápidas, aumente a camada de armazenamento para aumentar os IOPs provisionados. Lembre-se de que o armazenamento só pode ser escalado verticalmente, não para baixo.

Quando a migração for concluída, você poderá reverter os parâmetros do servidor e a configuração da camada de computação para seus valores anteriores.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Despejar e restaurar usando o utilitário mysqldump

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Criar um arquivo de backup a partir da linha de comando usando mysqldump
Para fazer backup de um banco de dados MySQL no servidor local ou em uma máquina virtual, execute o seguinte comando:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Os seguintes parâmetros devem ser fornecidos:
- [uname] Seu nome de usuário do banco de dados
- [pass] A senha do banco de dados (observe que não há nenhum espaço entre -p e a senha)
- [dbname] O nome do banco de dados
- [backupfile.sql] O nome do arquivo para o backup do banco de dados
- [-opt] A opção mysqldump

Por exemplo, para fazer backup de um banco de dados chamado “testdb” no servidor MySQL com o nome de usuário “testuser” e nenhuma senha para um arquivo testdb_backup.sql, use o comando a seguir. O comando faz backup do banco de dados `testdb` em um arquivo chamado `testdb_backup.sql`, que contém todas as instruções SQL necessárias para recriar o banco de dados. Verifique se o nome de usuário ' testuser ' tem pelo menos o privilégio SELECT para tabelas despejadas, mostrar exibição para exibições despejadas, gatilho para gatilhos despejados e tabelas de bloqueio se a opção--single-Transaction não for usada.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Agora execute mysqldump para criar o backup do `testdb` banco de dados

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurar o banco de dados MySQL usando a linha de comando ou o MySQL Workbench
Depois de criar o banco de dados de destino, você pode usar o comando mysql ou o MySQL Workbench para restaurar os dados no banco de dados recém-criado do arquivo de despejo.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Neste exemplo, restaure os dados no banco de dados recém-criado no banco de dados de destino para do Azure para o servidor MySQL.

Aqui está um exemplo de como usar este **MySQL** para **um único servidor** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Aqui está um exemplo de como usar este **MySQL** para **servidor flexível** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Despejo e restauração usando o PHPMyAdmin
Siga estas etapas para despejar e restaurar um banco de dados usando o PHPMyadmin.

> [!NOTE]
> Para um único servidor, o nome de usuário deve estar nesse formato, ' username@servername ', mas para um servidor flexível, você pode usar ' username ' se usar ' username@servername ' para o servidor flexível, a conexão falhará.

### <a name="export-with-phpmyadmin"></a>Exportar com PHPMyadmin
Para exportar, você pode usar a ferramenta phpMyAdmin que você pode já ter instalado localmente em seu ambiente. Para exportar o banco de dados MySQL usando PHPMyAdmin:
1. Abra o phpMyAdmin.
2. Selecione o banco de dados. Clique no nome do banco de dados na lista à esquerda.
3. Clique no link **Exportar**. Aparece uma nova página para exibir o despejo do banco de dados.
4. Na área de Exportação, clique no link **Selecionar Tudo** para selecionar as tabelas no banco de dados.
5. Na área de opções do SQL, clique nas opções apropriadas.
6. Clique na opção **Salvar como arquivo** e na opção de compactação correspondente e, em seguida, clique no botão **Ir**. Uma caixa de diálogo deve aparecer solicitando que você salve o arquivo localmente.

### <a name="import-using-phpmyadmin"></a>Importar usando PHPMyAdmin
Importar o banco de dados é semelhante à exportação. As seguintes ações ocorrem:
1. Abra o phpMyAdmin.
2. Na página de configuração do phpMyAdmin, clique em **Adicionar** para adicionar o Banco de dados do Azure para o servidor MySQL. Forneça os detalhes de conexão e informações de logon.
3. Crie um banco de dados com o nome adequado e selecione-o na lista à esquerda da tela. Para reconfigurar o banco de dados existente, clique no nome do banco de dados, selecione todas as caixas de seleção ao lado dos nomes da tabela e selecione **Remover** para excluir as tabelas existentes.
4. Clique no link **SQL** para mostrar a página onde você pode digitar os comandos SQL ou fazer upload do arquivo SQL.
5. Use o botão **procurar** para localizar o arquivo do banco de dados.
6. Clique no botão **Ir** para exportar o backup, execute os comandos SQL e recrie o banco de dados.

## <a name="known-issues"></a>Problemas conhecidos
Para problemas conhecidos, dicas e truques, recomendamos consultar nosso [blog techcommunity](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Próximas etapas
- [Conectar aplicativos ao Banco de Dados do Azure para MySQL](./howto-connection-string.md).
- Para obter mais informações de como migrar bancos de dados para o Banco de Dados do Azure para MySQL, confira o [Guia de Migração de Banco de Dados](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Se você estiver procurando migrar grandes bancos de dados com tamanhos de banco de dados mais de 1 TBs, convém considerar o uso de ferramentas da Comunidade como **mydumpr/myuploader** , que dá suporte à importação e exportação paralelas. Saiba [como migrar grandes bancos de dados MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
