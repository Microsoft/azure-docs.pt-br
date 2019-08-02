---
title: Fazer backup e recuperar um banco de dados Oracle Database 12c em uma máquina virtual Linux do Azure | Microsoft Docs
description: Saiba como fazer backup e recuperar um banco de dados Oracle Database 12c em seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 461f6127111e745fe4a81958aaa225ed1dc4392a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707712"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Fazer backup e recuperar um banco de dados Oracle Database 12c em uma máquina virtual Linux do Azure

Você pode usar a CLI do Azure para criar e gerenciar recursos do Azure em um prompt de comando, ou usar scripts. Neste artigo, usamos scripts da CLI do Azure para implantar um banco de dados Oracle Database 12c de uma imagem da galeria do Azure Marketplace.

Antes de começar, verifique se a CLI do Azure está instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Preparar o ambiente

### <a name="step-1-prerequisites"></a>Etapa 1: Pré-requisitos

*   Para executar o processo de backup e recuperação, você precisa primeiro criar uma VM Linux com uma instância do Bando de Dados Oracle 12c instalada. A imagem do Marketplace usada para criar a VM chama-se *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Para saber como criar um banco de dados Oracle, consulte [Guia rápido de criação do banco de dados Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Etapa 2: Conectar-se à VM

*   Para criar uma sessão SSH (Secure Shell) com a VM, use o comando a seguir. Substitua a combinação de endereço IP e nome do host pelo valor `publicIpAddress` para sua VM.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Etapa 3: Preparar o banco de dados

1.  Esta etapa pressupõe que você tem uma instância do Oracle (cdb1) que é executada em uma VM chamada *myVM*.

    Execute a raiz do superusuário *oracle* e inicialize o ouvinte:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Opcional) Verifique se o banco de dados está no modo de log de arquivo morto:

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (Opcional) Crie uma tabela para testar a confirmação:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Verifique ou altere o tamanho e o local do arquivo de backup:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Use o RMAN (Oracle Recovery Manager) para fazer backup do banco de dados:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Etapa 4: Backup consistente com aplicativo para VMs do Linux

Os backups consistentes com aplicativo são um novo recurso do Backup do Azure. Você pode criar e selecione os scripts para serem executados antes e depois do instantâneo da VM (pré-instantâneo e pós-instantâneo).

1. Baixe o arquivo JSON.

    Baixar VMSnapshotScriptPluginConfig.json do https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. O conteúdo do arquivo deve ser semelhante ao seguinte:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Crie a pasta /etc/azure na VM:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copie o arquivo JSON.

    Copie VMSnapshotScriptPluginConfig.json para a pasta /etc/azure.

4. Edite o arquivo JSON.

    Edite o arquivo VMSnapshotScriptPluginConfig.json para incluir os parâmetros `PreScriptLocation` e `PostScriptlocation`. Por exemplo:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Crie os arquivos de script pré-instantâneo e pós-instantâneo.

    Veja este exemplo de scripts de pré-instantâneo e pós-instantâneo para um “backup estático” (um backup offline, com desligamento e reinício):

    Para /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Veja este exemplo de scripts pré-instantâneo e pós-instantâneo para um “backup dinâmico” (um backup online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Para /etc/azure/pre_script.sql, modifique o conteúdo do arquivo de acordo com seus requisitos:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Para /etc/azure/post_script.sql, modifique o conteúdo do arquivo de acordo com seus requisitos:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Altere as permissões de arquivo:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Teste os scripts.

    Para testar os scripts, primeiro entre como raiz. Em seguida, verifique se não há erros:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Para saber mais, consulte [Backup consistente com aplicativo para VMs Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Etapa 5: Cofres dos serviços de recuperação do uso do Azure para fazer backup da VM

1.  No Portal do Azure, pesquise por **Cofres dos Serviços de Recuperação**.

    ![Página Cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na folha **Cofres dos Serviços de Recuperação**, adicione um novo cofre e clique em **Adicionar**.

    ![Página para adicionar cofres aos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Para continuar, clique em **myVault**.

    ![Página de detalhes de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na folha **myVault**, clique em **Backup**.

    ![Página de backup dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na folha **Meta de Backup**, use os valores padrão de **Azure** e **Máquina virtual**. Clique em **OK**.

    ![Página de detalhes de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Para **Política de backup**, use **DefaultPolicy** ou selecione **Criar Nova política**. Clique em **OK**.

    ![Página de detalhes da política de backup dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na folha **Selecionar máquinas virtuais**, marque a caixa de seleção **myVM1** e clique em **OK**. Clique no botão **Habilitar backup**.

    ![Página de detalhes de itens a serem incluídos no backup dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Depois de clicar em **Habilitar backup**, o processo de backup não iniciará até horário agendado expirar. Para configurar um backup imediato, conclua a próxima etapa.

8.  Na folha **myVault – Itens de backup**, em **CONTAGEM DE ITENS DE BACKUP**, selecione a contagem de itens de backup.

    ![Página de detalhes myVault de cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na folha **Itens de Backup (Máquina Virtual do Azure)** , do lado direito da página, clique no botão de reticências ( **...** ) e clique em **Fazer backup agora**.

    ![Comando Fazer backup agora dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_09.png)

10. Clique no botão **Backup**. Aguarde o processo de backup ser concluído. Em seguida, vá para [etapa 6: Remover os arquivos de banco de dados](#step-6-remove-the-database-files).

    Para exibir o status do trabalho de backup, clique em **Trabalhos**.

    ![Página de trabalho dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_10.png)

    O status do trabalho de backup é exibido na imagem a seguir:

    ![Página de trabalho dos cofres dos Serviços de Recuperação com status](./media/oracle-backup-recovery/recovery_service_11.png)

11. Para um backup consistente com aplicativo, resolva os possíveis erros no arquivo de log. O arquivo de log encontra-se em /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Etapa 6: Remover os arquivos de banco de dados 
Posteriormente neste artigo, você aprenderá a testar o processo de recuperação. Antes de testar o processo de recuperação, você precisará remover os arquivos de banco de dados.

1.  Remova os arquivos de backup e o espaço de tabela:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Opcional) Desligue a instância do Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Restaurar os arquivos excluídos de cofres dos Serviços de Recuperação
Para restaurar os arquivos excluídos, conclua o as etapas a seguir:

1. Entre no Portal do Azure e pesquise o item *myVault* dos cofres dos Serviços de Recuperação. Na folha **Visão Geral**, em **Fazer backup de itens**, selecione o número de itens.

    ![Itens do backup myVault dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_12.png)

2. Em **CONTAGEM DE ITENS DE BACKUP**, selecione o número de itens.

    ![Contagem de itens de backup de Máquina Virtual do Azure dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na folha **myvm1**, clique em **Recuperação de Arquivo (Versão Prévia)** .

    ![Captura de tela da página de recuperação de arquivos dos cofres dos Serviços de Recuperação](./media/oracle-backup-recovery/recovery_service_14.png)

4. No painel **Recuperação de Arquivo (Versão Prévia)** , clique em **Baixar Script**. Em seguida, salve o arquivo de download (.sh) em uma pasta no computador cliente.

    ![Opções de salvamento do arquivo de script de download](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copie o arquivo .sh para a VM.

    O exemplo a seguir mostra como usar um comando de cópia segura (scp) para mover o arquivo para a VM. Você pode também copiar o conteúdo para a área de transferência e colá-lo em um novo arquivo configurado na VM.

    > [!IMPORTANT]
    > No exemplo a seguir, lembre-se de atualizar os valores de endereço IP e pasta. Os valores devem estar mapeados para a pasta na qual o arquivo foi salvo.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Altere o arquivo para ele que pertença à raiz.

    No exemplo a seguir, altere o arquivo para que ele pertença à raiz. Em seguida, altere as permissões.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    O exemplo a seguir mostra o que você deverá ver depois de executar o script anterior. Quando você for solicitado a continuar, digite **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. O acesso aos volumes montados foi confirmado.

    Para sair, digite **p**e procure os volumes montados. Para criar uma lista de volumes adicionados, no prompt de comando, digite **df -k**.

    ![O comando df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Use o script a seguir para copiar os arquivos ausentes para as pastas:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. No script a seguir, use RMAN para recuperar o banco de dados:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Desmonte o disco.

    No Portal do Azure, na folha **Recuperação de Arquivos (Versão Prévia)** , clique em **Desmontar Discos**.

    ![Comando Desmontar discos](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restaurar toda a VM

Em vez de restaurar os arquivos excluídos dos cofres dos Serviços de Recuperação, você pode restaurar toda a VM.

### <a name="step-1-delete-myvm"></a>Etapa 1: Excluir myVM

*   No Portal do Azure, acesse o cofre **myVM1** e selecione **Excluir**.

    ![Comando Excluir cofre](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Etapa 2: Recupere a VM

1.  Vá para os **cofres dos Serviços de Recuperação**e selecione **myVault**.

    ![Entrada de myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na folha **Visão Geral**, em **Fazer backup de itens**, selecione o número de itens.

    ![Itens de backup de myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na folha **Itens de Backup (Máquina Virtual do Azure)** , selecione **myvm1**.

    ![Página VM de Recuperação](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na folha **myvm1**, clique no botão de reticências ( **...** ) e, em seguida, clique em **Restaurar VM**.

    ![Comando Restaurar VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na folha **Selecionar ponto de restauração**, selecione o item que você deseja restaurar e clique em **OK**.

    ![Selecionar o ponto de restauração](./media/oracle-backup-recovery/recover_vm_06.png)

    Se você tiver habilitado o backup consistente com aplicativo, uma barra azul vertical será exibida.

6.  Na folha **Restaurar configuração**, selecione o nome da máquina virtual, o grupo de recursos e clique em **OK**.

    ![Restaurar valores de configuração](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Para restaurar a VM, clique no botão **Restaurar**.

8.  Para exibir o status do processo de restauração, clique em **Trabalhos** e em **Trabalhos de Backup**.

    ![Comando Status de trabalhos de backup](./media/oracle-backup-recovery/recover_vm_08.png)

    A figura a seguir mostra o status do processo de restauração:

    ![Status do processo de restauração](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Etapa 3: Defina o endereço IP público
Depois que a VM for restaurada, configure o endereço IP público.

1.  Na caixa de pesquisa, digite **endereços IP públicos**.

    ![Lista de endereços IP públicos](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na folha **Endereços IP públicos**, clique em **Adicionar**. Na folha **Criar endereço IP público**, selecione o nome do IP público para **Nome**. Em **Grupo de recursos**, marque **Usar existente**. Em seguida, clique em **Criar**.

    ![Criar endereço IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Para associar o endereço IP público ao adaptador de rede para a VM, pesquise e selecione **myVMip**. Em seguida, clique em **Associar**.

    ![Associar o endereço IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Para **Tipo de recurso**, selecione **Adaptador de rede**. Selecione o adaptador de rede usado pela instância myVM e clique em **OK**.

    ![Selecione o tipo de recurso e os valores da NIC](./media/oracle-backup-recovery/create_ip_03.png)

5.  Pesquise e abra a instância de myVM trazida do portal. O endereço IP que está associado à VM aparece na folha **Visão Geral** de myVM.

    ![Valor do endereço IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Etapa 4: Conectar-se à VM

*   Use o script a seguir para se conectar à VM:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Etapa 5: Testar se o banco de dados está acessível
*   Use o script a seguir para testar a acessibilidade:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Se o banco de dados **inicialização** comando gera um erro, para recuperar o banco de dados, consulte [etapa 6: Use RMAN para recuperar o banco de dados](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Etapa 6: (Opcional) Use RMAN para recuperar o banco de dados
*   Use o script a seguir para recuperar o banco de dados:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

O backup e a recuperação do banco de dados do Oracle Database 12c em uma VM Linux do Azure foram concluídos.

## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, use o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](../../linux/cli-samples.md)



