---
title: Configurar a replicação de dados no banco de dados do Azure para MariaDB | Microsoft Docs
description: Este artigo descreve como configurar dados de replicação no banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444801"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Configurar a replicação de dados no banco de dados do Azure para MariaDB

Este artigo descreve como definir dados de replicação no banco de dados do Azure para MariaDB, configure os servidores mestre e de réplica. Este artigo pressupõe que você tenha alguma experiência anterior com MariaDB servidores e bancos de dados.

Para criar uma réplica no banco de dados do Azure para o serviço do MariaDB, replicação de dados sincroniza os dados de um mestre MariaDB server no local, em máquinas virtuais (VMs) ou em serviços de nuvem do banco de dados.

> [!NOTE]
> Se seu servidor mestre é a versão 10.2 ou mais recente, é recomendável que você defina a duplicação de dados usando [ID de transação Global](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Criar um servidor do MariaDB para usar como uma réplica

1. Crie um novo banco de dados do Azure para MariaDB (por exemplo, replica.mariadb.database.azure.com). O servidor é o servidor de réplica em replicação de dados.

    Para saber mais sobre a criação do servidor, consulte [criar um banco de dados do Azure para MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Você deve criar o banco de dados do Azure para MariaDB nas camadas de preço de uso geral ou com otimização de memória.

2. Crie contas de usuário idênticas e privilégios correspondentes.
    
    Contas de usuário não são replicadas do servidor mestre para o servidor de réplica. Para fornecer acesso de usuário para o servidor de réplica, você deve criar manualmente todas as contas e privilégios correspondentes no banco de dados do Azure criado recentemente para o servidor MariaDB.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre

As etapas a seguir preparar e configurar o MariaDB server hospedado no local, em uma VM ou em um serviço de banco de dados de nuvem para replicação de dados. O servidor MariaDB é o mestre em replicação de dados.

1. Ative o log binário.
    
    Para ver se o log binário está habilitado no mestre, digite o seguinte comando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) retorna o valor `ON`, log binário está habilitado no seu servidor.

   Se `log_bin` retorna o valor `OFF`, edite o **cnf** arquivo, de modo que `log_bin=ON` ativará o log binário. Reinicie o servidor para que as alterações entrem em vigor.

2. Configurações do servidor mestre.

    Replicação de dados requer que o parâmetro `lower_case_table_names` para ser consistente entre os servidores mestre e de réplica. O `lower_case_table_names` parâmetro é definido como `1` por padrão no banco de dados do Azure para MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar as permissões.

   Crie uma conta de usuário no servidor mestre que é configurado com privilégios de replicação. Você pode criar uma conta usando comandos SQL ou MySQL Workbench. Se você planeja replicar com SSL, você deverá especificar isso quando você cria a conta de usuário.
   
   Para saber como adicionar contas de usuário em seu servidor mestre, consulte o [MariaDB documentação](https://mariadb.com/kb/en/library/create-user/).

   Usando os comandos a seguir, a nova função de replicação pode acessar o mestre de qualquer computador, não apenas a máquina que hospeda o mestre em si. Esse acesso, especifique **syncuser\@'% s'** no comando para criar um usuário.
   
   Para saber mais sobre a documentação do MariaDB, consulte [especificando nomes de conta](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Comando SQL**

   - Replicação com SSL

       Para exigir SSL para todas as conexões de usuário, digite o seguinte comando para criar um usuário:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicação sem SSL

       Se o SSL não é necessária para todas as conexões, digite o seguinte comando para criar um usuário:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **Workbench do MySQL**

   Para criar a função de replicação no MySQL Workbench, nos **Management** painel, selecione **usuários e privilégios**. Em seguida, selecione **adicionar conta**.
 
   ![Privilégios e usuários](./media/howto-data-in-replication/users_privileges.png)

   Insira um nome de usuário na **nome de logon** campo.

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)
 
   Selecione o **funções administrativas** painel e, em seguida, na lista de **privilégios Global**, selecione **Replication Slave**. Selecione **aplicar** para criar a função de replicação.

   ![Replicação subordinada](./media/howto-data-in-replication/replicationslave.png)


4. Configure o servidor mestre para o modo somente leitura.

   Antes de você despejar um banco de dados, o servidor deve ser colocado no modo somente leitura. No modo somente leitura, o mestre não pode processar todas as transações de gravação. Para ajudar a evitar o impacto nos negócios, agendar a janela de somente leitura durante um horário de pico.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obtenha o nome do arquivo de log binário atual e o deslocamento.

   Para determinar o nome do arquivo de log binário atual e o deslocamento, execute o comando [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Os resultados devem ser semelhantes a tabela a seguir:
   
   ![Resultados de status do mestre](./media/howto-data-in-replication/masterstatus.png)

   Anote o nome de arquivo binário, porque será usado em etapas posteriores.
   
6. Obtenha a posição de GTID (opcional, necessários para a replicação com GTID).

   Executar a função [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) para obter a posição de GTID para o nome do arquivo binlog correspondente e o deslocamento.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Despejar e restaurar o servidor mestre

1. Despeje todos os bancos de dados do servidor mestre.

   Use mysqldump ao despejar todos os bancos de dados do servidor mestre. Não é necessário para a biblioteca de MySQL de despejo e biblioteca de teste.

    Para obter mais informações, consulte [Dump e restore](howto-migrate-dump-restore.md).

2. Configure o servidor mestre para o modo de leitura/gravação.

   Depois que o banco de dados foram colocado, alterar o mestre MariaDB volta de servidor para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o arquivo de despejo para o novo servidor.

   Restaure o arquivo de despejo para o servidor criado no banco de dados do Azure para o serviço MariaDB. Ver [despejar e restaurar](howto-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo para um servidor do MariaDB.

   Se o arquivo de despejo é grande, carregue-a uma VM no Azure na mesma região que o servidor de réplica. Restaure o banco de dados do Azure para servidor MariaDB a VM.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Vincule os servidores mestre e de réplica para iniciar a replicação de dados

1. Configure o servidor mestre.

   Todas as funções de replicação nos dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [Procedimentos armazenados de replicação nos dados](reference-data-in-stored-procedures.md). Procedimentos armazenados podem ser executados no shell do MySQL ou o MySQL Workbench.

   Para vincular os dois servidores e iniciar a replicação, entrar no servidor de réplica de destino no BD do Azure para o serviço do MariaDB. Em seguida, defina a instância externa como o servidor mestre usando o `mysql.az_replication_change_master` ou `mysql.az_replication_change_master_with_gtid` procedimento armazenado no BD do Azure para servidor MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   ou o
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: nome do host do servidor mestre
   - master_user: nome de usuário para o servidor mestre
   - master_password: a senha para o servidor mestre
   - master_log_file: nome de arquivo de log binário de `show master status` em execução
   - master_log_pos: posição de log binário de `show master status` em execução
   - master_gtid_pos: Posição GTID execução `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Contexto do Certificado de Autoridade de Certificação. Se você não estiver usando SSL, passe um string.* vazio
    
    
    \* É recomendável passar no parâmetro master_ssl_ca como uma variável. Para obter mais informações, confira os exemplos a seguir.

   **Exemplos**

   - Replicação com SSL

       Crie a variável `@cert` executando os comandos a seguir:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       A replicação com o SSL é configurada entre um servidor mestre hospedado no companya.com domínio e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replicação sem SSL

       Configurar a replicação sem SSL entre um servidor mestre hospedado no companya.com domínio e um servidor de réplica hospedado no banco de dados do Azure para MariaDB. Esse procedimento armazenado é executado na réplica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Inicie a replicação.

   Chamar o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Verificar o status de replicação.

   Chame o comando [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) no servidor de réplica para exibir o status de replicação.
    
   ```sql
   show slave status;
   ```

   Se `Slave_IO_Running` e `Slave_SQL_Running` estão no estado `yes`e o valor de `Seconds_Behind_Master` é `0`, a replicação está funcionando. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor não for `0`, em seguida, a réplica está processando as atualizações.

4. Atualize as variáveis de servidor correspondente para tornar os dados de replicação mais segura (necessária somente para replicação sem GTID).
    
    Devido a uma limitação de replicação nativa no MariaDB, você deve definir [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) e [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variáveis em replicação sem o cenário GTID.

    Verificação do seu servidor subordinado `sync_master_info` e `sync_relay_log_info` variáveis para se certificar de que a replicação de dados estáveis e defina as variáveis para `1`.
    
## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor primário e o de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Remova a relação de replicação

Para remover a relação entre o servidor mestre e de réplica, use o procedimento armazenado a seguir:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Ignorar o erro de replicação

Para ignorar um erro de replicação e permitir a replicação, use o procedimento armazenado a seguir:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Replicação de entrada de dados](concepts-data-in-replication.md) para o Banco de Dados do Azure para o MariaDB.
