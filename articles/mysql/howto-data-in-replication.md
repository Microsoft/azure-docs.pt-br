---
title: Configurar o data-in Replication-banco de dados do Azure para MySQL
description: Este artigo descreve como configurar a replicação nos dados para o Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 22974a47a6b1e9d49e5055a85f46286497cfe149
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250525"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a replicação nos dados para o Banco de Dados do Azure para MySQL

Este artigo descreve como configurar [replicação de dados](concepts-data-in-replication.md) no banco de dados do Azure para MySQL Configurando os servidores de origem e de réplica. Este artigo pressupõe que você tenha alguma experiência anterior com servidores e bancos de dados MySQL.

> [!NOTE]
> Este artigo contém referências ao termo _subordinado_, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
>

Para criar uma réplica no serviço de banco de dados do Azure para MySQL, [replicação de dados](concepts-data-in-replication.md)  sincroniza dados de um servidor MySQL de origem local, em VMS (máquinas virtuais) ou em serviços de banco de dados de nuvem. A Replicação nos dados se baseia na replicação nativa baseada na posição do arquivo de log binário (binlog) para o MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Examine as [limitações e os requisitos](concepts-data-in-replication.md#limitations-and-considerations) de replicação de dados antes de executar as etapas neste artigo.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Criar um servidor MySQL para ser usado como réplica

1. Criar um novo servidor de Banco de Dados do Azure para MySQL

   Crie um novo servidor MySQL (por exemplo, "replica.mysql.database.azure.com"). Consulte [Criar um servidor Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para verificar a criação do servidor. Esse servidor é o servidor de "réplica" da replicação nos dados.

   > [!IMPORTANT]
   > O Banco de Dados do Azure para MySQL deve ser criado nos tipos de preço Uso Geral ou Otimizado para Memória.
   >

2. Criar as mesmas contas de usuário e os privilégios correspondentes

   As contas de usuário não são replicadas do servidor de origem para o servidor de réplica. Se você planeja fornecer aos usuários acesso ao servidor de réplica, precisará criar todas as contas e privilégios correspondentes manualmente neste banco de dados do Azure recém-criado para o servidor MySQL.

3. Adicione o endereço IP do servidor de origem às regras de firewall da réplica.

   Atualizar regras de firewall usando o [Portal do Azure](howto-manage-firewall-using-portal.md) ou a [CLI do Azure](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>Configurar o servidor de origem

As etapas a seguir preparam e configuram o servidor MySQL hospedado no local, em uma máquina virtual ou serviço de banco de dados hospedado por outros provedores de nuvem para replicação de entrada nos dados. Esse servidor é a "origem" na replicação de dados.

1. Examine os [requisitos do servidor de origem](concepts-data-in-replication.md#requirements) antes de continuar.

2. Verifique se o servidor de origem permite o tráfego de entrada e de saída na porta 3306 e se ele tem um **endereço IP público**, se o DNS está acessível publicamente ou tem um FQDN (nome de domínio totalmente qualificado).

   Teste a conectividade com o servidor de origem tentando se conectar de uma ferramenta como a linha de comando do MySQL hospedada em outro computador ou da [Azure cloud Shell](../cloud-shell/overview.md) disponível no portal do Azure.

   Se sua organização tiver políticas de segurança estritas e não permitirá que todos os endereços IP no servidor de origem habilitem a comunicação do Azure para o servidor de origem, você poderá usar o comando abaixo para determinar o endereço IP do servidor MySQL.

   1. Entre no banco de dados do Azure para MySQL usando uma ferramenta como a linha de comando do MySQL.

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
   4. Execute o comando a seguir no utilitário ping para obter o endereço IP.

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
  
3. Ligar o registro em log binário

   Verifique se o log binário foi habilitado na origem executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) for retornada com o valor "on", o log binário será habilitado em seu servidor.

   Se `log_bin` for retornado com o valor "off", ative o log binário editando o arquivo My. cnf para que `log_bin=ON` o e reinicie o servidor para que a alteração entre em vigor.

4. Configurações do servidor de origem

   Replicação de Dados requer `lower_case_table_names` que o parâmetro seja consistente entre os servidores de origem e de réplica. Esse parâmetro é 1 por padrão no Banco de Dados do Azure para MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Criar uma nova função de replicação e configurar permissão

   Crie uma conta de usuário no servidor de origem que está configurada com privilégios de replicação. Isso pode ser feito por meio de comandos SQL ou de uma ferramenta como o Workbench do MySQL. Leve em conta se você planeja replicar com SSL, pois isso precisa ser especificado na criação do usuário. Consulte a documentação do MySQL para entender como [Adicionar contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) no servidor de origem.

   Nos comandos a seguir, a nova função de replicação criada pode acessar a origem de qualquer computador, não apenas a máquina que hospeda a própria origem. Isso é feito especificando "syncuser@'%'" no comando create user. Confira a documentação do MySQL para saber mais sobre [como especificar nomes de conta](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Comando SQL**

   *Replicação com SSL*

   Para exigir SSL em todas as conexões de usuário, use o seguinte comando para criar um usuário:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicação sem SSL*

   Se o SSL não for necessário para todas as conexões, use o seguinte comando para criar um usuário:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **Workbench do MySQL**

   Para criar a função de replicação no MySQL Workbench, abra o painel **usuários e privilégios** no painel de **Gerenciamento** e selecione **adicionar conta**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Privilégios e usuários":::

   Digite o nome de usuário no campo **Nome de Logon**.

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Sincronizar usuário":::

   Selecione o painel **funções administrativas** e, em seguida, selecione **escravo de replicação** na lista de **privilégios globais**. Em seguida, selecione **aplicar** para criar a função de replicação.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replicação subordinada":::

6. Definir o servidor de origem para o modo somente leitura

   Antes de começar a despejar o banco de dados, o servidor precisa ser colocado no modo somente leitura. Enquanto estiver no modo somente leitura, a origem não poderá processar nenhuma transação de gravação. Avalie o impacto em seus negócios e agende a janela de somente leitura em um horário fora de pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Obter o nome e o deslocamento do arquivo de log binário

   Execute o [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) comando para determinar o deslocamento e o nome do arquivo de log binário atual.

   ```sql
    show master status;
   ```

   Os resultados devem ser semelhantes ao seguinte. Certifique-se de anotar o nome do arquivo binário, pois ele será usado em etapas posteriores.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Resultados de status do mestre":::

## <a name="dump-and-restore-source-server"></a>Despejar e restaurar o servidor de origem

1. Determine os bancos de dados e as tabelas que você deseja replicar no Azure Database para MySQL e execute o despejo do servidor de origem.

    Você pode usar o mysqldump para despejar bancos de dados do seu mestre. Para obter detalhes, consulte [Despejar e restaurar](concepts-migrate-dump-restore.md). É desnecessário despejar biblioteca MySQL e biblioteca de teste.

2. Defina o servidor de origem como modo de leitura/gravação.

   Depois que o banco de dados tiver sido despejado, altere o servidor MySQL de origem de volta para o modo de leitura/gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaure o arquivo de despejo para o novo servidor.

   Restaure o arquivo de despejo no servidor criado no serviço Banco de Dados do Azure para MySQL. Consulte [Despejar e restaurar](concepts-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo em um servidor MySQL. Se o arquivo de despejo é grande, carregue-o em uma máquina virtual do Azure na mesma região do servidor de réplica. Restaure-o no serviço Banco de Dados do Azure para MySQL da máquina virtual.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Vincular os servidores de origem e de réplica para iniciar Replicação de Dados

1. Definir servidor de origem.

   Todas as funções de replicação nos dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [Procedimentos armazenados de replicação nos dados](./reference-stored-procedures.md). Os procedimentos armazenados podem ser executados no shell do MySQL ou no Workbench do MySQL.

   Para vincular dois servidores e iniciar a replicação, faça logon no servidor de réplica de destino no serviço BD do Azure para MySQL e defina a instância externa como o servidor de origem. Isso é feito usando o procedimento armazenado `mysql.az_replication_change_master` no servidor do Banco de Dados do Azure para MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome de host do servidor de origem
   - master_user: nome de usuário para o servidor de origem
   - master_password: senha para o servidor de origem
   - master_log_file: nome de arquivo de log binário de `show master status` em execução
   - master_log_pos: posição de log binário de `show master status` em execução
   - master_ssl_ca: contexto do certificado de autoridade de certificação. Se não estiver usando SSL, passe em uma cadeia de caracteres vazia.

     É recomendável passar esse parâmetro em como uma variável. Para obter mais informações, consulte os exemplos a seguir.

   > [!NOTE]
   > Se o servidor de origem estiver hospedado em uma VM do Azure, defina "permitir acesso aos serviços do Azure" como "ativado" para permitir que os servidores de origem e de réplica se comuniquem entre si. Essa configuração pode ser alterada a partir das opções **Segurança de conexão**. Para obter mais informações, consulte [gerenciar regras de firewall usando o portal](howto-manage-firewall-using-portal.md) .

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos do MySQL:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   A replicação com SSL é configurada entre um servidor de origem hospedado no domínio "companya.com" e um servidor de réplica hospedado no banco de dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replicação sem SSL*

   A replicação sem SSL é configurada entre um servidor de origem hospedado no domínio "companya.com" e um servidor de réplica hospedado no banco de dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Aplica.

   Se você quiser ignorar a replicação de algumas tabelas do seu mestre, atualize o `replicate_wild_ignore_table` parâmetro de servidor no servidor de réplica. Você pode fornecer mais de um padrão de tabela usando uma lista separada por vírgulas.

   Consulte a [documentação do MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) para saber mais sobre esse parâmetro.

   Para atualizar o parâmetro, você pode usar o [portal do Azure](howto-server-parameters.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md).

3. Inicie a replicação.

   Chame o `mysql.az_replication_start` procedimento armazenado para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Verifique o status de replicação.

   Chame o [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando no servidor de réplica para exibir o status de replicação.

   ```sql
   show slave status;
   ```

   Se o estado de `Slave_IO_Running` e for `Slave_SQL_Running` "Sim" e o valor de `Seconds_Behind_Master` for "0", a replicação estará funcionando bem. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor não for "0", significa que a réplica está processando atualizações.

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor de origem e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover relação de replicação

Para remover a relação entre o servidor de origem e de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorar erro de replicação

Para ignorar um erro de replicação e permitir que a replicação Continue, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Replicação no Dados](concepts-data-in-replication.md) parra Banco de Dados do Azure para MySQL.
