---
title: Fazer backup de um banco de dados Oracle Database 19C em uma VM Linux do Azure com o RMAN e o armazenamento do Azure
description: Saiba como fazer backup de um banco de dados Oracle Database 19C no armazenamento em nuvem do Azure.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670014"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Fazer backup e recuperar um banco de dados Oracle Database 19C em uma VM Linux do Azure usando o armazenamento do Azure

Este artigo demonstra o uso do armazenamento do Azure como uma mídia para fazer backup e restaurar um banco de dados Oracle em execução em uma VM do Azure. Você fará o backup do banco de dados usando o Oracle RMAN para o armazenamento de arquivos do Azure montado na VM usando o protocolo SMB. Usar o armazenamento do Azure para mídia de backup é extremamente econômico e de alto desempenho. No entanto, para bancos de dados muito grandes, o backup do Azure fornece uma solução melhor.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Para executar o processo de backup e recuperação, você deve primeiro criar uma VM do Linux que tenha uma instância instalada do Oracle Database 19C. A imagem do Marketplace usada atualmente para criar a VM é  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: Latest**. Siga as etapas no guia de [início rápido do Oracle CREATE DATABASE](./oracle-database-quick-create.md) para criar um banco de dados Oracle para concluir este tutorial.

## <a name="prepare-the-database-environment"></a>Preparar o ambiente de banco de dados

1. Para criar uma sessão SSH (Secure Shell) com a VM, use o comando a seguir. Substitua a combinação de endereço IP e nome do host pelo valor `publicIpAddress` para sua VM.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Alternar para o usuário ***raiz*** :
 
   ```bash
   sudo su -
   ```
    
3. Adicione o usuário Oracle ao arquivo ***/etc/sudoers*** :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Esta etapa pressupõe que você tenha uma instância do Oracle (teste) em execução em uma VM chamada *vmoracle19c*.

   Alternar usuário para o usuário *Oracle* :

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
    
6. Inicie o ouvinte Oracle se ele ainda não estiver em execução:
    
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

7.  Crie o local da FRA (área de recuperação rápida):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Conecte-se ao banco de dados:

    ```bash
    sqlplus / as sysdba
    ```

9.  Inicie o banco de dados se ele ainda não estiver em execução:

    ```bash
    SQL> startup
    ```

10. Definir variáveis de ambiente de banco de dados para a área de recuperação rápida:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Verifique se o banco de dados está no modo de log de arquivo morto para habilitar backups online.
    Verifique o status do arquivo de log primeiro:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    E se estiver no modo NOARCHIVELOG, execute os seguintes comandos em sqlplus:

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

## <a name="back-up-to-azure-files"></a>Fazer backup em arquivos do Azure

Para fazer backup em arquivos do Azure, conclua estas etapas:

1. Configure o armazenamento de arquivos do Azure.
1. Monte o compartilhamento de arquivos do armazenamento do Azure para sua VM.
1. Fazer backup do banco de dados.
1. Restaure e recupere o banco de dados.

### <a name="set-up-azure-file-storage"></a>Configurar o armazenamento de arquivos do Azure

Nesta etapa, você fará o backup do banco de dados Oracle usando o RMAN (Oracle Recovery Manager) para o armazenamento de arquivos do Azure. Os compartilhamentos de arquivos do Azure são compartilhamentos de arquivos totalmente gerenciados que residem na nuvem. Eles podem ser acessados usando o protocolo SMB ou o protocolo NFS (sistema de arquivos de rede). Esta etapa aborda a criação de um compartilhamento de arquivos que usa o protocolo SMB para montar a sua VM. Para obter informações sobre como montar usando o NFS, consulte [montar o armazenamento de BLOBs usando o protocolo nfs 3,0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Ao montar os arquivos do Azure, usaremos o `cache=none` para desabilitar o cache de dados de compartilhamento de arquivos. E para garantir que os arquivos criados no compartilhamento sejam de Propriedade do usuário Oracle, defina as `uid=oracle` `gid=oinstall` Opções e também. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Primeiro, configure sua conta de armazenamento.

1. Configurar o armazenamento de arquivos no portal do Azure

    Na portal do Azure, selecione ***+ criar um recurso** _ e pesquise e selecione _ conta de *_armazenamento_**
    
    ![Captura de tela que mostra onde criar um recurso e selecionar conta de armazenamento.](./media/oracle-backup-recovery/storage-1.png)
    
2. Na página Criar conta de armazenamento, escolha o grupo de recursos existente ***RG-Oracle** _, nomeie sua conta de armazenamento _*_Oracbkup1_*_ e escolha _*_armazenamento v2 (generalpurpose v2)_*_ para tipo de conta. Altere a replicação para _*_armazenamento com redundância local (LRS)_*_ e defina o desempenho como _ *_Standard_* *. Verifique se o local está definido para a mesma região que todos os outros recursos no grupo de recursos. 
    
    ![Captura de tela que mostra onde escolher o grupo de recursos existente.](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Clique na guia ***avançado** _ e em arquivos do Azure definir _*_compartilhamentos de arquivos grandes_*_ como _ *_habilitado_* *. Clique em revisão + criar e, em seguida, clique em criar.
    
    ![Captura de tela que mostra onde definir compartilhamentos de arquivos grandes como habilitados.](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Quando a conta de armazenamento tiver sido criada, vá para o recurso e escolha ***compartilhamentos de arquivos***
    
    ![Captura de tela que mostra onde selecionar compartilhamentos de arquivos.](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Clique em ***+ compartilhamento de arquivos** _ e, na folha _*_novo compartilhamento de arquivo_*_ , nomeie o compartilhamento de arquivos _*_orabkup1_*_. Defina a _*_cota_*_ como _*_10240_*_ GiB e verifique a _*_transação otimizada_*_ como a camada. A cota reflete um limite superior para o qual o compartilhamento de arquivos pode crescer. Como estamos usando o armazenamento padrão, os recursos são PAYG e não provisionados, portanto, defini-lo como 10 TiB não incorrerá em custos além do que você usa. Se sua estratégia de backup exigir mais armazenamento, você deverá definir a cota como um nível apropriado para manter todos os backups.   Quando você tiver concluído a folha novo compartilhamento de arquivos, clique em _ *_criar_* *.
    
    ![Captura de tela que mostra onde adicionar um novo compartilhamento de arquivos.](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Quando criado, clique em ***orabkup1*** na página Configurações de compartilhamento de arquivos. 
    Clique na guia ***Connect** _ para abrir a folha conectar e, em seguida, clique na guia _ *_Linux_**. Copie os comandos fornecidos para montar o compartilhamento de arquivos usando o protocolo SMB. 
    
    ![Página de adição da conta de armazenamento](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a conta de armazenamento e o compartilhamento de arquivos, execute os seguintes comandos no CLI do Azure.

1. Crie a conta de armazenamento no mesmo grupo de recursos e no mesmo local que sua VM:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. Crie um compartilhamento de arquivos na conta de armazenamento com uma cota de 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Recupere a chave primária da conta de armazenamento (key1) que é necessária ao montar o compartilhamento de arquivos em sua VM:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Montar o compartilhamento de arquivos do armazenamento do Azure para sua VM

1. Crie o ponto de montagem:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Configurar credenciais:

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

4. Adicione a montagem ao/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Execute os comandos para montar o armazenamento de arquivos do Azure usando o protocolo SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Se você receber um erro semelhante a:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   em seguida, o pacote CIFS pode não estar instalado no seu host Linux. 
   
   Para verificar se o CIS está instalado, execute o seguinte comando:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Se o comando não retornar nenhuma saída, instale o pacote CIFS usando o seguinte comando:

   ```bash 
   sudo yum install cifs-utils
   ```

   Agora, execute novamente o comando Mount acima para montar o armazenamento de arquivos do Azure.

6. Verifique se o compartilhamento de arquivos está montado corretamente com o seguinte comando:

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

Nesta seção, usaremos o RMAN (Oracle Recovery Manager) para fazer um backup completo do banco de dados e arquivar logs e gravar o backup como um conjunto de backup para o compartilhamento de arquivos do Azure montado anteriormente. 

1. Configure o RMAN para fazer backup para o ponto de montagem dos arquivos do Azure:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Como os compartilhamentos de arquivos padrão do Azure têm um tamanho de arquivo máximo de 1 TiB, limitaremos o tamanho das peças de backup do RMAN para 1 TiB. (Observe que os compartilhamentos de arquivos Premium têm um limite máximo de tamanho de arquivo de 4 TiB. Para obter mais informações, consulte [escalabilidade e metas de desempenho dos arquivos do Azure](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Confirme os detalhes da alteração de configuração:

    ```bash
    RMAN> show all;
    ```

4. Agora, execute o backup. O comando a seguir usará um backup de banco de dados completo, incluindo arquivos de log de arquivo, como um conjunto de backup no formato compactado:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Agora você fez backup do banco de dados online usando o Oracle RMAN, com o backup localizado no armazenamento de arquivos do Azure. Esse método tem a vantagem de utilizar os recursos do RMAN ao armazenar backups no armazenamento de arquivos do Azure, onde eles podem ser acessados de outras VMs, úteis se você precisar clonar o banco de dados.  
    
Embora o uso do RMAN e do armazenamento de arquivos do Azure para o backup de banco de dados tenha muitas vantagens, o tempo de backup e restauração é vinculado ao tamanho do banco de dados, para que, para os bancos de dados muito grandes, ele possa levar um tempo considerável para essas operações.  Um mecanismo de backup alternativo, usando backups de VM consistentes com aplicativos de backup do Azure, usa a tecnologia de instantâneo para executar backups, que tem a vantagem de backups muito rápidos, independentemente do tamanho do banco de dados. A integração com o cofre dos serviços de recuperação fornece armazenamento seguro de seus backups de Oracle Database no armazenamento em nuvem do Azure acessível de outras VMs e regiões do Azure. 

### <a name="restore-and-recover-the-database"></a>Restaurar e recuperar o banco de dados

1.  Desligue a instância do Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Remova os arquivos e os backups:

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. Os comandos a seguir usam o RMAN para restaurar os arquivos ausentes e recuperar o banco de dados:

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


O backup e a recuperação do banco de dados do Oracle Database 19C em uma VM Linux do Azure agora estão concluídos.

## <a name="delete-the-vm"></a>Excluir a VM

Quando a VM não for mais necessária, use o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial: criar VMs altamente disponíveis](../../linux/create-cli-complete.md)

[Explorar exemplos da CLI do Azure de implantação de VM](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)