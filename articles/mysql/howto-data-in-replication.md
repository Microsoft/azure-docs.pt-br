---
title: Configurar a replicação nos dados para replicar dados no Banco de Dados do Azure para MySQL.
description: Este artigo descreve como configurar a replicação nos dados para o Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: 7d56d7f8fcbd53d4f69863d260591ef80f3d7188
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459025"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Como configurar a replicação nos dados para o Banco de Dados do Azure para MySQL

Neste artigo, você aprenderá a configurar a Replicação de Entrada de Dados no serviço Banco de Dados do Azure para MySQL, configurando os servidores mestre e de réplica. A Replicação de Entrada de Dados permite sincronizar dados de um servidor MySQL mestre em execução no local, em máquinas virtuais ou serviços de banco de dados hospedados por outros provedores de nuvem em uma réplica no serviço Banco de Dados do Azure para MySQL. 

Este artigo pressupõe que você tem pelo menos alguma experiência anterior com servidores e bancos de dados MySQL.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Criar um servidor MySQL para ser usado como réplica

1. Criar um novo servidor de Banco de Dados do Azure para MySQL

   Crie um novo servidor MySQL (por exemplo, "replica.mysql.database.azure.com"). Consulte [Criar um servidor Banco de Dados do Azure para MySQL usando o portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md) para verificar a criação do servidor. Esse servidor é o servidor de "réplica" da replicação nos dados.

   > [!IMPORTANT]
   > O Banco de Dados do Azure para MySQL deve ser criado nos tipos de preço Uso Geral ou Otimizado para Memória.
   > 

2. Criar as mesmas contas de usuário e os privilégios correspondentes

   Contas de usuário não são replicadas do servidor mestre para o servidor de réplica. Se você planeja fornecer aos usuários acesso ao servidor de réplica, precisa criar manualmente todas as contas e privilégios correspondentes no servidor do Banco de Dados do Azure para MySQL recém-criado.

## <a name="configure-the-master-server"></a>Configurar o servidor mestre
As etapas a seguir preparam e configuram o servidor MySQL hospedado no local, em uma máquina virtual ou serviço de banco de dados hospedado por outros provedores de nuvem para replicação de entrada nos dados. Esse servidor é o "mestre" em replicação de dados. 

1. Ligar o registro em log binário

   Verifique se o log binário foi ativado no mestre executando o seguinte comando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Se a variável [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) é retornada com o valor "ON", o registro em log binário está habilitado no servidor. 

   Se `log_bin` é retornado com o valor "OFF", ligue o registro em log binário editando o arquivo my.cnf para que `log_bin=ON` e reinicie o servidor para que a alteração tenha efeito.

2. Configurações do servidor mestre

   A replicação de entrada de dados exige que o parâmetro `lower_case_table_names` seja consistente entre os servidores mestre e de réplica. Esse parâmetro é 1 por padrão no Banco de Dados do Azure para MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Criar uma nova função de replicação e configurar permissão

   Crie uma conta de usuário no servidor mestre que é configurado com privilégios de replicação. Isso pode ser feito por meio de comandos SQL ou de uma ferramenta como o Workbench do MySQL. Leve em conta se você planeja replicar com SSL, pois isso precisa ser especificado na criação do usuário. Consulte a documentação do MySQL para entender como [adicionar contas de usuário](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) ao seu servidor mestre. 

   Nos comandos abaixo, a nova função de replicação criada é capaz de acessar o mestre de qualquer máquina, não apenas da máquina que hospeda o próprio mestre. Isso é feito especificando "syncuser@'%'" no comando create user. Confira a documentação do MySQL para saber mais sobre [como especificar nomes de conta](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **Comando SQL**

   *Replicação com SSL*

   Para exigir SSL em todas as conexões de usuário, use o seguinte comando para criar um usuário: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicação sem SSL*

   Se o SSL não é exigido para todas as conexões, use o seguinte comando para criar um usuário:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **Workbench do MySQL**

   Para criar a função de replicação no Workbench do MySQL, abra o painel **Usuários e Privilégios** do painel **Gerenciamento**. Em seguida, clique em **Adicionar Conta**. 
 
   ![Privilégios e usuários](./media/howto-data-in-replication/users_privileges.png)

   Digite o nome de usuário no campo **Nome de Logon**. 

   ![Sincronizar usuário](./media/howto-data-in-replication/syncuser.png)
 
   Clique no painel **Funções Administrativas** painel e selecione **Replicação Subordinada** na lista de **Privilégios Globais**. Em seguida, clique em **Aplicar** para criar a função de replicação.

   ![Replicação subordinada](./media/howto-data-in-replication/replicationslave.png)


4. Configure o servidor mestre para o modo somente leitura

   Antes de começar a despejar o banco de dados, o servidor precisa ser colocado no modo somente leitura. No modo somente leitura, o mestre será capaz de processar todas as transações de gravação. Avalie o impacto em seus negócios e agende a janela de somente leitura em um horário fora de pico, se necessário.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Obter o nome e o deslocamento do arquivo de log binário

   Execute o comando [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) para determinar o nome e o deslocamento do arquivo de log binário atual.
    
   ```sql
   show master status;
   ```
   Os resultados devem ser assim. Anote o nome de arquivo binário, pois ele será usado em etapas posteriores.

   ![Resultados de status do mestre](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Despejar e restaurar o servidor principal

1. Descarregar todos os bancos de dados do servidor mestre

   Você pode usar o mysqldump para despejar bancos de dados do seu mestre. Para obter detalhes, consulte [Despejar e restaurar](concepts-migrate-dump-restore.md). Não é necessário despejar as bibliotecas normal e de teste do MySQL.

2. Definir o servidor mestre para o modo de leitura/gravação

   Uma vez que o banco de dados foi descartado, mude o servidor MySQL de volta para o modo de leitura / gravação.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Restaurar arquivo de despejo no novo servidor

   Restaure o arquivo de despejo no servidor criado no serviço Banco de Dados do Azure para MySQL. Consulte [Despejar e restaurar](concepts-migrate-dump-restore.md) para saber como restaurar um arquivo de despejo em um servidor MySQL. Se o arquivo de despejo é grande, carregue-o em uma máquina virtual do Azure na mesma região do servidor de réplica. Restaure-o no serviço Banco de Dados do Azure para MySQL da máquina virtual.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Vincular servidores mestre e de réplica para iniciar a replicação de dados

1. Definir o servidor mestre

   Todas as funções de replicação nos dados são feitas por procedimentos armazenados. Você pode encontrar todos os procedimentos em [Procedimentos armazenados de replicação nos dados](reference-data-in-stored-procedures.md). Os procedimentos armazenados podem ser executados no shell do MySQL ou no Workbench do MySQL. 

   Para vincular dois servidores e iniciar a replicação, faça logon no servidor de réplica de destino no BD do Azure para o serviço MySQL e defina a instância externa como o servidor mestre. Isso é feito usando o procedimento armazenado `mysql.az_replication_change_master` no servidor do Banco de Dados do Azure para MySQL.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: nome do host do servidor mestre
   - master_user: nome de usuário para o servidor mestre
   - master_password: a senha para o servidor mestre
   - master_log_file: nome de arquivo de log binário de `show master status` em execução
   - master_log_pos: posição de log binário de `show master status` em execução
   - master_ssl_ca: Contexto do Certificado de Autoridade de Certificação. Se não estiver usando SSL, passe em uma cadeia de caracteres vazia.
       - É recomendável passar esse parâmetro como uma variável. Confira os exemplos a seguir para obter mais informações.

> [!NOTE]
> Se o servidor mestre estiver hospedado em uma VM do Azure, defina "Permitir acesso a serviços do Azure" como "ATIVADO" para permitir que os servidores mestres e de réplica comuniquem-se entre si. Essa configuração pode ser alterada a partir das opções **Segurança de conexão**. Consulte [gerenciar regras de firewall usando o portal](howto-manage-firewall-using-portal.md) para obter mais informações.

   **Exemplos**

   *Replicação com SSL*

   A variável `@cert` é criada executando os seguintes comandos do MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   A replicação com SSL é configurada entre um servidor mestre hospedado no domínio “companya.com” e um servidor de réplica hospedado no Banco de Dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replicação sem SSL*

   A replicação com SSL é configurada entre um servidor mestre hospedado no domínio “companya.com” e um servidor de réplica hospedado no Banco de Dados do Azure para MySQL. Esse procedimento armazenado é executado na réplica.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Iniciar replicação

   Chame o procedimento armazenado `mysql.az_replication_start` para iniciar a replicação.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Verificar status de replicação

   Chame o comando [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) no servidor de réplica para exibir o status de replicação.
    
   ```sql
   show slave status;
   ```

   Se os estados de `Slave_IO_Running` e `Slave_SQL_Running` são "yes" e o valor de `Seconds_Behind_Master` é "0", a replicação está funcionando bem. `Seconds_Behind_Master` indica o atraso da réplica. Se o valor não é "0", isso significa que a réplica está processando as atualizações. 

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

### <a name="stop-replication"></a>Parar replicação

Para interromper a replicação entre o servidor primário e o de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Remover relação de replicação

Para remover o relacionamento entre o servidor mestre e o servidor de réplica, use o seguinte procedimento armazenado:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Ignorar erro de replicação

Para ignorar um erro de replicação e permitir que a replicação continue, use o seguinte procedimento armazenado:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Replicação no Dados](concepts-data-in-replication.md) parra Banco de Dados do Azure para MySQL. 
