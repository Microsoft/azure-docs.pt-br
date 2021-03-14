---
title: Fazer backup e recuperar um banco de dados Oracle Database 19C em uma VM Linux do Azure usando o backup do Azure
description: Saiba como fazer backup e recuperar um banco de dados Oracle Database 19C usando o serviço de backup do Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673893"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Fazer backup e recuperar um banco de dados Oracle Database 19C em uma VM Linux do Azure usando o backup do Azure

Este artigo demonstra o uso do backup do Azure para tirar instantâneos de disco dos discos de VM, que incluem os arquivos de banco de dados e a área de recuperação rápida. Usando o backup do Azure, você pode tirar instantâneos de disco completos adequados como backups, que são armazenados no [cofre dos serviços de recuperação](../../../backup/backup-azure-recovery-services-vault-overview.md).  O backup do Azure também fornece backups consistentes com o aplicativo, o que garante que as correções adicionais não sejam necessárias para restaurar os dados. Restaurar dados consistentes com aplicativos reduz o tempo de restauração, permitindo que você rapidamente retorne ao estado de execução.

> [!div class="checklist"]
>
> * Fazer backup do banco de dados com backup consistente com o aplicativo
> * Restaurar e recuperar o banco de dados de um ponto de recuperação
> * Restaurar a VM de um ponto de recuperação

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Para executar o processo de backup e recuperação, você deve primeiro criar uma VM do Linux que tenha uma instância instalada do Oracle Database 19C. A imagem do Marketplace usada atualmente para criar a VM é  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Latest**. Siga as etapas no guia de [início rápido do Oracle CREATE DATABASE](./oracle-database-quick-create.md) para criar um banco de dados Oracle para concluir este tutorial.


## <a name="prepare-the-environment"></a>Preparar o ambiente

Para preparar o ambiente, conclua estas etapas:

1. Conecte-se à VM.
1. Prepare o banco de dados.

### <a name="connect-to-the-vm"></a>Conectar-se à VM

1. Para criar uma sessão SSH (Secure Shell) com a VM, use o comando a seguir. Substitua a combinação de endereço IP e nome do host pelo valor `<publicIpAddress>` para sua VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Alternar para o usuário *raiz* :

   ```bash
   sudo su -
   ```
    
1. Adicione o usuário Oracle ao arquivo */etc/sudoers* :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Preparar o banco de dados

Esta etapa pressupõe que você tenha uma instância do Oracle (*teste*) em execução em uma VM chamada *vmoracle19c*.

1. Alternar usuário para o usuário *Oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
2. Antes de se conectar, você precisa definir a variável de ambiente ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Você também deve adicionar a variável ORACLE_SID ao arquivo `.bashrc` dos usuários `oracle` para entradas futuras usando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Inicie o ouvinte Oracle se ele ainda não estiver em execução:

    ```output
    $ lsnrctl start
    ```

    O resultado deverá ser semelhante ao seguinte exemplo:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  Crie o local da FRA (área de recuperação rápida):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Conecte-se ao banco de dados:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Inicie o banco de dados se ele ainda não estiver em execução:

    ```bash
    SQL> startup
    ```

7.  Definir variáveis de ambiente de banco de dados para a área de recuperação rápida:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Verifique se o banco de dados está no modo de log de arquivo morto para habilitar backups online.

    Verifique o status do arquivo de log primeiro:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Se ele estiver no modo NOARCHIVELOG, execute os seguintes comandos:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Crie uma tabela para testar as operações de backup e restauração:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Configure o RMAN para fazer backup para a área de recuperação rápida localizada no disco da VM:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Confirme os detalhes da alteração de configuração:

    ```bash
    RMAN> show all;
    ```    

12.  Agora, execute o backup. O comando a seguir usará um backup de banco de dados completo, incluindo arquivos de log de arquivo, como um conjunto de backup no formato compactado:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Usando o backup do Azure (versão prévia)

O serviço de Backup do Azure fornece soluções simples, seguras e econômicas para fazer backup de seus dados e recuperá-los da nuvem do Microsoft Azure. O Backup do Azure fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. Os backups são armazenados em um cofre dos Serviços de Recuperação com gerenciamento interno de pontos de recuperação. A configuração e a escalabilidade são simples, os backups são otimizados e você pode fazer uma restauração com facilidade, conforme necessário.

O serviço de backup do Azure fornece uma [estrutura](../../../backup/backup-azure-linux-app-consistent.md) para alcançar a consistência do aplicativo durante backups de VMs do Windows e do Linux para vários aplicativos, como Oracle, MySQL, Mongo DB e PostgreSQL. Isso envolve invocar um pré-script (para desativar os aplicativos) antes de tirar um instantâneo dos discos e chamar o post-script (comandos para descongelar os aplicativos) depois que o instantâneo for concluído, para retornar os aplicativos para o modo normal. Embora os pré-scripts de exemplo e os pós-scripts sejam fornecidos no GitHub, a criação e a manutenção desses scripts são de sua responsabilidade.

Agora o backup do Azure está fornecendo uma estrutura de pré-scripts aprimorada e pós-script (**que está atualmente em versão prévia**), em que o serviço de backup do Azure fornecerá pré-scripts e pós-scripts empacotados para os aplicativos selecionados. Os usuários do backup do Azure precisam apenas nomear o aplicativo e, em seguida, o backup da VM do Azure invocará automaticamente os scripts de pré-lançamento relevantes. Os pré-scripts e pós-scripts empacotados serão mantidos pela equipe de backup do Azure e, portanto, os usuários poderão ter a garantia do suporte, da propriedade e da validade desses scripts. Atualmente, os aplicativos com suporte para a estrutura avançada são *Oracle* e *MySQL*.

Nesta seção, você usará a estrutura aprimorada do backup do Azure para obter instantâneos consistentes com o aplicativo da VM em execução e do banco de dados Oracle. O banco de dados será colocado no modo de backup, permitindo que um backup online transacionalmente consistente ocorra enquanto o backup do Azure tira um instantâneo dos discos de VM. O instantâneo será uma cópia completa do armazenamento e não um instantâneo de gravação incremental ou cópia, portanto, é um meio efetivo para restaurar seu banco de dados do. A vantagem de usar instantâneos consistentes com o aplicativo de backup do Azure é que eles são extremamente rápidos, não importando o tamanho do banco de dados, e um instantâneo pode ser usado para operações de restauração assim que for feito, sem a necessidade de esperar que ela seja transferida para o cofre dos serviços de recuperação.

Para usar o backup do Azure para fazer backup do banco de dados, conclua estas etapas:

1. Prepare o ambiente para um backup consistente com o aplicativo.
1. Configurar backups consistentes com o aplicativo.
1. Disparar um backup consistente com o aplicativo da VM.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Preparar o ambiente para um backup consistente com o aplicativo

1. Alternar para o usuário *raiz* :

   ```bash
   sudo su -
   ```

1. Criar novo usuário de backup:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Configurar o ambiente de usuário de backup:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Configure a autenticação externa para o novo usuário de backup. O usuário de backup precisa ser capaz de acessar o banco de dados usando a autenticação externa, para que não seja desafiado por uma senha.

   Primeiro, volte para o usuário *Oracle* :

   ```bash
   su - oracle
   ```

   Faça logon no banco de dados usando sqlplus e verifique as configurações padrão para autenticação externa:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   A saída deve ser semelhante a este exemplo: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Agora, crie um usuário de banco de dados *azbackup* autenticado externamente e conceda o privilégio sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Se você receber `ORA-46953: The password file is not in the 12.2 format.`  um erro ao executar a `GRANT` instrução, siga estas etapas para migrar o arquivo orapwd para o formato 12,2:
   >
   > 1. Saia do sqlplus.
   > 1. Mova o arquivo de senha com o formato antigo para um novo nome.
   > 1. Migre o arquivo de senha.
   > 1. Remova o arquivo antigo.
   > 1. Execute o comando a seguir:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Execute novamente a `GRANT` operação em sqlplus.
   >
   
4. Crie um procedimento armazenado para registrar mensagens de backup no log de alertas do banco de dados:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Configurar backups consistentes com o aplicativo  

1. Alternar para o usuário *raiz* :

   ```bash
   sudo su -
   ```

2. Verifique a pasta "etc/Azure". Se isso não estiver presente, crie o diretório de trabalho de backup consistente com o aplicativo:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Verifique se há "Workload. conf" na pasta. Se isso não estiver presente, crie um arquivo no diretório */etc/Azure* chamado *Workload. conf* com o conteúdo a seguir, que deve começar com `[workload]` . Se o arquivo já estiver presente, basta editar os campos de modo que ele corresponda ao conteúdo a seguir. Caso contrário, o comando a seguir criará o arquivo e preencherá o conteúdo:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Disparar um backup consistente com o aplicativo da VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Na portal do Azure, vá para o grupo de recursos **RG-Oracle** e clique em sua máquina virtual **vmoracle19c**.

2.  Na folha **backup** , crie um novo **cofre dos serviços de recuperação** no grupo de recursos **RG-Oracle** com o nome **myvault**.
    Para **escolher política de backup**, use **(New) política diária**. Se você quiser alterar a frequência de backup ou o período de retenção, selecione **criar uma nova política** em vez disso.

    ![Página para adicionar cofres aos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Para continuar, clique em **habilitar backup**.

    > [!IMPORTANT]
    > Depois de clicar em **Habilitar backup**, o processo de backup não iniciará até horário agendado expirar. Para configurar um backup imediato, conclua a próxima etapa.

4. Na página grupo de recursos, clique no cofre de serviços de recuperação recém-criado **myvault**. Dica: Talvez seja necessário atualizar a página para vê-la.

5.  Na folha **myVault – Itens de backup**, em **CONTAGEM DE ITENS DE BACKUP**, selecione a contagem de itens de backup.

    ![Página de detalhes myVault de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-02.png)

6.  Na folha **Itens de Backup (Máquina Virtual do Azure)**, do lado direito da página, clique no botão de reticências (**...**) e clique em **Fazer backup agora**.

    ![Comando Fazer backup agora dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Aceite o valor padrão reter backup até e clique no botão **OK** . Aguarde o processo de backup ser concluído. 

    Para exibir o status do trabalho de backup, clique em **trabalhos de backup**.

    ![Página de trabalho dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-04.png)

    O status do trabalho de backup é exibido na imagem a seguir:

    ![Página de trabalho dos cofres dos Serviços de Recuperação com status](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Observe que, embora apenas alguns segundos executem o instantâneo, pode levar algum tempo para transferi-lo para o cofre, e o trabalho de backup não será concluído até que a transferência seja concluída.

8. Para um backup consistente com aplicativo, resolva os possíveis erros no arquivo de log. O arquivo de log está localizado em/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Criar um cofre dos serviços de recuperação:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Habilite a proteção de backup para a VM:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Dispare um backup para executar agora em vez de esperar que o backup seja disparado no agendamento padrão (5 AM UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Você pode monitorar o progresso do trabalho de backup usando o `az backup job list` e o `az backup job show` .

---

## <a name="recovery"></a>Recuperação

Para recuperar o banco de dados, conclua estas etapas:

1. Remova os arquivos de banco de dados.
1. Gerar um script de restauração do cofre dos serviços de recuperação.
1. Monte o ponto de restauração.
1. Execute a recuperação.

### <a name="remove-the-database-files"></a>Remover os arquivos de banco de dados 

Posteriormente neste artigo, você aprenderá a testar o processo de recuperação. Antes de testar o processo de recuperação, você precisará remover os arquivos de banco de dados.

1.  Desligue a instância do Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Remova os arquivos e os backups:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Gerar um script de restauração do cofre dos serviços de recuperação

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na portal do Azure, procure o item cofres dos serviços de recuperação *myvault* e selecione-o.

    ![Itens do backup myVault dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-06.png)

2. Na folha **visão geral** , selecione **itens de backup** e selecione a **máquina virtual do Azure**, que deve ter a contagem de itens de backup de anon-zero listada.

    ![Contagem de itens de backup de Máquina Virtual do Azure dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-07.png)

3. Na página itens de backup (máquinas virtuais do Azure), sua VM **vmoracle19c** é listada. Clique nas reticências à direita para exibir o menu e selecione **recuperação de arquivo**.

    ![Captura de tela da página de recuperação de arquivos dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-08.png)

4. No painel **Recuperação de Arquivo (Versão Prévia)**, clique em **Baixar Script**. Em seguida, salve o arquivo de download (. py) em uma pasta no computador cliente. Uma senha é gerada para executar o script. Copie a senha para um arquivo para uso posterior. 

    ![Opções de salvamento do arquivo de script de download](./media/oracle-backup-recovery/recovery-service-09.png)

5. Copie o arquivo. py para a VM.

    O exemplo a seguir mostra como usar um comando de cópia segura (scp) para mover o arquivo para a VM. Você pode também copiar o conteúdo para a área de transferência e colá-lo em um novo arquivo configurado na VM.

    > [!IMPORTANT]
    > No exemplo a seguir, lembre-se de atualizar os valores de endereço IP e pasta. Os valores devem estar mapeados para a pasta na qual o arquivo foi salvo.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar pontos de recuperação para sua VM, use AZ Backup Recovery Point List. Neste exemplo, selecionamos o ponto de recuperação mais recente da VM denominada myVM que está protegido em myRecoveryServicesVault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

Para obter o script que conecta ou monta o ponto de recuperação da VM, use az backup restore files mount-rp. O exemplo a seguir obtém o script para a VM denominada myVM que está protegida em myRecoveryServicesVault.

Substitua myRecoveryPointName pelo nome do ponto de recuperação que você obteve no comando anterior:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

O script é baixado e uma senha é exibida, como no exemplo a seguir:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Copie o arquivo. py para a VM.

O exemplo a seguir mostra como usar um comando de cópia segura (scp) para mover o arquivo para a VM. Você pode também copiar o conteúdo para a área de transferência e colá-lo em um novo arquivo configurado na VM.

> [!IMPORTANT]
> No exemplo a seguir, lembre-se de atualizar os valores de endereço IP e pasta. Os valores devem estar mapeados para a pasta na qual o arquivo foi salvo.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Montar o ponto de restauração

1. Crie um ponto de montagem de restauração e copie o script nele.

    No exemplo a seguir, crie um diretório */Restore* para o instantâneo a ser montado, mova o arquivo para o diretório e altere o arquivo para que ele seja de Propriedade do usuário raiz e tenha sido executável.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Agora, execute o script para restaurar o backup. Você será solicitado a fornecer a senha gerada em portal do Azure. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    O exemplo a seguir mostra o que você deverá ver depois de executar o script anterior. Quando você for solicitado a continuar, digite **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. O acesso aos volumes montados foi confirmado.

    Para sair, digite **p** e procure os volumes montados. Para criar uma lista dos volumes adicionados, em um prompt de comando, digite **DF-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Executar recuperação

1. Copie os arquivos de backup ausentes de volta para a área de recuperação rápida:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Os comandos a seguir usam o RMAN para restaurar os arquivos ausentes e recuperar o banco de dados:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Verifique se o conteúdo do banco de dados foi totalmente recuperado:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Desmonte o ponto de restauração.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    No Portal do Azure, na folha **Recuperação de Arquivos (Versão Prévia)**, clique em **Desmontar Discos**.

    ![Comando Desmontar discos](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Você também pode desmontar os volumes de recuperação executando o script Python novamente com a opção **-Clean** .

## <a name="restore-the-entire-vm"></a>Restaurar toda a VM

Em vez de restaurar os arquivos excluídos dos cofres dos Serviços de Recuperação, você pode restaurar toda a VM.

Para restaurar toda a VM, conclua estas etapas:

1. Pare e exclua vmoracle19c.
1. Recupere a VM.
1. Defina o endereço IP público.
1. Conecte-se à VM.
1. Inicie o estágio do banco de dados para montar e execute a recuperação.

### <a name="stop-and-delete-vmoracle19c"></a>Parar e excluir vmoracle19c

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na portal do Azure, vá para a máquina virtual **vmoracle19c** e, em seguida, selecione **parar**.

1. Quando a máquina virtual não estiver mais em execução, selecione **excluir** e **Sim**.

   ![Comando Excluir cofre](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Pare e desaloque a VM:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Exclua a VM. Insira ' y ' quando solicitado:

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Recuperar a VM

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Crie uma conta de armazenamento para preparo no portal do Azure.

   1. Na portal do Azure, selecione **+ criar um recurso** e pesquise e selecione a **conta de armazenamento**.
    
      ![Captura de tela que mostra onde criar um recurso.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Na página Criar conta de armazenamento, escolha seu grupo de recursos existente **RG-Oracle**, nomeie sua conta de armazenamento **Oracrestore** e escolha **armazenamento v2 (generalpurpose v2)** para tipo de conta. Altere a replicação para **armazenamento com redundância local (LRS)** e defina o desempenho como **padrão**. Verifique se o local está definido para a mesma região que todos os outros recursos no grupo de recursos. 
    
      ![Página de adição da conta de armazenamento](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Clique em revisão + criar e, em seguida, clique em criar.

2. Na portal do Azure, pesquise o item cofres dos serviços de recuperação do *myvault* e clique nele.

    ![Itens do backup myVault dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  Na folha **visão geral** , selecione **itens de backup** e selecione a **máquina virtual do Azure**, que deve ter a contagem de itens de backup de anon-zero listada.

    ![Contagem de itens de backup de Máquina Virtual do Azure dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery-service-07.png)

4.  Nos itens de backups (máquinas virtuais do Azure), página sua VM **vmoracle19c** está listada. Clique no nome da VM.

    ![Página VM de Recuperação](./media/oracle-backup-recovery/recover-vm-02.png)

5.  Na folha **vmoracle19c** , escolha um ponto de restauração que tenha um tipo consistente de consistência de **aplicativo** e clique nas reticências (**...**) à direita para exibir o menu.  No menu, clique em **restaurar VM**.

    ![Comando Restaurar VM](./media/oracle-backup-recovery/recover-vm-03.png)

6.  Na folha **restaurar máquina virtual** , escolha **criar novo** e **criar nova máquina virtual**. Insira o nome da máquina virtual **vmoracle19c** e escolha a VNet **vmoracle19cVNET**, a sub-rede será preenchida automaticamente para você com base em sua seleção de vnet. O processo de restauração de VM requer uma conta de armazenamento do Azure no mesmo grupo de recursos e região. Você pode escolher a conta de armazenamento **orarestore** a configuração anterior.

    ![Restaurar valores de configuração](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Para restaurar a VM, clique no botão **Restaurar**.

8.  Para exibir o status do processo de restauração, clique em **Trabalhos** e em **Trabalhos de Backup**.

    ![Comando Status de trabalhos de backup](./media/oracle-backup-recovery/recover-vm-05.png)

    Clique na operação de restauração **em andamento** para mostrar o status do processo de restauração:

    ![Status do processo de restauração](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar sua conta de armazenamento e compartilhamento de arquivos, execute os seguintes comandos no CLI do Azure.

1. Crie a conta de armazenamento no mesmo grupo de recursos e no mesmo local que sua VM:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Recupere a lista de pontos de recuperação disponíveis. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Restaure o ponto de recuperação para a conta de armazenamento. Substitua `<myRecoveryPointName>` por um ponto de recuperação da lista gerada na etapa anterior:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Recupere os detalhes do trabalho de restauração. O comando a seguir obtém mais detalhes para o trabalho restaurado disparado, incluindo seu nome, que é necessário para recuperar o URI do modelo. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   A saída terá uma aparência semelhante a esta `(Note down the name of the restore job)` :

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Recupere os detalhes do URI a ser usado para recriar a VM. Substitua o nome do trabalho de restauração da etapa anterior para `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   A saída é semelhante a esta:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   O nome do modelo, que está no final do URI do blob de modelo, que neste exemplo é `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` , e o nome do contêiner de BLOB, que está `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` listado. 

   Use esses valores no comando a seguir para atribuir variáveis em preparação para criar a VM. Uma chave SAS é gerada para o contêiner de armazenamento com duração de 30 minutos.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Agora, implante o modelo para criar a VM.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Você será solicitado a fornecer um nome para a VM.

---

### <a name="set-the-public-ip-address"></a>Definir o endereço IP público

Depois que a VM for restaurada, você deverá reatribuir o endereço IP original para a nova VM.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Na portal do Azure, acesse sua máquina virtual **vmoracle19c**. Você notará que foi atribuído um novo IP público e uma NIC semelhante a vmoracle19c-NIC-XXXXXXXXXXXX, mas não tem um endereço DNS. Quando a VM original foi excluída, seu IP público e sua NIC são mantidos e as próximas etapas as reanexarão à nova VM.

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create-ip-01.png)

2.  Pare a VM.

    ![Criar endereço IP](./media/oracle-backup-recovery/create-ip-02.png)

3.  Ir para **rede**

    ![Associar o endereço IP](./media/oracle-backup-recovery/create-ip-03.png)

4.  Clique em **anexar interface de rede**, escolha o NIC original * * vmoracle19cVMNic, ao qual o endereço IP público original ainda está associado e clique em **OK**

    ![Selecione o tipo de recurso e os valores da NIC](./media/oracle-backup-recovery/create-ip-04.png)

5.  Agora você deve desanexar a NIC que foi criada com a operação de restauração da VM, pois ela está configurada como a interface primária. Clique em **desanexar interface de rede** e escolha a nova NIC semelhante a **vmoracle19c-NIC-xxxxxxxxxxxx** e clique em **OK**

    ![Captura de tela que mostra onde selecionar desanexar interface de rede.](./media/oracle-backup-recovery/create-ip-05.png)
    
    Sua VM recriada agora terá o NIC original, que está associado ao endereço IP original e às regras do grupo de segurança de rede
    
    ![Valor do endereço IP](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Volte para a **visão geral** e clique em **Iniciar** 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Pare e desaloque a VM:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Listar o atual, restaurar NIC de VM gerada

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   A saída terá uma aparência semelhante a esta, que lista o nome da NIC gerada para restauração como `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. Anexe a NIC original, que deve ter um nome `<VMName>VMNic` , nesse caso `vmoracle19cVMNic` . O endereço IP público original ainda está anexado a essa NIC e será restaurado para a VM quando a NIC for reanexada. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Desanexar a NIC gerada pela restauração

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Inicie a VM:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Conectar-se à VM

Use o script a seguir para se conectar à VM:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Iniciar o estágio do banco de dados para montar e executar a recuperação

1. Você pode achar que a instância está sendo executada, pois o início automático tentou iniciar o banco de dados na inicialização da VM. No entanto, o banco de dados requer recuperação e provavelmente estará somente no estágio de montagem; portanto, um desligamento preparatória é executado primeiro.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Verifique se o conteúdo do banco de dados foi recuperado:

    ```bash
    SQL> select * from scott.scott_table;
    ```

O backup e a recuperação do banco de dados do Oracle Database 19C em uma VM Linux do Azure agora estão concluídos.

## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, use o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)