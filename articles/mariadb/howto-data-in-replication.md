---
title: Configurar o data-in Replication-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar Replicação de Dados no banco de dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: ed1031bd45ca4f1223de9c0bce2053a393e3a2c6
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665116"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurar Replicação de Dados no banco de dados do Azure para MariaDB

Este artigo descreve como configurar o [replicação de dados](concepts-data-in-replication.md) no banco de dados do Azure para MariaDB Configurando os servidores de origem e de réplica. Este artigo pressupõe que você tenha alguma experiência anterior com servidores e bancos de dados MariaDB.

Para criar uma réplica no banco de dados do Azure para o serviço MariaDB, [replicação de dados](concepts-data-in-replication.md) sincroniza dados de um servidor de MariaDB de origem local, em VMS (máquinas virtuais) ou em serviços de banco de dados de nuvem. A Replicação de Dados é baseada na replicação nativa com base na posição do arquivo de log binário (binlog) para o MariaDB. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

Examine as [limitações e os requisitos](concepts-data-in-replication.md#limitations-and-considerations) de replicação de dados antes de executar as etapas neste artigo.

> [!NOTE]
> Se o servidor de origem for da versão 10,2 ou mais recente, recomendamos que você configure Replicação de Dados usando a [ID de transação global](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Criar um servidor MariaDB para usar como uma réplica

1. Crie um novo banco de dados do Azure para o servidor MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica em Replicação de Dados.

    Para saber mais sobre a criação do servidor, consulte [criar um banco de dados do Azure para o servidor MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Você deve criar o banco de dados do Azure para o servidor MariaDB nos tipos de preço Uso Geral ou com otimização de memória.

2. Crie contas de usuário idênticas e privilégios correspondentes.

    As contas de usuário não são replicadas do servidor de origem para o servidor de réplica. Para fornecer acesso de usuário ao servidor de réplica, você deve criar manualmente todas as contas e privilégios correspondentes no banco de dados do Azure recém-criado para o servidor MariaDB.

3. Adicione o endereço IP do servidor de origem às regras de firewall da réplica. 

   Atualizar regras de firewall usando o [Portal do Azure](howto-manage-firewall-portal.md) ou a [CLI do Azure](howto-manage-firewall-cli.md).

## <a name="configure-the-source-server"></a>Configurar o servidor de origem

As etapas a seguir preparam e configuram o servidor MariaDB hospedado localmente, em uma VM ou em um serviço de banco de dados de nuvem para Replicação de Dados. O servidor MariaDB é a origem em Replicação de Dados.

1. Examine os [requisitos do servidor primário](concepts-data-in-replication.md#requirements) antes de continuar. 

2. Verifique se o servidor de origem permite o tráfego de entrada e de saída na porta 3306 e se o servidor de origem tem um **endereço IP público**, se o DNS está acessível publicamente ou tem um FQDN (nome de domínio totalmente qualificado). 

   Teste a conectividade com o servidor de origem tentando se conectar de uma ferramenta como a linha de comando do MySQL hospedada em outro computador ou da [Azure cloud Shell](../cloud-shell/overview.md) disponível no portal do Azure.

   Se sua organização tiver políticas de segurança estritas e não permitirá que todos os endereços IP no servidor de origem habilitem a comunicação do Azure para o servidor de origem, você poderá usar o comando abaixo para determinar o endereço IP do banco de dados do Azure para o servidor MariaDB.

   1. Entre no banco de dados do Azure para MariaDB usando uma ferramenta como a linha de comando do MySQL.
   2. Execute a consulta abaixo.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Abaixo estão alguns exemplos de saída:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Saia da linha de comando do MySQL.
   4. Execute o abaixo no utilitário ping para obter o endereço IP.

      ```bash
      ping <output of step 2b>
      ```

      Por exemplo:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configure as regras de firewall do servidor de origem para incluir o endereço IP de saída da etapa anterior na porta 3306.

   > [!NOTE]
   > Esse endereço IP pode mudar devido a operações de manutenção/implantação. Esse método de conectividade é apenas para clientes que não podem ter a permissão de permitir todo endereço IP na porta 3306.

3. Ative o registro em log binário.

    Para ver se o log binário está habilitado no primário, digite o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) retornar o valor `ON` , o log binário será habilitado no servidor.

   Se `log_bin` retornar o valor `OFF` , edite o arquivo **My. cnf** para que `log_bin=ON` ative o log binário. Reinicie o servidor para fazer a alteração entrar em vigor.

4. Defina as configurações do servidor de origem.

    Replicação de Dados exige que o parâmetro `lower_case_table_names` seja consistente entre os servidores de origem e de réplica. O `lower_case_table_names` parâmetro é definido como `1` por padrão no banco de dados do Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Crie uma nova função de replicação e configure as permissões.

   Crie uma conta de usuário no servidor de origem que está configurada com privilégios de replicação. Você pode criar uma conta usando comandos SQL ou MySQL Workbench. Se você planeja replicar com SSL, você deve especificar isso ao criar a conta de usuário.

   Para saber como adicionar contas de usuário em seu servidor de origem, consulte a [documentação do MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Usando os comandos a seguir, a nova função de replicação pode acessar a origem de qualquer computador, não apenas a máquina que hospeda a própria origem. Para esse acesso, especifique **syncuser \@ '% '** no comando para criar um usuário.

   Para saber mais sobre a documentação do MariaDB, consulte [especificando nomes de conta](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replicação com SSL

       Para exigir o SSL para todas as conexões de usuário, digite o seguinte comando para criar um usuário:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não for necessário para todas as conexões, digite o seguinte comando para criar um usuário:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **Workbench do MySQL**

   Para criar a função de replicação no MySQL Workbench, no painel **Gerenciamento** , selecione **usuários e privilégios**. Em seguida, selecione **adicionar conta**.

   ![Privilégios e usuários](./media/howto-data-in-replication/users_privileges.png)

   Insira um nome de usuário no campo **nome de logon** .

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)

   Selecione o painel **funções administrativas** e, em seguida, na lista de **privilégios globais**, selecione **replicação escravo**. Selecione **aplicar** para criar a função de replicação.

   ![Replicação subordinada](./media/howto-data-in-replication/replicationslave.png)

6. Defina o servidor de origem para o modo somente leitura.

   Antes de despejar um banco de dados, o servidor deve ser colocado no modo somente leitura. Enquanto estiver no modo somente leitura, a origem não poderá processar nenhuma transação de gravação. Para ajudar a evitar o impacto nos negócios, Agende a janela somente leitura fora do horário de pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Obter o nome e o deslocamento do arquivo de log binário atual.

   Para determinar o nome e o deslocamento do arquivo de log binário atual, execute o comando [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   Os resultados devem ser semelhantes à tabela a seguir:

   ![Resultados de status do mestre](./media/howto-data-in-replication/masterstatus.png)

   Observe o nome do arquivo binário, pois ele será usado em etapas posteriores.

8. Obtenha a posição GTID (opcional, necessária para replicação com GTID).

   Execute a função [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição de GTID para o nome do arquivo binlog correspondente e o deslocamento.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>Despejar e restaurar o servidor de origem

1. Despeje todos os bancos de dados do servidor de origem.

   Use mysqldump para despejar todos os bancos de dados do servidor de origem. Não é necessário despejar a biblioteca do MySQL e a biblioteca de teste.

    Para obter mais informações, consulte [dump and Restore](howto-migrate-dump-restore.md).

2. Defina o servidor de origem para o modo de leitura/gravação.

   Depois que o banco de dados tiver sido despejado, altere o servidor MariaDB de origem de volta para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o arquivo de despejo para o novo servidor.

   Restaure o arquivo de despejo para o servidor criado no banco de dados do Azure para o serviço MariaDB. Consulte [despejar & restaurar](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor MariaDB.

   Se o arquivo de despejo for grande, carregue-o em uma VM no Azure na mesma região que o servidor de réplica. Restaure-o no banco de dados do Azure para o servidor MariaDB da VM.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Vincular os servidores de origem e de réplica para iniciar Replicação de Dados

1. Defina o servidor de origem.

   Todas as funções de replicação nos dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [Procedimentos armazenados de replicação nos dados](reference-stored-procedures.md). Os procedimentos armazenados podem ser executados no Shell do MySQL ou no MySQL Workbench.

   Para vincular dois servidores e iniciar a replicação, entre no servidor de réplica de destino no banco de BD do Azure para o serviço MariaDB. Em seguida, defina a instância externa como o servidor de origem usando `mysql.az_replication_change_master` o `mysql.az_replication_change_master_with_gtid` procedimento armazenado ou no Azure DB para MariaDB Server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   ou

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome de host do servidor de origem
   - master_user: nome de usuário para o servidor de origem
   - master_password: senha para o servidor de origem
   - master_log_file: nome de arquivo de log binário de `show master status` em execução
   - master_log_pos: posição de log binário de `show master status` em execução
   - master_gtid_pos: GTID posição da execução `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: contexto do certificado de autoridade de certificação. Se você não estiver usando SSL, passe uma cadeia de caracteres vazia. *


    * Recomendamos passar o parâmetro master_ssl_ca como uma variável. Para obter mais informações, consulte os exemplos a seguir.

   **Exemplos**

   - Replicação com SSL

       Crie a variável `@cert` executando os seguintes comandos:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com SSL é configurada entre um servidor de origem hospedado no domínio companya.com e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replicação sem SSL

       A replicação sem SSL é configurada entre um servidor de origem hospedado no domínio companya.com e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Inicie a replicação.

   Chame o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verifique o status de replicação.

   Chame o [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) comando no servidor de réplica para exibir o status de replicação.

   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` estiver no estado `yes` , e o valor de `Seconds_Behind_Master` for, a `0` replicação estará funcionando. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor não for `0` , a réplica estará processando atualizações.

4. Atualize as variáveis de servidor correspondentes para tornar a replicação de dados em segurança (necessária somente para replicação sem GTID).

    Devido a uma limitação de replicação nativa no MariaDB, você deve definir  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis na replicação sem o cenário GTID.

    Verifique o servidor de réplica `sync_master_info` e as `sync_relay_log_info` variáveis para certificar-se de que a replicação de dados está estável e defina as variáveis como `1` .

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor de origem e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remover o relacionamento de replicação

Para remover a relação entre o servidor de origem e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorar o erro de replicação

Para ignorar um erro de replicação e permitir a replicação, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Replicação de entrada de dados](concepts-data-in-replication.md) para o Banco de Dados do Azure para o MariaDB.
