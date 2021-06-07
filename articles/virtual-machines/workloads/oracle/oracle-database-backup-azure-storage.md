---
title: Fazer backup de um banco de dados Oracle Database 19c em uma VM Linux do Azure com o RMAN e o Armazenamento do Microsoft Azure
description: Saiba como fazer backup de um banco de dados Oracle Database 19c para o armazenamento em nuvem do Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670014"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Fazer backup e recuperar um banco de dados Oracle Database 19c em uma VM Linux do Azure usando o Armazenamento do Microsoft Azure

Este artigo demonstra como usar o Armazenamento do Microsoft Azure como uma mídia para fazer backup e restaurar um banco de dados Oracle em execução em uma VM do Azure. Você fará o backup do banco de dados usando o Oracle RMAN para o Armazenamento de Arquivos do Azure montado na VM usando o protocolo SMB. Usar o armazenamento do Azure como mídia de backup é extremamente econômico e fornece alto desempenho. No entanto, para bancos de dados muito grandes, o Backup do Azure fornece uma solução melhor.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar o processo de backup e recuperação, você precisa primeiro criar uma VM Linux com uma instância do Oracle Database 19c instalada. A imagem do Marketplace usada atualmente para criar a VM é **Oracle:oracle-database-19-3:oracle-database-19-0904:latest**. Siga as etapas no [Guia de início rápido para criação de banco de dados Oracle](./oracle-database-quick-create.md) para criar um banco de dados Oracle e concluir este tutorial.

## <a name="prepare-the-database-environment"></a>Preparar o ambiente do banco de dados

1. Para criar uma sessão SSH (Secure Shell) com a VM, use o comando a seguir. Substitua a combinação de endereço IP e nome do host pelo valor `publicIpAddress` para sua VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Mude para o usuário ***raiz***:
 
   ```bash
   sudo su -
   ```
    
3. Adicione o usuário Oracle ao arquivo ***/etc/sudoers***:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Esta etapa pressupõe que você tem uma instância do Oracle (teste) em execução em uma VM chamada *vmoracle19c*.

   Mude para o usuário *oracle*:

   ```bash
   sudo su - oracle
   ```
    
5. Antes de se conectar, você precisa definir a variável de ambiente ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Você também deve adicionar a variável ORACLE_SID ao arquivo `.bashrc` dos usuários `oracle` para entradas futuras usando o seguinte comando:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Inicie o ouvinte do Oracle se ele ainda não estiver sendo executado:
    
   ```bash
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

7.  Crie a localização da FRA (Área de recuperação rápida):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Conecte-se ao banco de dados:

    ```bash
    sqlplus / as sysdba
    ```

9.  Inicie o banco de dados se ainda não estiver em execução:

    ```bash
    SQL> startup
    ```

10. Defina as variáveis do ambiente de banco de dados para a área de recuperação rápida:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Verifique se o banco de dados está no modo de log de arquivos para habilitar backups online.
    Verifique o status do arquivo de log primeiro:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Se estiver no modo NOARCHIVELOG, execute os seguintes comandos em sqlplus:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Crie uma tabela para testar as operações de backup e restauração:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Fazer backup nos Arquivos do Azure

Para fazer backup nos Arquivos do Azure, conclua estas etapas:

1. Configure o Armazenamento de Arquivos do Azure.
1. Monte o compartilhamento de arquivo do Armazenamento do Microsoft Azure para sua VM.
1. Fazer backup do banco de dados.
1. Restaure e recupere o banco de dados.

### <a name="set-up-azure-file-storage"></a>Configurar o Armazenamento de Arquivos do Azure

Nesta etapa, você fará backup do banco de dados Oracle usando o Oracle RMAN (Recovery Manager) para o armazenamento de Arquivos do Azure. Os compartilhamentos de arquivos do Azure são totalmente gerenciados e residem na nuvem. Eles podem ser acessados usando o protocolo SMB ou o protocolo NFS (Network File System). Esta etapa descreve a criação de um compartilhamento de arquivo que usa o protocolo SMB para montar em sua VM. Para obter informações sobre como montar usando o NFS, confira [Montar o Armazenamento de Blobs usando o protocolo NFS 3.0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Ao montar os Arquivos do Azure, usaremos o `cache=none` para desabilitar o cache de dados de compartilhamento de arquivo. Para garantir que os arquivos criados no compartilhamento sejam de propriedade do usuário Oracle, defina também as opções `uid=oracle` e `gid=oinstall`. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Primeiro, configure a conta de armazenamento.

1. Configurar o Armazenamento de Arquivos no portal do Azure

    No portal do Azure, selecione * **+ Criar um recurso** _ e pesquise e selecione _ *_Conta de Armazenamento_**
    
    ![Captura de tela que mostra onde criar um recurso e selecionar a Conta de Armazenamento.](./media/oracle-backup-recovery/storage-1.png)
    
2. Na página Criar conta de armazenamento, escolha o grupo de recursos existente ***rg-oracle** _, dê à conta de armazenamento o nome _*_oracbkup1_*_ e escolha _*_Armazenamento v2 (generalpurpose v2)_*_ como tipo de conta. Altere a replicação para _*_Armazenamento com redundância local (LRS)_*_ e defina o desempenho como _*_Standard_**. Verifique se a localização está definida como a mesma região que todos os outros recursos no grupo de recursos. 
    
    ![Captura de tela que mostra onde escolher o grupo de recursos existente.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Clique na guia ***Avançado** _ e, em Arquivos do Azure, defina _*_Compartilhamentos de arquivos grandes_*_ como _*_Habilitado_**. Clique em Examinar + criar e clique em Criar.
    
    ![Captura de tela que mostra onde definir Compartilhamentos de arquivos grandes como Habilitado.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Quando a conta de armazenamento tiver sido criada, vá para o recurso e escolha ***Compartilhamentos de arquivos***
    
    ![Captura de tela que mostra onde selecionar Compartilhamentos de arquivos.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Clique em * **+ Compartilhamento de arquivo** _ e, na folha _*_Novo compartilhamento de arquivo_*_, dê ao compartilhamento de arquivo o nome _*_orabkup1_*_. Defina a _*_Cota_*_ como _*_10240_*_ GiB e marque _*_Transação otimizada_*_ como a camada. A cota reflete um limite superior até o qual o compartilhamento de arquivo pode crescer. Como estamos usando o Armazenamento Standard, os recursos são PAYG e não provisionados, de modo que os definir como 10 TiB não incorrerá em custos além do que você usa. Se a sua estratégia de backup exigir mais armazenamento, defina a cota como um nível apropriado para manter todos os backups.   Quando você tiver concluído a folha Novo compartilhamento de arquivo, clique em _*_Criar_**.
    
    ![Captura de tela que mostra onde adicionar um novo compartilhamento de arquivo.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Após a criação, clique em ***orabkup1*** na página Configurações do compartilhamento de arquivo. 
    Clique na guia ***Conectar** _ para abrir a folha Conectar e clique na guia _ *_Linux_**. Copie os comandos fornecidos para montar o compartilhamento de arquivo usando o protocolo SMB. 
    
    ![Página de adição da conta de armazenamento](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a conta de armazenamento e o compartilhamento de arquivo, execute os comandos a seguir na CLI do Azure.

1. Crie a conta de armazenamento no mesmo grupo de recursos e localização que sua VM:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Crie o compartilhamento de arquivo na conta de armazenamento com uma cota de 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Recupere a chave primária da conta de armazenamento (key1) que é necessária ao montar o compartilhamento de arquivo em sua VM:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Montar o compartilhamento de arquivo do Armazenamento do Microsoft Azure para sua VM

1. Crie o ponto de montagem:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Configure as credenciais:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   Substitua `<Your Storage Account Key1>` pela chave da conta de armazenamento recuperada anteriormente antes de executar o seguinte comando:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Altere as permissões no arquivo de credenciais:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Adicione a montagem ao /etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Execute os comandos para montar o armazenamento de Arquivos do Azure usando o protocolo SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Se você receber um erro semelhante a:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   O pacote CIFS pode não estar instalado em seu host Linux. 
   
   Para verificar se o CIS está instalado, execute o seguinte comando:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Se o comando não retornar nenhuma saída, instale o pacote CIFS usando o seguinte comando:

   ```bash 
   sudo yum install cifs-utils
   ```

   Agora, execute novamente o comando de montagem acima para montar o armazenamento de Arquivos do Azure.

6. Verifique se o compartilhamento de arquivo está montado corretamente com o seguinte comando:

   ```bash
   df -h
   ```

   A saída deverá ser semelhante ao seguinte:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Fazer backup do banco de dados

Nesta seção, usaremos o Oracle RMAN (Recovery Manager) para fazer um backup completo do banco de dados e logs de arquivos e gravar o backup como um conjunto de backup para o compartilhamento de arquivo do Azure montado anteriormente. 

1. Configure o RMAN para fazer backup para o ponto de montagem dos Arquivos do Azure:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Como os compartilhamentos de arquivo padrão do Azure têm um tamanho de arquivo máximo de 1 TiB, limitaremos o tamanho das partes do backup do RMAN a 1 TiB. (Observe que compartilhamentos de arquivo Premium têm um limite de tamanho de arquivo máximo de 4 TiB. Para obter mais informações, confira [Metas de desempenho e escalabilidade dos Arquivos do Azure](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confirme os detalhes da alteração de configuração:

    ```bash
    RMAN> show all;
    ```

4. Agora, execute o backup. O seguinte comando fará um backup de banco de dados completo, incluindo os arquivos de log de arquivo, como um conjunto de backup no formato compactado:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Você fez backup do banco de dados online usando o Oracle RMAN com o backup localizado no armazenamento de Arquivos do Azure. Esse método tem a vantagem de utilizar os recursos do RMAN enquanto armazena os backups no armazenamento de Arquivos do Azure, onde eles podem ser acessados de outras VMs, o que é útil se você precisa clonar o banco de dados.  
    
Embora o uso do RMAN e do armazenamento de Arquivos do Azure para backup de banco de dados tenha muitas vantagens, o tempo de backup e restauração é vinculado ao tamanho do banco de dados, de modo que, para bancos de dados muito grandes, essas operações podem levar um tempo considerável.  Um mecanismo de backup alternativo, usando backups de VM consistentes com o aplicativo Backup do Azure, usa a tecnologia de instantâneo para executar backups, o que tem a vantagem de fazer backups muito rápidos, independentemente do tamanho do banco de dados. A integração com o cofre dos Serviços de Recuperação fornece armazenamento seguro de seus backups do Oracle Database no armazenamento em nuvem do Azure acessível de outras VMs e regiões do Azure. 

### <a name="restore-and-recover-the-database"></a>Restaurar e recuperar o banco de dados

1.  Desligue a instância do Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Remova os arquivos de dados e os backups:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Os seguintes comandos usam o RMAN para restaurar os arquivos de dados ausentes e recuperar o banco de dados:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Verifique se o conteúdo do banco de dados foi totalmente recuperado:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


O backup e a recuperação do banco de dados Oracle Database 19c em uma VM Linux do Azure foram concluídos.

## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, use o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)